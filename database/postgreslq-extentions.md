# PostgreSLQ extentions

* 1
* 1
* 1
* 1
* 1

The **PostgreSQL extensions** are supplemental SQL objects included with the **installation files**. They introduce **new functions**, data types, and operations without modifying the core **database engine**.

The **pg\_stat\_statements** extension has a minimal memory footprint. It isn't enabled by default and it must be explicitly configured in the **postgresql.conf** file.

1

```sql
-- We un-comment some lines from the PostgreSQL file
C:\Program Files\PostgreSQL\14\data\postgresql.conf
#session_preload_libraries = ''
shared_preload_libraries = 'pg_stat_statements'		# (change requires restart)
pg_stat_statements.max = 10000
```

1

We **restart** the postgresql **background service** to create the necessary memory section:\
`windows_Key+R -> services.msc -> postgresql-x64-17 - PostgreSQL Server 17.`

1

The pg\_stat\_statements extension consists of a **C-compiled file** (.dll or .so) stored in the PostgreSQL library directory. It gets **registered** in the memory and it allocates a dedicated block of shared memory separate from shared\_buffers.                                                                                                                 The CREATE EXTENTION command **registers** its wrapper functions and view relation in the system catalog.

The pg\_stat\_statements collects the **performance metrics** at the start and finish of the query execution. It accumulates the statistics for each **normalized SQL statement**. It's not part of the query planner pg\_statistics; it represents the runtime execution and it's queried directly from the pg\_stat\_statement view.

1

```sql
-- We create a table of 200k rows, with 200n values being repeated in the id column
drop table bloom_test;
CREATE TABLE bloom_test ( id uuid, padding text );

INSERT INTO bloom_test
SELECT md5((i/200)::text)::uuid, md5(i::text) FROM generate_series(1,200000) s(i);

-- The shared_buffer cache data is discarded during server restarts
-- The extention functions push the data to disk (pg_stat_statements.stat) on restart 
-- We need to use the reset() function to clean the extention mmeory section
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;
SELECT pg_stat_statements_reset();

-- A SELECT command in the PL/pgSQL function requires a variable SELECT * INTO ... FROM ...
-- The PERFORM command mantains the query execution without needing to store the results
CREATE OR REPLACE FUNCTION test_query(num_iters INTEGER)
RETURNS VOID AS $$
DECLARE i INTEGER;
BEGIN
    FOR i IN 1..num_iters LOOP
        PERFORM * FROM bloom_test WHERE id = 'c81e728d-9d4c-2f63-6f06-7f89cc14862c';
    END LOOP;
END; $$ LANGUAGE plpgsql;

-- The pg_stat_statements collects the average procession time for the looped query
SELECT test_query(10); 

-- The extention stores the nomalized version of the query in its rows.
SELECT
    query, calls, rows, total_plan_time, mean_exec_time, rows
FROM pg_stat_statements
WHERE query LIKE '%SELECT * FROM bloom_test WHERE id%' 
-- The multiple rows caused by the In-clause explosion
ORDER BY total_exec_time DESC;
query                                 |calls|rows|total_plan_time|mean_exec_time|rows|
--------------------------------------+-----+----+---------------+--------------+----+
SELECT * FROM bloom_test WHERE id = $1|   10|2000|         5.9939|0.406449999999|2000|
```

1

The PERFORM command differs from SELECT, as it executes a query but discards the results.                         It allows the pg\_stat\_statements to store only the **internal processing time**, removing the network latency and the client-size rendering.

1

The perform is a PL/pgSQL-specific keyword that is converted into a SELECT statement during execution. It sets an internal flag in the Server Programming Interface (SPI) to discard any returned rows. The pg\_stat\_statements extension intercepts the query after this PL/pgSQL rewrite process occurs.

The PostgreSQL creates an Abstract Syntax Tree (AST) to interpret SQL commands, it destruct every query into multiple nodes. The pg\_stat\_statements extension replaces constant values with parameter placeholders and generates a unique queryid.

1

```sql
-- The normalized queries share the same execution tree structure
SELECT * FROM test WHERE a IN (1, 2, 3, 4, 5, 6, 7);
SELECT * FROM test WHERE a IN (1, 2, 3, 4, 5, 6, 7, 8);

SELECT query, calls, queryid
FROM pg_stat_statements
WHERE query LIKE '%SELECT * FROM test WHERE a IN%' 
ORDER BY total_exec_time DESC;

query                                                           |queryid             |calls|
----------------------------------------------------------------+--------------------+-----+
SELECT * FROM test WHERE a IN ($1, $2, $3, $4, $5, $6, $7)      |-9065225424971712000|    1|
SELECT * FROM test WHERE a IN ($1, $2, $3, $4, $5, $6, $7, $8)  |  -36995768465626311|    1|
```

1

The extension's normalization process only replaces the literal constant values, it doesn't alter\
the underlying syntax tree structure. The PostgreSQL interprets each IN() paramether\
as a separate node, which results in multiple unique queryID.

1

1

1

The pg\_stat\_statements differs from the pg\_statistic system view in both the data it stores and the stage of query execution it covers.

The pg\_statistic stores the disk's data distribution statistics as part of the database metadata. It's part of the query planner estimations before the execution, and it is updated statically during maintenance operations like ANALYZE. The pg\_stat\_statements stores the query runtime statistics, which are updated after query execution completes.

The pg\_stat\_statements is designed for immediate, live shared memory updates after every query runs. It's columns are fixed-sized, each occupying a specific amount of 8kb disk pages. This design allows PostgreSQL to use pointer arithmetic to overwrite a metric in place, eliminating memory allocation overhead and avoiding memory fragmentation.

The query column contains variable-length text and can't fit into the fixed-size design. The PostgreSQL separates it from the internal memory structures and stores the actual query text string in the pgss\_query\_texts disk file.

1

1

1

### Identifying columns

These pg\_stat\_statement columns contains the query execution identifiers and context values.

**userid**: An unique primary key shared between multiple system catalogs, it's associated with the username used during the server connection startup step.                                                                           **dbid**: The unique identifier of the database where the client executes the query.                                  **toplevel**: A boolean value that indicates the execution context of the query. It's true for queries executed directly by the client as a top-level command. It's false for queries executed as a nested statement from inside another database object, like a PL/pgSQL function.                                                 **queryid**: A 64-bit hash code that identifies and groups a set of normalized query structures.

The queryid identifier differs from the userid and dbid because it's generated by a hashing function. It converts a specific query structure into a single identifier, allowing the database to group multiple executions that share the exact same structure into one single tracking entry.

The hash identifier doesn't only follow the query's text syntax, but also relies on a post-parse analysis process. The database evaluates the query's logical meaning, ensuring that two textually identical queries that reference different tables will be stored as separate entries.

The hashing function is enabled by the compute\_query\_id setting in the postgresql.conf file, which dedicates processing resources to mathematically calculate these hash values.

1

```sql
-- Each client connection creates a new userID,  a reused connection still changes userID
-- Multiple queries on different tables share the same dbID
SELECT userid, dbid, toplevel, queryid
FROM pg_stat_statements
WHERE query LIKE '%SELECT * FROM bloom_test WHERE id%' 
ORDER BY total_exec_time DESC;

userid|dbid|toplevel|queryid            |
------+----+--------+-------------------+
    10|   5|false   |7654037259107770231|
```

1

### PLAN columns

1

These columns record the time spent by the query planner converting SQL statements into execution trees.

> **plans**: The number of query plans created for the queries part of the normalized group. **total\_plan\_time**: The total time (in milliseconds) used to create the query plans. **min\_plan\_time**: The minimum time required to create a query plan.                                     **max\_plan\_time**: The maximum time required to create a query plan.                                                                    **mean\_plan\_time**: The average time used to create a single query plan.                                                 **stddev\_plan\_time**: The standard deviation in the queries. It tracks the planning time differences between the different queries condition values aplied to the same table data.

```sql
-- Every new query increases the plans column once
-- The postgresql doesn't count repeated cached queries
-- The deviation represents a data skew, a difference in table data distribution
-- It detects short but inconsistent planning times that require optimitation.
plans|total_plan_time|min_plan_time|max_plan_time|mean_plan_time|stddev_plan_time|
-----+---------------+-------------+-------------+--------------+----------------+
    1|         5.8999|       5.8999|       5.8999|        5.8999|             0.0|
-- It's more precise than the flat mean_exec_time value
-- It returns 0 for repeated queries
```

We collect the SQL statement timing statistics with a node-level implementation or postgresql hooks.

The EXPLAIN ANALYZE attaches a measurement engine to the query executor, it triggers a timer call for every query execution node. It generates a high timing overhead.                                                       The pg\_stat\_statements registers C functions on the PostgreSQL's internal hooks to record query execution timestamps. It avoids any query node-level tracking.

1

### EXEC columns

1

These columns track the cumulate the count and the timestamps of the query execution plans.

> **calls**: The total number of times the SQL statement was executed.                                    **total\_exec\_time**: The total time (in milliseconds) spent executing the statement across all **calls. min\_exec\_time**: The minimum time (in milliseconds) spent executing the statement. **max\_exec\_time**: The maximum time (in milliseconds) spent executing the statement.                **stddev\_exec\_time**: The population standard deviation of the query execution time. It's evaluated alongside mean\_exec\_time, a high value indicates an unstable query execution. **mean\_exec\_time**: The average execution time per SQL statement execution (in milliseconds). **rows**: The total number of table rows retrieved, inserted, updated, or deleted across all statement executions.

1

The plan and execution counts differ, a cached plan doesnt increase its count for every query execution.&#x20;The calls column tracks only completed query executions, excluding any executions that\
fail with an error.

The pg\_stat\_statements view includes the execution statistics for the procedural statements executed inside PL/pgSQL functions, like variable assignments or conditional checks. They receive a queryid and exec columns, but all of their planning metrics remain NULL because they bypass the query planner and are executed directly by the PL/pgSQL interpreter.

1

```sql
-- 
SELECT
    query, calls, total_exec_time, min_exec_time, max_exec_time, 
    mean_exec_time, stddev_exec_time, rows
FROM pg_stat_statements;

-- The low-level expressions are visible forthe pg_stat_statement.track = 'all'
query                                     |calls|total_exec_time   |min_exec_time|
SELECT * FROM bloom_test WHERE id = $1    |   10|3.6370999999999998|       0.1536|
SELECT test_query($1)                     |    1|           14.3413|      14.3413|
$1                                        |    0|               0.0|          0.0|
num_iters                   						  |    0|               0.0|          0.0|

max_exec_time |mean_exec_time   |stddev_exec_time  |rows|
2.124500000000|0.363710000000000|0.5871200549291431|2000|
14.3413       |          14.3413|               0.0|   1|
0.0           |              0.0|               0.0|   0|
0.0           |              0.0|               0.0|   0|
```

1

1

### The blks

1

The SQL statements are **declarative**: they define what data is requested, not how to access it.\
The **client backend process** translates high-level SQL commands into a physical execution plan.

It follows the Write-Ahead Logging (WAL) protocol, it creates a **WAL file** which records the byte level changes&#x20;described by the query. It then modifies the **target tuples**, both column values and header metadata, within&#x20;the shared buffers.

The SQL commands apply their changes differently in the e**xecution layer**:\
The SELECT is a **read/hit** operation, it can include a **write** when it updates the target tuple hint bits,&#x20;marking the disk page as **dirty**.\
The INSERT **reads** the Free Space Map (FSM) to locate an existing page with available space or\
allocates a new page. It then **writes** the new tuple into shared\_buffers.\
The DELETE **reads** the shared\_buffers to locate the target tuple, it **writes** on the header the **xmax**\
value to the current transaction ID.\
The UPDATE is implemented as a combined DELETE and INSERT. It updates the existing tuple header\
with an xmax value, then reads the FSM to locate space and writes a new tuple version with an xmin value.

The blks columns track the disk pages involved by the SQL query they are categorized by their location and role during the query execution.&#x20;

> **shared\_blks\_hit**: The disk pages accessed from the shared\_buffers memory cache. **shared\_blks\_read**: The disk pages that have to be loaded from disk, they are then cached in the shared buffer for future queries.                                                                                  **shared\_blks\_dirtied**: The disk pages modified in shared\_buffers by the query. It includes the header metadata changes (xmin, xmax, hint bits).                                                       **shared\_blks\_written**: The number of dirty disk pages physically written to disk directly by the client backend process. It's usually not part of the SQL command.

1

```sql
-- Accessed form shared buffer, extracted form disk pages or dirtied
-- a hint wot generate a wal unless we change teh setting wal_log_hints = on
SELECT
    query, shared_blks_hit, shared_blks_read, shared_blks_dirtied, shared_blks_written
FROM pg_stat_statements;

query                                 |shared_blks_hit|shared_blks_read|shared_blks_dirtied
SELECT * FROM bloom_test WHERE id = $1|            219|              28|                 11
SELECT test_query($1)                 |            257|              30|                 11
$1                                    |              0|               0|                  0
num_iters                             |              0|               0|                  0
-- The shared_blks_written is 0 because the dirty pages stay in memory and don't 
-- affect the disk data.

```

1

The client backend processes are not designed to modify the data blocks stored in disk, it relies on the background processes. The CHECKPOINTER scans the shared\_buffers for the disk pages and overwrites them to the disk. It's an asynchronous and physical-level process that doesn't modify the set row data or xmin/xmax values.                                                                                                                                           The checkpointer can be triggered by its checkpoint\_timeout, by its CHECKPOINT sql command, by the WAL\_buffer exceeding the max\_wal\_size or by a database shutdown.

1

The VACUUM is a background process that cleans up dead tuples. It scans the xmin/xmax\
header values to find the outdated tuples, it updates the Free Space Map (FSM) and Visibility Map (VM)&#x20;for future queries to use. The standard VACUUM doesn't change the total number of disk pages occupied by the table data.

1

The tuple header stores the metadata. The xmin field is a permanent XID 32-bit value of the transaction that created the tuple. The xmax field is set to 0 by default; it's updated with the XID of the transaction that locks, updates, or deletes the tuple.

The SQL statement changes both the tuple payload and its header, it records them into a wal buffer. It then flushes the wal buffer files into the pg\_wal disk directory, to ensure data recoverability.

The postgreSQL can't wait for VACUUM to physically clean up outdated tuples. It relies on Multi-Version Concurrency Control (MVCC) visibility checks in shared buffers, allowing queries to dynamically skip dead tuples based on transaction hits.

The Commit Log is an array disk file mapping each 32-bit Transaction ID with a 2-bit status flag: IN\_PROGRESS, COMMITTED, ABORTED, or SUBCOMMITTED. It provides a sequential, permanent record updated when transactions finalize.

A SQL statement reads the xID value of its tuple's xmin/xmax fields, it then triggers a I/O disk check for the commit log to determine if their transaction was already commited.

The PostgreSQL includes a series of hint bits in the tuple header to cache teh xmin/xmax xID state. It uses a 16-bits flag (t\_infomask) with specific values (HEAP\_XMIN\_COMMITTED, HEAP\_XMIN\_INVALID, HEAP\_XMAX\_COMMITTED, HEAP\_XMAX\_INVALID) to avoid the I/O overhead of the commit log check.

The xmin hint bit is set by the first SQL statement that reads the tuple value. It runs a I/O commit log access to check if the transaction that included the query that created the tuple is commited, it then sets the corresponding hint bit, marking the tuple as visible and skipping any xmin checks in the commit log. The xmax hint bit is set when a SQL statement reads a modified tuple. It checks the xID of the xmax in the commit log, if the transaction that contained the query that deleted or updated the tuple is commited, it sets the hint bit to make the tuple invisible for teh next queries, vaiting for teh VACUUM to physically delete it.

The VACUUM scans all the tuples in the disk pages marked as dirty and sets their hint bits by checking the commit log for every single one.

1

### The LOCAL, TEMP and TIME BLKS columns&#x20;

1

The local\_blks columns track the runtime statistics of pages created by the CREATE TEMP TABLE command. Temporary tables are private to individual client connections and lack the locking overhead required for permanent tables accessed across all sessions. They are automatically dropped when the connection closes and don't generate Write-Ahead Logging (WAL) records.

The temp\_buffers setting in the configuration file defines the size of the memory allocated for temporary tables. The hit and dirtied columnms indicate temporary data blocks processed directly in RAM, while the read and written metrics represent blocks that exceeded temp\_buffers and were created on disk.

The TEMP\_BLKS columns track the implicit temporary disk pages created by query execution nodes. The work\_mem setting in the configuration file sets the memory allocated for handling query execution. Complex operations (such as GROUP BY, ORDER BY, DISTINCT, or merges) that exceed work\_mem implicitly write temporary data blocks to disk. These files are deleted as soon as the query finishes executing.

The temporray blocks differ from the temporary tables, they skip the shared\_buffers memory space and can't record buffer cache hits or dirty pages. The temp\_blks\_written column returns the number of disk pages pushed to storage, while temp\_blks\_read returns the number of disk pages read back during execution.

1

```sql
-- They differ form teh tempo bloks because they dont need to spill form teh defined work_mem property, set
-- temp_buffer =/= work mme
query                                         |local_blks_hit|local_blks_read|local_blks_dirtied|
------------------------------------------------------------------------------------------------+
SELECT * FROM bloom_test WHERE id = 'c81e...' |             0|              0|                 0|
SELECT test_query($1)                         |             0|              0|                 0|

local_blks_written|temp_blks_read|temp_blks_written|
------------------+--------------+-----------------+
                 0|             0|                0|
                 0|             0|                0|
```

1

All BLKS categories include associated TIME columns that track execution intervals. The timing metrics don't cover the memory accesses, which are almost instant, but only the disk I/O operations; the time columns are available exclusively for read and written metrics. They require the track\_io\_timing configuration setting to be explicitly set to on.

1

```sql
-- AQll columns juts ass _time to tehir name
-- we need to restart teh entirepostgresql system to reset teh shared buffer, a disconect wont be enought.
-- using teh windows-r shortcut
- the data is stil recorded, it will jst return 0
ALTER SYSTEM SET track_io_timing = on; 
SELECT pg_reload_conf();

SELECT
	query, shared_blk_read_time, shared_blk_write_time, 
	local_blk_read_time, local_blk_write_time, temp_blk_read_time, temp_blk_write_time
FROM pg_stat_statements;

query                                  |shared_blk_read_time|shared_blk_write_time|local_blk_read_time|
---------------------------------------+--------------------+---------------------+-------------------+
SELECT * FROM bloom_test WHERE id = $1 |             15.6444|                  0.0|                0.0|
SELECT test_query($1)                  |             25.1179|                  0.0|                0.0|

local_blk_write_time|temp_blk_read_time|temp_blk_write_time|
--------------------+------------------+-------------------+
                 0.0|               0.0|                0.0|
                 0.0|               0.0|                0.0|
```

1

1

### WAL columns

The WAL columns track the Write-Ahead Logging statistics generated by table or index changes during query\
execution.

The wal\_records column tracks the total number of individual entries written to the WAL stream,\
which varies based on the number of rows modified and the index structures present on the table.

The wal\_bytes column returns the cumulative byte size of all generated WAL records.

The wal\_fpi column records the number of full-page images. An FPI captures the entire 8 KB block when it's\
modified for the first time after a checkpoint, while subsequent operations log only their specific\
specific changes.\
This design ensures PostgreSQL can restore a clean baseline page during crash recovery, to then apply all&#x20;successive changes on top of it.

1

```sql
-- READ or HIT queries queries that include, like SELEFCT, dont generate any Wl 
-- two pages being accessed mean 2 fpi and 2 entries, teh data recoded, for the first +
query is 16kb whic after internal optimitation is 14059 bytes, 
-- SELECT will generate 0 as value for all columns

insert into bloom_test(id, padding) values ('cfcd2084-95d5-65ef-6e67-dff9f98767da', '6974ce5zc660610b44d9b9fed1ff9548');

SELECT query, wal_records, wal_fpi, wal_bytes
FROM pg_stat_statements;

query                                                   |wal_records|wal_fpi|wal_bytes|
--------------------------------------------------------+-----------+-------+---------+
insert into bloom_test(id, padding) values ($1, $2)     |          2|      2|    14059|
```

1

1

1

1


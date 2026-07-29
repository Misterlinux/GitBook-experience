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
DECLARE
    i INTEGER;
BEGIN
    FOR i IN 1..num_iters LOOP
        PERFORM * FROM bloom_test WHERE id = 'c81e728d-9d4c-2f63-6f06-7f89cc14862c';
    END LOOP;
END;
$$ LANGUAGE plpgsql;

-- The pg_stat_statements collects the average procession time for the looped query
SELECT test_query(10); 

-- The extention stores the nomalized version of the query in its rows.
SELECT
    query, calls, rows, total_plan_time, mean_exec_time, rows
FROM pg_stat_statements
WHERE query LIKE '%SELECT * FROM bloom_test WHERE id%' 

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

1

1

1

1

1

1

1

1

1

1


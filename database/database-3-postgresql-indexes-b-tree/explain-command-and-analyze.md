# EXPLAIN command and ANALYZE

* 1
* 1
* 1
* 1

1

### QUERY PLANNER, QUERY TREE AND EXECUTION PLAN creating

The PostgreSQL parses the SQL query to create the **logical structure** that defines the execution plan.\
The query planner uses the **query tree** and **table statistics** to find the most suitable **strategy** for its resulting query plan.

The **Query Tree** translates the **SQL text command** into a structured set of logical requirements necessary to satisfy the SQL query.\
It's composed of a hierarchical series of **nodes**, with each representing a specific **logical operation** or clause from the SQL statement.

The query tree represents the **syntax** and **logic** of the SQL statement within its hierarchical structure.\
The **SQL command** serves as the **root node**, and its branches organize the query's components:

> **RangeTable**: Specifies the relations being accessed (FROM tables).> \
> **TargetList**: Defines the resulting columns (SELECT targets).> \
> **Qualification**: Contains the Boolean expression that checks which rows should be returned by the WHERE clause.> \
> Other clauses, like GROUP BY, are included as their own specific nodes.



\--->IMMAGGINE&#x20;

The **query tree** develops multiple **branches** to represent specific SQL query logic.\
They are used for compound **logical clauses** (AND, OR, and NOT) within the WHERE clause, and for showing the relationships between the **query's** various elements, like **tables**, **joins**, and **projections**.

The **query planner** converts the **logical definition** of the query tree into an **execution plan.**\
It uses **transformation functions** to map **logical** operation nodes to **physical access steps**, and applies mathematical properties to maintain the integrity of the SQL logic.

The query planner defines its execution plan using **paths**, which are high-level sequences of steps.\
Each step represents a low-level scan operation that performs the **physical access method** to satisfy the specific **node's logical condition**.

The query planner estimates the total **cost** of each defined **execution path** using **table statistics**.\
It accesses the **pg\_statistics** system catalog to calculate and summarize the cost for every single **scan operation** within that path.

The path with the lowest estimated cost becomes the **query plan** used during the execution process.\
The query plan is a **tree structure** that represents the **execution strategy**, detailing the sequence of steps required to satisfy the **SQL query**. The text output from the EXPLAIN command describes the plan by using its index scan type.

1

### Explain command

The EXPLAIN command is an **estimation tool** not an execution tool.\
It returns the **execution plan** used by the **query planner**, but it doesn't run the query, nor does it include the actual execution time or operation properties.

The EXPLAIN command provides the query planner's **estimated execution strategy** and **cost**.\


> **Startup Cost**: The cost of the **preliminary actions** needed to process the **query**, like accessing the index page, sorting data, and initializing any necessary function, before the first row is retrieved.> \
> **Total Cost**: The cumulative cost to fully process the SQL query and retrieve all of its resulting rows.

The costs values are used differently depending on the EXPLAIN query operations.\
A **single-row operation** relies more on the Startup Cost, as the database often optimizes the execution plan to finish early and skip the total estimated work. The Total Cost represents the most conservative estimate, which requires scanning every relevant row to fully satisfy the query.\
Query execution plans are influenced by WHERE and LIMIT clauses.

The LIMIT clause **reduces** the total estimated cost during query execution.\
The query uses **cost interpolation** to estimate the cost, taking a **fraction** of the **total execution cost** based on the number of rows defined in the clause.

The EXPLAIN cost output is expressed in abstract, **arbitrary units**.\
The database defines these units for **internal use** as relative values, which the query planner uses to **compare** the cost between **different execution plans for the same query**. The database interprets a cost of 1.0 as the expense required to perform a sequential I/O access on a single disk page.\
The cost units do not correspond to real time values and will not reflect the actual execution time returned by EXPLAIN ANALYZE.

```sql
-- The query planner performs an ACCESS PATH SELECTION
-- It uses the table statistics to select the better query execution plan
CREATE TABLE tavole ( codice integer, padding text );

INSERT INTO tavole (codice, padding)
SELECT floor(random() * 10000) + 1, md5(random()::text)
FROM generate_series(1, 10000) AS i;

-- An index is designed to handle specific queries conditions
create index paddinx on tavole (padding);

-- The planner uses the table statistics to calculate the clause selectivity
-- The plan retrieves all matching index values form the heap table and aplies the filter
-- It considers it a more effective plan based on teh WHERE clause
EXPLAIN SELECT * FROM tavole WHERE codice = 4348 and padding = 'c000...';
---------------------------------------------------------------------+
Index Scan using paddinx on tavole  (cost=0.29..8.30 rows=1 width=37)|
  Index Cond: (padding = 'c00066a000586c5327a9d5a6428a786b'::text)   |
  Filter: (codice = 4348)   
```

1

### OPTIONS RESUMING

The EXPLAIN command **OPTIONs** control the **properties** of the **query planner**'s output, and can modify the displayed results' level of detail.

The **VERBOSE** option provides a more technical and detailed explanation of the query plan.\
It expands the output of the query plan, explicitly adding details such as the **full set of columns** physically accessed by the _query plan_ bottom-level _nodes_ that are required to satisfy the SQL clause.\
It includes the **query identifier**, which is used by the database administrators for tracking and optimization purposes.

```sql
-- We add options between parentheses
-- It specifies all columns involved in the query plan and query identification ID
explain (verbose) SELECT * FROM tavole WHERE codice = 4348 and padding = 'c00066...';
----------------------------------------------------------------------------
Index Scan using paddinx on public.tavole  (cost=0.29..8.30 rows=1 width=37)
  Output: codice, padding                                                   
  Index Cond: (tavole.padding = 'c00066a000586c5327a9d5a6428a786b'::text)   
  Filter: (tavole.codice = 4348)                                            
Query Identifier: -2711217402490113008 
```

The **SETTINGS** option returns the **configuration parameters** (GUC variables) applied to the query planner.\
These parameters directly impact the planner, including values like **cost factors** (which can adjust the cost of I/O operations), **memory limits** (which change the available **work\_mem** size for the query), and **feature toggles** (which can remove specific **available scan types**).\
It includes all active parameters in the output which deviate from the default planning operation.

```sql
-- The Grand Unified Configuration is part by the database management system
-- It's aplied during the planning pahse, not execution
SET enable_seqscan = off;
explain (settings) SELECT * FROM tavole WHERE codice = 4348 and padding = 'c00066...'; 
-------------------------------------------------------------------------
Index Scan using paddinx on tavole  (cost=0.29..8.30 rows=1 width=37)    
  Index Cond: (padding = 'c00066a000586c5327a9d5a6428a786b'::text)       
  Filter: (codice = 4348)                                                
Settings: search_path = 'public, public, "$user"', enable_seqscan = 'off'
```

The **GENERIC\_PLAN** option creates a **query plan** that executes the SQL query by replacing the specific input values with **generic parameters**. It bases its estimation cost on general table and data distribution **statistics** (like MCV and histograms) without relying on calculated selectivity for the specific query values.\
It uses the underlying data types and existing indexes to present a generic access strategy.

```sql
-- It replaces the query conditions values with generic parameters
-- It create a plan using table statistics and clause operators selectivity
PREPARE my_query (integer, text) AS
SELECT * FROM tavole WHERE codice = $1 and padding = $2;

EXPLAIN (GENERIC_PLAN) EXECUTE my_query(15347, '1bf8d8e46fcd41d2baf821579c300996');
----------------------------------------------------------------------------------
Index Scan using tavole_codice_idx ON tavole  (cost=0.42..8.44 rows=1 width=100)
   Output: (all columns of tavole)
   Index Cond: (tavole.codice = $1)
   Filter: (tavole.padding = $2)
```

The **BOOLEAN** option explicitly controls which listed options are included in the EXPLAIN command output. It's TRUE by default and allows values like ON/OFF and 1/0.

```sql
-- All the options included are still performed in the planning phase
-- It doesn't save time, it provides a cleaner output for specific queries
explain (SETTINGS FALSE) SELECT * FROM tavole WHERE codice = 15347 and padding = '1bf8...';
---------------------------------------------------------------------+
Index Scan using paddinx on tavole  (cost=0.29..8.30 rows=1 width=37)|
  Index Cond: (padding = '1bf8d8e46fcd41d2baf821579c300996'::text)   |
  Filter: (codice = 15347)                                           |
S̶e̶t̶t̶i̶n̶g̶s̶:̶ ̶s̶e̶a̶r̶c̶h̶_̶p̶a̶t̶h̶ ̶=̶ ̶'̶p̶u̶b̶l̶i̶c̶,̶ ̶p̶u̶b̶l̶i̶c̶,̶ ̶"̶$̶u̶s̶e̶r̶"̶'̶                        |
```

The **FORMAT** option is a meta-command that dictates the **serialization style** of the EXPLAIN output, changing the **presentation** without affecting the internal query execution or the data types accessed by the plan.

```sql
-- It can be used for EXPLAIN ANALYZE query outputs
-- Avaiable formats TEXT, JSON, XML, YAML
explain (FORMAT JSON) SELECT * FROM tavole WHERE codice = 4348 and padding = 'c0006...';
[{
  "Plan": {
    "Node Type": "Index Scan", "Scan Direction": "Forward",
    "Index Name": "paddinx", "Relation Name": "tavole",
    "Startup Cost": 0.29, "Total Cost": 8.30, "Plan Rows": 1, "Plan Width": 37,
    "Index Cond": "(padding = 'c0006...'::text)", "Filter": "(codice = 4348)"
  }
}]                 
```

The **MEMORY** option reports the memory used during the query's **planning phase**.\
It covers the memory used for internal **data structure processes** like creating the query tree, loading table statistics, and calculating estimated costs.

```sql
-- It returns the memory used by the query planner
-- The allocated memory returns teh data chucks allocated for the planning phase
explain (MEMORY) SELECT * FROM tavole WHERE codice = 4348 and padding = 'c0006...';
---------------------------------------------------------------------
Index Scan using paddinx on tavole  (cost=0.29..8.30 rows=1 width=37)
  Index Cond: (padding = 'c00066a000586c5327a9d5a6428a786b'::text)   
  Filter: (codice = 4348)                                            
Planning: Memory: used=12kB  allocated=16kB 
```

The **SUMMARY** option includes the time statistics needed for both the planning and execution phases, depending on the command used.

```sql
-- It doesn't remove the planning time estimation process, it removes it from the output
explain (SUMMARY FALSE)
SELECT * FROM tavole WHERE codice = 4348 and padding = 'c0006...';

Index Scan using paddinx on tavole  (cost=0.29..8.30 rows=1 width=37)
  Index Cond: (padding = 'c00066a000586c5327a9d5a6428a786b'::text)   
  Filter: (codice = 4348)
  P̶l̶a̶n̶n̶i̶n̶g̶ ̶T̶i̶m̶e̶:̶ ̶0̶.̶1̶6̶3̶ ̶m̶s̶ ̶
-- It's active by default and produces two times for ANALYZE
Planning Time: 0.115 ms, Execution Time: 0.694 ms
```

### The ANALYZE command

The **ANALYZE** option **executes the query plan** defined by the EXPLAIN command.                                        It collects the **runtime metrics** of the execution process and compares the estimated cost and time to the actual query results.

```sql
-- Some options are exclusive to ANALYZE as they provide information for the execution phase
-- It returns the estimated and actual query output results.
explain ANALYZE SELECT * FROM tavole WHERE codice = 4348 and padding = 'c0006...';
----------------------------------------------------------------------------------
Index Scan using paddinx on tavole  
  (cost=0.29..8.30 rows=1 width=37) (actual time=0.088..0.090 rows=1 loops=1)
  Index Cond: (padding = 'c00066a000586c5327a9d5a6428a786b'::text)                 
  Filter: (codice = 4348)                                                                
Planning Time: 0.744 ms, Execution Time: 0.282ms    

-- The ROLLBACK reverses all changes ade by the query we want to EXPLAIN ANALYZE
BEGIN;
EXPLAIN ANALYZE
INSERT ...;
ROLLBACK;
```

The **SERIALIZE** option returns the **serialization properties** of the query results.\
The **query planner** uses an internal data type to convert the **table data** physically accessed by the **query plan's** bottom **node** into a type-specific **binary data**. This binary data is then used for the **internal operations** that satisfy the SQL clause.\
The **serialization** occurs **after the query plan execution**. It converts the returned table row data from the internal data type into the **planner output forma**t that is sent to the **client**.\
It includes the output format (which can be TEXT or BINARY), the **time** required to convert the internal data and the **size** of the resulting output after the serialization process by the query planner.\
The conversion time depends on the complexity of the data type or the returned values, especially those requiring extra retrieval processes, such as TOAST data.

The SERIALIZE option is a **profiling tool** for EXPLAIN ANALYZE that measures the time required to **format query results** for the client. It distinguishes between the time spent retrieving data and the time spent formatting it.\
Its use case is for queries involving GIN-indexed **JSONB columns**. They present a fast query execution plan, due to the JSONB binary structure not requiring key parsing, but have a **slower output phase** caused by the cost of converting JSONB into TEXT.\
The EXPLAIN ANALYZE ignores the **serialitation process** for result-set queries (like SELECT) and doesn't include the time required to format the rows in its total execution time. The SERIALIZE option reveals this hidden cost.

```sql
-- A specific WHERE clause returns a small result-set that occupies little space and time.
explain (analyze, SERIALIZE) SELECT * FROM tavole WHERE codice = 4348 and padding = 'c0...';
------------------------------------------------------------------------------------
Index Scan using paddinx on tavole  (cost=0.29..8.30 rows=1 width=37)
  Index Cond: (padding = 'c00066a000586c5327a9d5a6428a786b'::text)
  Filter: (codice = 4348)                     
Planning Time: 0.162 ms      
Serialization: time=0.003 ms  output=1kB  format=text 
Execution Time: 0.114 ms     
```

The **BUFFERS** option tracks the usage of buffer **memory space** in RAM.&#x20;It returns additional statistics indicating the optimized disk page access:

> **HITS**: The number of requests that were satisfied by the RAM buffer data instead of requiring an I/O disk page access (fast access).> \
> **READS**: The number of actual physical disk pages accessed needed to retrieve the query-requested data.> \
> It adds _specific statistics_ for commands that modify the table (INSERT, UPDATE, DELETE):> \
> **DIRTIED**: The number of query operations that were aplied to disk pages in the memory buffer.> \
> **WRITTEN**: The number of modified disk pages that were pushed from the buffer to the disk memory.

The BUFFERS output indicates the level of optimization for the query, which is influenced by similar **previous queries** that loaded relevant data pages into the **buffer memory**.

```sql
-- In the planning phase EXPLAIN accesses the metadata disk pages
-- The pages contain the column data types, avaiable index and table statistics
-- The data the query planner needs to cerate a query plan for the query
explain (BUFFERS) SELECT * FROM tavole WHERE codice = 4348 and padding = 'c000...';
---------------------------------------------------------------------
Index Scan using paddinx on tavole  (cost=0.29..8.30 rows=1 width=37)
  Index Cond: (padding = 'c00066a000586c5327a9d5a6428a786b'::text)   
  Filter: (codice = 4348)                                            
Planning: Buffers: shared hit=107 read=4                           

-- The execution phase ANALYZE accesses the disk pages with the actual column values 
-- The pages accessed by the scan type depende on the created query plan 
-- Its value is separated from the EXPLAIN estimated pages.
explain (analyze, BUFFERS) ...
-------------------------------------------------------------------------------------
Index Scan using paddinx on tavole  (cost=0.29..8.30 rows=1 width=37)
  Index Cond: (padding = 'c00066a000586c5327a9d5a6428a786b'::text)         
  Filter: (codice = 4348)                                                        
  Buffers: shared read=3     
Planning Time: 0.160 ms, Execution Time: 5.472 ms    
```

The **TIMING** option involves the **Actual Time** property and is applicable only to EXPLAIN ANALYZE.\
It enables the **tracking** of the execution time for every **query operation**, which returns both the **startup** and **final times** for each node.\
The overhead is caused by the tracking functions that collects the **timestamps** for every uperation, which are part of the total execution cost.\
A disabled TIMING option removes the **collection overhead**, returning a more accurate Execution Time without the extra functions, while removing the Actual Time properties from the ouput.

```sql
-- It removes timestamp collection function overhead for a closer query time estimate
-- It's called the Observer effect, when the measuring a query makes it slower
explain (analyze, TIMING off) select T * FROM tavole WHERE codice=4348 and padding = 'c...';
--------------------------------------------------------------------------------------------
Index Scan using paddinx on tavole  
  (cost=0.29..8.30 rows=1 width=37) 
  (a̶c̶t̶u̶a̶l̶ ̶t̶i̶m̶e̶=̶0̶.̶0̶7̶2̶.̶.̶0̶.̶0̶7̶4̶  actual rows=1 loops=1)  -- removed by the option
  Index Cond: (padding = 'c00066a000586c5327a9d5a6428a786b'::text)  
  Filter: (codice = 4348)                               
Planning Time: 0.166 ms, Execution Time: 0.109 ms  -- mantained timestamps
```

The **WAL** (Write-Ahead Log) option in the EXPLAIN ANALYZE explicitly enables the reporting of WAL statistics.\
The query output includes all relevant **pg\_wal statistics** used by the database to maintain **data durability** and **recovery** for the disk pages handled during **query execution**. The WAL metric only reflects logging activity; it has no influence on query planning. Its properties are:

> The WAL **records property** tracks the number of individual **log entries** created for the operation.> \
> It refers to the involved **database relations**, like an INSERT requiring one record for the main table data and one record for every index associated with the columns being modified.
>
> The WAL **bytes** property represents the total amount of **data** written to the **pg\_wal directory** by the query operation. It includes the size of the **full disk page image** (FPI) and the small change records.

The database first **loads** the **disk page** containing the data to be modified into the RAM buffer. Before applying any changes, the database checks if the page is not securely logged; if it is not, the database **copies** the complete **unedited disk page** (the FPI) from the RAM buffer into the **WAL log file** to ensure data recovery. The database then applies the changes to the page in the buffer, marking the page as DIRTIED, and adding a small WAL record detailing only the change to the WAL file.

The **Free Space Map** is an auxiliary structure created for each **database relation** to organize the remaining free space within their disk pages.\
It's primarily used during modification operations to quickly locate the most efficient position for new data.\
The FSM cost is included in the node's Actual Time and contributes to the total WAL bytes output during execution.

```sql
-- It's false by default and its FPI tracks every database relation operation
-- The FPI first loads the disk page insert target, it then lowers its FSM space
-- The FSM is stored a disk page, and its change triggers another fpi WAL record
explain (analyze, WAL) insert into tavole(codice, padding) values (12128, '1bf8...');
rollback;
--------------------------------------------------------------------------------------------
Insert on tavole  (cost=0.00..0.01 rows=0 width=0) (actual time=2.576..2.577 rows=0 loops=1)
  WAL: records=2 fpi=2 bytes=10343                                                          
  ->  Result  (cost=0.00..0.01 rows=1 width=36) (actual time=0.002..0.002 rows=1 loops=1)   
Planning Time: 0.026 ms, Execution Time: 7.931 ms  
```

### The ANALUYZE command

The EXPLAIN ANALYZE command provides the **runtime statistics** of the executed **query plan**.\
The separate ANALYZE command is a **maintenance operation** that **collects** and **updates** the **table statistics**. It's a **read-only** process that neither executes nor modifies any data in the table.

```sql
-- We can use it for entire tables or specific columns
analyze tavole(codice);
```

The **SKIP\_LOCKED** option is applied to the ANALYZE process.\
It avoids waiting for the lightweight lock on a table caused by other operations. It allows ANALYZE to skip the statistics update for the specific table to improve efficiency.

```sql
-- Its a one-time option designed for ANALYZE that includes multiple tables
-- It only updates the statistics of tables that aren't involved in any operation
-- It doesn't work at the column level
analyze (SKIP_LOCKED) tavole;
```

The ANALYZE command executes a **sequential scan** of the table's disk pages to update the **pg\_statistics** columns. It applies a **sampling strategy** to extract a specific set of sampled table row values, which are then used by the **statistical functions** to calculate teh table statistics **metadata**. The sampled set is fixed and doesn't depend on the table's total size; it's set by default at 30000 and based on the **default\_statistics\_target** setting (which is 100 by default), which controls the size and the level of detail of the statistical data. The statistical sampling **algorithm** provides a **random** and **statistically accurate** representation of the table's **data distribution**.

The algoritm used by ANALYZE, the **Reservoir sampling**, ensures that every row scanned by the ANALYZE command has an equal chance of being included in the final sampled set.

### EXPLENATION RESERVOIR sampling and VITTER

The Reservoir algorithm stores its samples values in the **reservoir array** of a fixed K size.

It guarantees that after processing all N values of the ANALYZE **data stream**, every single table row has the same K/N final **probability** of being included in the resulting reservoir array. It's designed for data streams with an **unknown total size** (N) whose data can't fit into the available memory.

The **I** variable keeps track of the **current value** being processed in the ANALYZE stream.                        The **K** values defines its array insertion, when I is less than the reservoir size (I\<K), all rows are automatically inserted. This **ensures small tables** are fully represented in the sample.                                     Once the stream **surpasses** the **array size** (I>=K), insertion is no longer guaranteed and will require a **statistical operation** and **random sampling** to replace of of the K existing elements in the now-filled reservoir.

The algorithm establishes an **inverse relationship** between the initial automatic insertion (when I < K) and the subsequent **probabilistic replacement** (when I>=K). The first automatically inserted K items are continuously subjected to the replacement process ensuring that the final selection probability for every item is maintained at K/N.

The **replacement operation** relies on two separate **random values**, **R** and **J**, used in different phases. The **initial insertion** uses the mathematical operation P=K/I, which results in a probability of **P>1** for I\<K, ensuring insertion.                                                                                                                                              The probabilistic replacement triggers **after I>=K** and requires additional values: The **Probability Test** (R) is applied to the resulting insertion probability P; the test passes if P>R, and the item is inserted into the **reservoir array**. The Replacement Index (J) is the **random index position** of the reservoir element that gets replaced. The process applies a balanced and probabilistic replacement to achieve a final statistically random sample.

The variables involved in the Reservoir Sampling process are:&#x20;

{% hint style="info" %}
K (**Reservoir Size**): The fixed size of the **array**, derived from the default\_statistics\_target setting.&#x20;

I (Current Stream **Index**): The index of the **item** currently being scanned from the **data stream**, representing its position the sampling operation.&#x20;

N (**Total** Stream Size): The total number of **table rows** in the **stream**, which is unknown beforehand and too large to be stored entirely in memory.&#x20;

J (Replacement Index): A random **index** generated by the algorithm to determine which element in the reservoir should be replaced by the current item (I), enabling unbiased random sampling.
{% endhint %}

The **Reservoir Sampling** ensures the final K/N probability result from its **mathematical derivation.**\
It defines the **insertion probability** for any **next item** as K/(I+1), which is valid regadless of the phase of the stream the index is in.

The Probability of staying for an inserted item is: P(staying) = P(servival\_before) / P(no\_replacement)

The probability that a given I item is already present in the reservoir array is K/I.

The Replacement Probability is obtained by multiplying the probability a new value has of being inserted by the chance that the specific existing I item is the one replaced among the K reservoir values

P(replacement) = K/I+1 \* 1/k = 1/I+1.

The No replcament probability is:

P(no\_replacement) = 1 - 1/I+1 = I/I+1

The probability an item, once inserted into the reservoir array, will remain is:

P(staying) = K/I \* I/I+1 = K/I+1

It confirms that the probability rule for a new value about to be inserted and the rule for a value to remain in the reservoir array are **mathematically equivalent**. Applying this rule to all N elements in the stream results in the final statistical probability K/N.

The floating-point calculation of all the (K/I+1) probability slows down the reservoir sampling process. The database utilizes an **algorithm implementation** that is mathematically equivalent to the calculation, which enables an optimized ANALYZE process. It **maintains the statistical accuracy** of the final data sample by **translating** the replacement **random J index** (used after the K/I+1 > R) into a single, **pre-calculated index** used at the beginning of the operation.

**Vitter's** **Algorithm** R is the slower implementation that proves the correctness of the reservoir sampling. The Algorithm Z is the optimized version used by the database, but both algorithms maintain the final selection probability of K/N.

The Algorithm R maintains the slow randomness check for every single value of the data stream, but now uses a **single random integer** J that fulfills both the **testing** and **replacement index roles**. The single generated random J uses the current (K/I) item denominator as its limit J in {1, ..., I}. It then compares J to the reservoir length K (J <= K); this ensures the probability of selection is proportional to K/I and covers the testing. If the comparison is true, the current item replaces the item at index J in the reservoir array, thus applying the replacement index role.

\--> IMMAGGINE

The Vitter's Algorithm Z optimizes the sampling process by removing the need to generate the random replacement index J and perform the selection check for each item in the ANALYZE data stream. It implements **skip logic** that allows it to use a single random value (often the logarithmic representation of a uniform random number) that is plugged into a formula to calculate the skip distance (S) to the next item selected for the reservoir.

### Shared buffer cache

The **Shared Buffer Cache** is a section of RAM used for read operations involving disk pages.

```sql
// Some code
ANALYZE (BUFFER_USAGE_LIMIT 2MB) tavole;
```

It occupies one-quarter of the total RAM, leaving the rest to be utilized by other memory areas like:

{% hint style="info" %}
**work\_mem**: It handles the _internal operations_ during **query execution**, such as sorting and hashing, applied to the data retrieved from disk tables.\
**Local Backend Memory**: It's dedicated to the current connected backend process, handling its session-specific data like the connection state or the private memory structure used for its query operations.
{% endhint %}

The Shared Buffer Cache stores **copies** of frequently accessed **disk pages** for both **tables** and **indexes** to minimize disk I/O. It tracks modified pages and writes them to disk during **buffer eviction** or a **checkpoint**.\
It optimizes the ANALYZE command's performance by minimizing slow disk I/O accesses and allowing it to retrieve the data needed for the operation directly from RAM.

Sequential read operations, such as VACUUM or VACUUM FREEZE, utilize the Shared Buffer Cache to retrieve and process pages, marking them as dirty when dead rows are removed. These operations protect the current set of active disk pages by relying on a specialized **Ring Buffer,** which uses a small, dedicated memory space to process the disk pages **without polluting the main** Shared Buffer Cache.

The Ring Buffer is a **Buffer Access Strategy** created by the Buffer Manager for **bulk operations** like ANALYZE.&#x20;It functions as a temporary, fixed-size **sub-cache** for operations that need to process many disk pages, like the _reservoir sampling_ for ANALYZE.\
It **differs** from the main Shared Buffer Cache in both **size** and **purpose**. Instead of preserving frequently accessed pages for long-term reuse, the Ring Buffer is designed to process pages and immediately evict them.\
Both mechanisms follow the same data persistence rule: a dirty page must be written to disk before it can be evicted. The difference lies in their **eviction algorithm**.

The Shared Buffer Cache relies on a Clock Sweep algorithm (an approximation of Least Recently Used/**LRU**) for its eviction mechanism. It tracks page access counts to identify and evict pages that are rarely used by database operations.\
It differs from the **FIFO** (First-In, First-Out) policy applied by the Ring Buffer strategy during commands like ANALYZE, which strictly recycles pages based on their entry order without checking usage frequency.

The ANALYZE operation first attempts a **Cache Hit** by searching for the disk page within the Shared Buffer Cache. If the page is not found (**Cache Miss**), it retrieves the data from disk via I/O using a Ring Buffer strategy to avoid polluting the main Shared Buffer Cache.

The ring buffer is a **circular data structure**, which effectively frees space by evicting (overwriting) the oldest already processed disk pages.\
It purges all the remaining values at the end of the reservoir sampling, ensuring they are not pushed into the main cache.

The ring buffer size optimizes the data management during ANALYZE, as it allows it to store more disk pages and reduces the need to use the eviction mechanism, minimizing stalling.

The ring buffer is not used for tables that are smaller than the shared buffer cache, as the database can read the data directly into the shared buffer cache without causing many LRU evictions.

```sql
// Some code
ANALYZE (BUFFER_USAGE_LIMIT 2MB) tavole;
```

1

1

1

1

1

1

1

1

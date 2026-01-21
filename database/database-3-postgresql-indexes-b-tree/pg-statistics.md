# PG statistics

* 1
* 1
* 1
* 1

The ANALYZE command updates the **pg\_statistic** and **pg\_class** system catalogs, which are used by the Query Planner to determine the **cost** of the chosen **execution plan**. The table rows processed by the _reservoir sampling_ store their column metrics in pg\_statistic to represent the table statistical properties:

{% hint style="info" %}
**Null\_Frac**: A value between 0 and 1 representing the proportion of NULL values present in the sampled table set.&#x20;

**n\_distinct**: The number of unique, non-null column values found in the sampled rows.&#x20;

**Average Width**: The average physical length (in bytes) of the column's values.&#x20;

**Correlation**: A value (between -1 and +1) that represents the physical correlation between the logical order of the rows and their physical location on disk, with 0 being random.

**most\_common\_vals**: An array that holds the Most Common Values (MCV) that appear in the rows. **most\_common\_freqs**: The parallel array that holds the fractional frequency (proportion) for each corresponding MCV value.&#x20;

**Histogram Distribution**: The distribution of the values that aren't part of the MCV set, which informs the planner about the data spread of the column values.
{% endhint %}

```sql
-- The ANALYZE updates the system catalog statistical information
CREATE TABLE tavole ( codice integer, padding text );

INSERT INTO tavole (codice, padding)
SELECT floor(random() * 100000) + 1, md5(random()::text)
FROM generate_series(1, 100000) AS i;

ANALYZE tavole;
-- pg_stats is the user-friendly interface to the pg_statistic system catalog.
-- Both the most_common and the histogram_bounds are arrays
SELECT 
    tablename, attname AS column_name,
    null_frac, n_distinct, correlation,        
    most_common_vals, most_common_freqs, histogram_bounds 
FROM pg_stats WHERE tablename = 'tavole' AND attname = 'codice';
```

— IMMAGGINE

Database statistics utilize specific system columns depending on the **data type** of the column being analyzed, like most\_common\_vals and most\_common\_elems.

<details>

<summary>VALUES ADN ELEMETS IN PG_STATISTICS and more pg_statiscst</summary>

Statistics for **values**, as scalar data types (like integers and text), are based on how frequently the entire **data cell** appears in the column. The statistics for **elements**, as complex data types (like ARRAY or JSONB), track how often the **internal items** appear inside the data structures across the table rows.

The pg\_stats view includes specific columns reserved for multidimensional data types, like the **most\_common\_elem\_freqs** and **elem\_count\_histogram**. These columns appear in the view's **output structure**, but remain empty (NULL) if the analyzed table column doesn't contain a complex data type.

The n\_distinct metadata value varies based on its mathematical sign. A positive value returns the number of unique column values in the table statistics, while a **negative value** represents the **ratio** of unique values to total rows (where -1 is 100%).

The query planner uses it to calculate **cardinality**, providing either a **precise count** of unique values or a **stable percentage** estimate as the table scales. It includes the total unique values in both the MCV list and the histogram.

An **equi-height** **histogram** contains a fixed number of values across all **buckets**, while the **width** defined by its boundaries varies. Each bucket represents an equal fraction of the table rows, accurately reflecting the data's density.

</details>

— CREATE TABS FOR PG\_CLASS AND

The ANALYZE command updates the metadata columns in the **pg\_class** catalog:

{% hint style="info" %}
**reltuples**: The estimated count of the live rows (tuples) in the table.

**relpages**: The estimated number of disk pages occupied by the table.&#x20;
{% endhint %}

The **pg\_statistic** data is stored in **binary** code. It's designed to be read by the **query planner** and requires an interpreter to be accessed. The **pg\_stats** is not a system catalog but a **system view**, a _virtual table_ created by a query. It formats the pg\_statistic data into a readable structure and joins columns from other catalogs, like **tablename** and **attname** (column name).

1

A single-value **equality clause** (=) calculates its **selectivity** by first checking the most\_common\_vals for the query value; if found, it returns the corresponding mcv frequency.                                                            The query planner estimates the **non-MCV** query value using the **residual frequancy** (using the formula 1 - the sum of all mcv frequencies), it then divides it by the number of remaining distinct values (subtracting the **mcv values** form the total **n\_distinct**), assuming a **uniform distribution**.                                                                               A **scalar range** (<, >) query analyzes the boundaries of the **histogram buckets** to detect which ones include the query values. The planner defines the specific range by using **linear interpolation** to "cut" the histogram bounds, in order to estimate the total frequency for the selected range.

The **frequency** and **selectivity** can share the same mathematical value, but they represent different data sources and stages within the **query planner process**.\
The frequency is a **raw value** stored in the **pg\_statistic** metadata columns. It represents the **physical proportion** of specific values within the table rows.\
The selectivity defines the **fraction** of rows selected by the **query clause**. The query planner calculates this value based on the frequency, but the result changes depending on the specific **query conditions** being estimated.\
The probability is the conceptual term we use to interpret the selectivity value.

1

The **query planner** compares all **paths** in the **plan tree**, calculating the cost of each physical operation based on its **selectivity**, **correlation**, and **average row size**.\
The **total cost** of an **execution plan** is determined by **data volume** and I/O efficiency. The planner calculates the volume by multiplying **selectivity row count** by the **avg\_width**. The **correlation** (ranging from -1 to 1) represents the **I/O cost**: high absolute values indicate ordered data suitable for index scans, while values near zero indicate scattered data that requires Bitmap or Sequential scans to avoid the random I/O cost.

The SELECT columns define the **output payload** and infleunce the query plan **cost** with their **avg\_width** value, while the WHERE clauses define the **estimation process** for the columns **selectivities**.\
The planner retrieves the selectivies for multiple query conditions (equality or scalar) from the MCV list and the histogram bins as non-overlapping data sources. It **multiples** the selctivities values for 'AND' conditions while it **sums** it for 'OR'.

The query planner uses the conditional **independence assumption** when evaluating plans involving **multiple columns**.\
It multiplies the **individual selectivity** of each column, assuming they are **unrelated**.                                    **Extended statistics** estimate the **functional dependencies** between correlated column values, which provides the ANALYZE process with additional data for a more accurate execution plan.

1

— EXTENDED STATISTICS

The CREATE STATISTICS command builds a **statistical object** for its specified table columns.                     It's not part of the standard pg\_stats view; instead it consists of multi-party entry distributed across two primary system catalogs **pg\_statistic\_ext** and **pg\_statistic\_ext\_data.**

The extended statistics metadata differ from the pg\_statistics. They cover **multivariate functional dependencies** and use them to enhance the standard query planner **estimation process**:&#x20;

{% hint style="info" %}
**Initial Estimation**: The planner generates an initial estimate using standard pg\_stats metadata.&#x20;

**Stat Lookup**: It checks the pg\_statistics\_ext catalog for the specific columns included in the query.

**Override**: If matching columns are found, the planner overrides the _initial estimate_ to produce a more accurate execution plan.&#x20;

The planner can still partially apply the extended statistics for **some matching columns** while treating the remaining ones as independent.
{% endhint %}

The CREATE STATISTICS command requires running ANALYZE to **populate** the pg\_statistics\_ext\_data catalog with the actual **processed information**.

```sql
-- We create a table with correlated values using a SUBQUERY
CREATE TABLE correlated_lab ( category_id int, sub_category_id int );
-- It inserts table column values in different stages of the query
INSERT INTO correlated_lab (category_id, sub_category_id)
WITH source_data AS (
    SELECT (random() * 9 + 1)::int AS cat_id  --The +1 is there to avoid values 0
    FROM generate_series(1, 100000)
)
SELECT 
    cat_id, -- The first column
    CASE 
        WHEN cat_id = 1 THEN 1       -- Functional dependencies for value 1
        ELSE (random() * 4 + 1)::int -- random values (1-5) for the rest
    END
FROM source_data;
-- It collects the statistics for the single columns
ANALYZE correlated_lab;

-- The query planner estimates the selectivity from the separate columns
-- The rows being 0.05496 * 0.17 * 100000 = 944
select most_common_vals, most_common_freqs from pg_stats where tablename = 'correlated_lab';
-- category_id|  1|0.054966666, sub_category_id|  1|     0.1718|

-- The indipendent assumption creates an innacurate rows estimation
-- It results in a incorrect cost estimate and a suboptimal query plan for the table values
EXPLAIN (ANALYZE) SELECT * FROM correlated_lab 
WHERE category_id = 1 AND sub_category_id = 1;
-- Seq Scan on correlated_lab (rows=944 width=8) (rows=5642)

-- We create an extended statistic for the two columns of the 'mcv' type
-- It requires ANALYZE to actually fill the columns statistics
CREATE STATISTICS shop_dependency (mcv) 
ON category_id, sub_category_id FROM correlated_lab;
analyze correlated_lab;

-- The query planner accesses the frequency for the {1, 1} column combination values
-- The table rows estimations 0.57 * 100000 = 5700
select most_common_vals, most_common_freqs from pg_stats_ext 
where statistics_name = 'shop_dependency' 

EXPLAIN (ANALYZE) SELECT * FROM correlated_lab 
WHERE category_id = 1 AND sub_category_id = 1;
-- Seq Scan on correlated_lab (rows=5700 width=8) (rows=5642)
```

-— Find a way to cerate teh doubfle colun explenation

The query planner is **data-blind** when calculating the total selectivity of the query's condition.               Both pg\_stats and pg\_stats\_ext rely on directly **matching raw query values** against MCV lists or histogram buckets.                                                                                                                                                The planner can't process **math operations**, **function calls**, or **type casting** within a query condition, as it would be too expensive to transform the values to match the pg\_stats metadata. It instead falls back on generic selectivity estimates, such as 0.005 for equality and 0.33 for scalar operations.

— It can work for single colun exprs --

<details>

<summary>Single column expression.</summary>

The CREATE STATISTICS command can generate **functional statistics** for **columns expressions**. The ANALYZE applies the expression to **every table row** and **maps** the results to a **virtual column**, which provides the planner with the actual data distribution and removes the need for guesses.

The functional statistics are stored separately from multivariate dependencies. They **don't include** metadata columns like as n\_distinct and dependencies, which are reserved for multi-column relationships; instead, they store their own MCV and histogram values in the **pg\_stats\_ext\_exprs** catalog.

```sql
-- We create a table with rows with sequantial values form 1-100000
CREATE TABLE expression_test AS SELECT generate_series(1, 100000) AS id;

-- It uses the generic selectivity 0.005
ANALYZE expression_test;
EXPLAIN ANALYZE SELECT * FROM expression_test WHERE (id % 100) = 42;
--Seq Scan on expression_test (rows=500 width=4) (rows=1000)
--  Filter: ((id % 100) = 42), Rows Removed by Filter: 99000 

CREATE STATISTICS expr_stat ON (id % 100) FROM expression_test;

-- It gets a closer rows estimation with the functional statistics 
ANALYZE expression_test;
EXPLAIN ANALYZE SELECT * FROM expression_test WHERE (id % 100) = 42;
-- Seq Scan on expression_test (rows=1007 width=4) (rows=1000)
--  Filter: ((id % 100) = 42), Rows Removed by Filter: 99000
```

1

</details>

```sql
-- The pg_stats_ext maps the boolean expression results not its table values.
CREATE STATISTICS singlecol_expr ON (sub_category_id < 3) FROM correlated_lab;
-- It collects statistics for the returned BOOLEAN data types 

//Put image returning
statistics_name|expr                 |null_frac|avg_width|n_distinct|most_common_vals|most_common_freqs      |histogram_bounds|
---------------+---------------------+---------+---------+----------+----------------+-----------------------+----------------+
singlecol_expr |(sub_category_id < 3)|      0.0|        1|       2.0|{f,t}           |{0.59026664,0.40973333}|                |
```

— IMMAGGINE

We add the IF NOT EXISTS clause to the CREATE STATISTICS command to prevent errors if an **object** with that **name** already exists. The clause triggers a **silent skip** based solely on the name, regardless of the columns or expressions used in the definition.

1

### Multi column dependency

The CREATE STATISTICS command creates **multivariate dependencies** when applied to multiple columns. The statistical object, stored in **pg\_statistic\_ext**, acts as a **flag**. It instructs the ANALYZE command to calculate **additional statistics** for those specific columns.                                                          The ANALYZE process maintains a **single scan** of the table data from the disk. It adds a simultaneous **second track** that analyzes the relationships between **column combinations** rather than treating them as separate and independent.

The **pg\_statistic\_ext** system catalog contains the columns **definition** of the statistics that appear in the pg\_stats\_ext view.

{% hint style="info" %}
**OID**: It's an unique identifier of the statistics object within the database\
**stxrelid**: The OID value of the **pg\_class** table described by the statistics object.\
**stxname**: It records the **object's name** in the CREATE STATISTICS command.\
**stxnamespace**: It identifies the schema (namespace) where the object resides.\
**stxowner**: The OID of the user managing the access.\
**stxkeys**: It's an array that contains the columns **positions attributes** of the statistics object as **indexes**.\
**stxkind**: A string that defines the different **types** of analysis enabled.\
**stxexprs**: It stores the **expression** used in functional statistics objects.\
**stxstattarget**: It defines the detail level of extended statistics compared to the individual columns they are composed of. A value of -1 inherits the highest **statistics\_target** from its component columns, while a value of 0 disables statistics collection for the object. A positive integer sets an **explicit limit** on the number of Multivariate MCV entries, regardless of individual column settings.
{% endhint %}

<pre class="language-sql"><code class="lang-sql">-- The null represents the default's -1 value
select * from pg_statistic_ext where stxname = 'shop_dependency';
--oid   |stxrelid|stxnamespace|stxowner|stxkeys|stxstattarget|stxkind|stxexprs|
<strong>--966703|  958515|        2200|      10|1 2    |             |{m}    |        |
</strong></code></pre>

The **pg\_statistic\_ext\_data** system catalog contains the statistical results of the extended statistical objects that appear in teh pg\_stats\_ext view.

{% hint style="info" %}
**stxoid**: The pg\_statistic\_ext OID value that originated the data.&#x20;

**stxdinherit**: A boolean value (default false) indicating the scope of the statistics, it's true for statistics that apply to an entire **inheritance tree** (the table plus all its children/partitions).&#x20;

**stxdndistinct**: It stores a serialized map of **N-distinct coefficents** for each unique column combination. It uses positive integers for the unique values and negative integers as a ratio relative to the total rows.&#x20;

**stxdependencies**: It stores the functional dependency score for all the current **column values combinations**.&#x20;

**stxdmcv**: It stores the MCV list, which tracks the most common columns combination values. It uses an **internal binary format** to store combinations that include multiple data types, which requires a set-returning function like pg\_mcv\_list\_items() to be accesses.&#x20;

**stxdexpr**: It's an array of pg\_statistic objects. It stores the complete statistics set for the objects defined on columns expressions, using a serialized format.
{% endhint %}

```sql
-- The populated columns depend on the types defined in the CREATE STATISTICS command.
-- Close the output viewer after acessing the extended data, as it might get stuck
select * from pg_statistic_ext_data where stxoid = 966703;
--stxoid|stxdinherit|stxdndistinct|stxddependencies    |stxdmcv|stxdexpr|
--966703|false      |{"1, 2": 46} |{"1 => 2": 0.056100}|\xc25..|NULL    |
```

We can use the **pg\_mcv\_list\_items()** function to acccess the pg\_statistics\_ext\_data mcv values in a **virtual table**.

<details>

<summary>JOIN LATERAL DETAILED ACCESS, CROSS JOIN mathematical operator unnest vertical logial</summary>

The **inspection functions** convert binary pg\_statistics\_ext\_data into a structured, **multi-row format**. It provides more detailed, readable data than the standard pg\_stats\_ext view.

The LATERAL JOIN allows the inspection function to unnest filtered binary data that matches specific WHERE conditions. It references data dynamically from **preceding tables**, unlike a standard JOIN which connects static data between independent columns.

The function unnests a single row into multiple table rows to create a detailed dataset. The **cardinality expansion** transforms binary data into vertical rows of structured output. We apply this method to complex data types to create a visible output for analysis.

```sql
-- The LATERAL keyword accesses the previos columns for the function argument (d)
-- The CROSS JOIN manages the vertical expansion that creates the virtual table
-- It accesses the multiple entries returned by the set-returning function 
-- It creates multiple table rows to store the different results

SELECT m.values, m.frequency, m.base_frequency 
FROM pg_statistic_ext_data d    -- The pg_statistic_ext identifies the mcv data
JOIN pg_statistic_ext s ON s.oid = d.stxoid
CROSS JOIN LATERAL pg_mcv_list_items(d.stxdmcv) m
WHERE s.stxname = 'shop_full_stats';

stxname        |values|frequency           |
---------------+------+--------------------+
shop_dependency|{1,1} |              0.0561|
shop_dependency|{9,4} |0.029133333333333334|
shop_dependency|{5,2} |0.028933333333333332|
shop_dependency|{3,4} |              0.0289|
shop_dependency|{9,2} |              0.0289|
shop_dependency|{2,2} |              0.0289|
shop_dependency|{9,3} |0.028833333333333332|
shop_dependency|{5,4} |              0.0288|
shop_dependency|{6,2} |              0.0288|
...
```

</details>

1

The **pg\_stats\_ext** view presents the binary content of pg\_statistic\_ext\_data in a readable format.      Its columns distinguish between the statistics object’s **identification** metadata and the processed **statistical payload**.

{% hint style="info" %}
**schemaname** / **tablename**: The logical schema and the specific table name referenced by the statistics object.                                                                                                                                               **statistics\_schemaname** / **statistics\_owner**: The schema and the database user who owns the statistics object.                                                                                                                                                          **statistics\_name**: It provides the unique object name assigned during the CREATE STATISTICS process. **attnames**: This array lists the specific table column names included in the statistics object definition. **exprs**: It contains the SQL expressions used for functional or expression-based statistics.                          **inherited**: A boolean value indicating if the statistics include data from child tables or partitions.                     **kinds**: This char\[] array lists the enabled statistic types: n-distinct (d), functional dependencies (f), most-common-values (m), or expressions (e).
{% endhint %}

```sql
-- The database fills the unusued statistics coluns with NULL
select 
    tablename, statistics_name,
    attnames, kinds, n_distinct,
    dependencies, 
    most_common_vals, most_common_freqs, most_common_base_freqs
FROM pg_stats_ext 
WHERE tablename = 'correlated_lab';
```

The extended statistics don't include histograms. They provide multi-column metadata, like **dependencies**, **mcv**, and **n\_distinct**.\
Multi-dimensional histograms aren't supported because they are too complex to generate during the ANALYZE process; extended statistics aren't designed to estimate selectivity for range operations, (like < or >).

{% hint style="info" %}
**n\_distinct**: It returns the estimated number of distinct **value combinations** for the specified columns, expressed as either a fixed integer or a ratio.&#x20;

**dependencies**: It lists the column **attribute index numbers** and a **score** between 0 and 1 that represents the level of correlation between its columns.&#x20;

**most\_common\_vals** / **most\_common\_val\_nulls**: These arrays contain the most **frequent combinations** of values for the specified columns and identify the NULL combinations.

**most\_common\_freqs** / **most\_common\_base\_freqs**: These columns report the **actual frequency** of each common value combination and the expected **frequency assumed** if the columns were independent. The **query planner** compares these values to detect real-world correlations.
{% endhint %}

<figure><img src="../../.gitbook/assets/statview1.png" alt="" width="563"><figcaption><p>The allora</p></figcaption></figure>

The **stxkind** **array** column identifies the different **types** of statistical processes run during ANALYZE. It acts as a 'multi-tool' map, indicating which **payload columns** are populated within the pg\_stats\_ext view.

Each statistic type has a specific role: **functional dependencies** estimate **selectivity** for equality operators, **n\_distinct** calculates **cardinality** for GROUP BY and ORDER BY clauses, and **MCV** is used for operations within **query conditions**.

1

### The statistical object types&#x20;

The functional **dependency** (stxkind 'd') measures the **logical correlation** between the **columns** defined in a&#x20;**statistics object**. It ranges from 0 to 1 and is derived from the data sample collected during ANALYZE.\
It's a value-agnostic score that tracks the **global dependency level** between columns, rather than specific value combinations like an MCV. It represents a functional rule that measures how consistently a value in the first column determines the value in the second.

The dependency score factors in **dataset 'noise'**, which represents value pairs that **contradict** the logical dependencies. The planner uses the score to weight its total selectivity estimate for the query condition, accounting for both **correlated** and **coincidental** table rows.

The total multi-column **selectivity formula** uses the **dependency score** to weight the functional dependency against the independent 'noise'.&#x20;It uses the _single-column selectivities_ from the pg\_stats mcv column.

```
//A dependency (d) of 0 means all columns are P(A)*P(B selectivity as independet)
//A dependency (d) of 1 signifies total correlation of columns between values as min(P(A), P(B))

The logical formula for the weighted slectivity calculation with the dependency is 

//The linear interpolation view of teh formulareflext the planner strategy
//It shows teh different selectivies the dependency creates a weighted average from

d * min(P(A), P(B)) + [(1-d) * P(A) * P(B)] //(Funtional dependnecy + indipendent assumptiopn)

//The factored formula used by teh postgreSQL uses P(A) as the common value
//The min(P(A), P(B)) is simplified as P(A) as in a functional dependency its teh first column to define the second column value
//This is the reason of the included overstimation in the formula which is preferredby teh planner to avoid the most complex retrieval processes in teh query plan reserved for smaller query executios
P(A) * (d + (1-d) * P(B))
```

The query planner uses the dependency formula only if the _MCV statistics are excluded_ from the extended statistics definition. The planner ignores the dependency estimation if the specific query value combinations appear in the MCV list.

```sql
-- A statistics object with more than 2 columns uses a greedy algorithm
-- Which lists the correlations and only uses the highest one in the formula
DROP STATISTICS shop_full_stats;

CREATE STATISTICS shop_full_stats (dependencies) 
ON category_id, sub_category_id FROM correlated_lab;
ANALYZE correlated_lab;

SELECT dependencies FROM pg_stats_ext s
WHERE tablename = 'correlated_lab' and statistics_name = 'shop_full_stats';
-- dependencies | {"1 => 2": 0.059133}|

-- The extract the single pg_stats columns selectivities
select most_common_vals, most_common_freqs
from pg_stats where tablename = 'correlated_lab';
--most_common_vals      | most_common_freqs
{3,2,5,9,7,6,4,8,1,10}|{0.11336,0.111,0.111,0.111,0.110,0.11076,0.1094,0.1091,0.0591,0.052}
{3,2,4,1,5}           |{0.2367,0.2330,0.2319,0.1775,0.12076}
-- The values being 0.11336666, 0.23193334 respectevly

-- It includes both the multi-column and the indipendent columns in its estimate
EXPLAIN analyze 
select * from correlated_lab where category_id = 3 and sub_category_id = 4;

-- The weighted selectivity formula
P(A) * (D + (1-d) * P(B))
(0.1133) * (0.0591 + (1-0.0591) * 0.2319) = 0.03141 * 100000 = 3141
-- Which is very close to the value estimated by the query planner
-- Seq Scan on correlated_lab  (rows=3144 width=8) (rows=2797 loops=1)|
--   Filter: ((category_id = 3) AND (sub_category_id = 4))
```

The functional dependency value describes the **structure**, not the content. Its formula, unlike MCV, doesn't depend on the query values combination.\
It instead acts as a **mathematical generalization** applied to the existing single-column statistics.        It eliminates the need to store a large list of common combinations, allowing the planner to estimate selectivity for less common values that do not appear in the MCV statistics.

1

1

1

1

1

1

1

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

The CREATE STATISTICS command builds a **statistical object** for its specified table columns.                     It's not part of the standard pg\_stats view; instead it consists of multi-party entry distributed across two primary system catalogs:

{% hint style="info" %}
**pg\_statistic\_ext**: It stores the object definition and metadata, which is used to identify the columns or expressions being tracked in the pg\_stats\_ext view.

**pg\_statistic\_ext\_data**: It stores the actual statistical results that populate the pg\_stats\_ext\_data view.
{% endhint %}

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

1

1

1

1

1

1

1

1

1

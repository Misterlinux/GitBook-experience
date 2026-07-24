# The BRIN index

* 1
* 1
* 1
* 1
* 1

1

The BRIN (**Block Range INdex**) organizes its data into a series of block range entries, each representing the summary of its **indexed column values**. It's a **mathematical summary** index optimized for speed and size.                                                                                                                                 It doesn't collect pointers for each of the table column values like B-tree, its entries only contain the **minimun and maximum values** of the data contained within its **disk pages set**.

The number of **table rows** included within each BRIN entry depends on the disk page data summarized by the **min-max range**. The BRIN **pages\_per\_range** attribute sets the number of disk pages represented by each range, and the entry's range **bound values** depend only on the **current table values** contained within that specific set of pages.

The database defines the BRIN index entries by **sequentially scanning** the table disk pages; it does not provide a built-in sorting support function, nor does it create an index structure that sorts the values into specific index page units like B-Tree or GiST.                                                                                     It is optimized for **data types that inherently follow a linear increase** and for tables whose data possesses a high statistical correlation.

The table's **correlation value** represents the relationship between the **physical position** and the **logical values** of the column data; this allows the BRIN index to define sequential and **non-overlapping ranges**, resulting in a more efficient scan during query execution.                                              The query planner uses the statistical correlation value to estimate the cost of a BRIN index scan, as it compares the **index overhead cost** against the number of disk pages that need to be scanned compared to a **sequential scan**.

The BRIN index creates a **lossy structure** where each entry summarizes a range of disk pages.\
The index scan returns the index position of the BRIN block, it then starts an **additional bitmap scan**\
on the physical **disk pages** part of the **range** and finds the matching query values.

```plsql
-- The BRIN index is optimized for small size, not for enforcing EXCLUDE constraints.
-- Its lossy structure can't confirm an exact match with a single index scan.
drop table t;
CREATE TABLE t ( id int, happened_at timestamptz );

INSERT INTO t ( id, happened_at )
SELECT n, '2023-01-01 UTC'::timestamptz + (interval '1 second' * n)
from generate_series(1, 1000000) as n;

SELECT count(*), max(happened_at) FROM t;  -- 1000000|2023-01-12 14:46:40.000 +0100|
CREATE INDEX brin_minmax ON t USING brin(happened_at);

-- The bitmap scan begins at the first disk page of the range, 
-- It finds it by multiplying the entry 
-- The precision of the Rows_removed_by_Index_recheck depends on the pages_per_range value
EXPLAIN (ANALYZE) SELECT * FROM t WHERE happened_at 
BETWEEN '2023-01-12 13:45:00 UTC' AND '2023-01-12 13:46:00 UTC';
-----------------------------------------------------------------------------------
Bitmap Heap Scan on t (actual time=0.577..0.589 rows=61 loops=1)                  
  Recheck Cond: ((happened_at >= '2023-01-12 14:45:00+01'::timestamp with time zone) ... 
  Rows Removed by Index Recheck: 5379; Heap Blocks: lossy=30
  ->  Bitmap Index Scan on brin_minmax (actual time=0.042..0.042 rows=300 loops=1)  
        Index Cond: ((happened_at >= '2023-01-12 14:45:00+01'::timestamp with time zone) 
        AND (happened_at <= '2023-01-12 14:46:00+01'::timestamp with time zone))
Planning Time: 1.567 ms                                                  
Execution Time: 0.614 ms
-- The postgreSQL returns 300 estimated rows
-- The bitmap heap scans 30 lossy pages and discards 5376 disk page rows
-- It returns 61 matching table rows.      
```

The **row** values in the BRIN index scan don't return disk pages directly. It's part of **PostgreSQL convention**, which counts every **lossy page as 10 rows**. The actual number of lossy pages and disk pages is resolved during the bitmap heap scan.

The BRIN index scan **optimizes different query conditions** applied to the same table.                                   It returns different index entries representing a **fixed pages\_per\_range** number of disk pages.               The **query planner overlaps** the results and triggers a bitmap heap scan only for overlapping values to find disk pages matching **both query conditions**.

The BRIN index is designed to handle table data stored in **linear order**, similar to a **B-Tree**.                            It **adapts its range** **entries** to the specific data types of the indexed columns, much like **GiST**, and is optimized for storage size using a **single-level lossy structure**, similar to a **Hash** index.

1

### BRIN index conditions

The **ANALYZE command** provides the necessary pg\_stats and pg\_stat\_statements **statistics**, which allow the query planner to accurately choose a **BRIN scan** over a sequential scan.

The **avg\_width** value returns the average size in bytes of the table rows. The planner uses it to estimate the number of **table rows** each **disk page contains**, which affects the CPU time necessary for the heap scan. It doesn't affect the BRIN index entry structure.

The **n\_distinct** value doesn't influence the BRIN index. A table with few unique values can create an efficient BRIN index where specific ranges are reserved for a single value.

The **correlation** value is the most important statistic for the BRIN index. A low correlation represents random values not part of any linear order, creating wider and less precise ranges.

```sql
-- The planner uses the BRIN index scan for the query executon based on the statistics
ANALYZE t;

SELECT correlation FROM pg_stats
WHERE tablename = 't' AND attname = 'happened_at';
-- correlation|  1.0|
```

The BRIN (**Block Range Index**) structure consists of a **single-level** index of disk pages.                                            The page 0 contains **metadata**, which holds high-level information like version, the pages\_per\_range value, and the last index disk page occupied by the revmap.

The **revmap** is an array of TupleID (TID) **pointers** referencing the disk pages that contain BRIN index tuples. The **execution engine** sequentially groups table **disk pages into ranges** based on the **pages\_per\_range** parameter and maps them using the revmap. It then retrieves the **min/max values** for each range and compares them to **query conditions**. If a match is found, the entire page range is marked in the **memory bitmap**.

The revmap contains range values but not the actual disk pages they reference, while the engine has the disk page groups but can't read min/max values directly from data pages.

1

### Pageinspect

The **pageinspect** extension provides functions to access the **physical properties** of **BRIN index** disk pages. The **get\_raw\_page()** function returns the **binary data** of a specified BRIN index page, while **brin\_metapage\_info()** extracts specific **metadata** values from the index's entry.

```sql
-- The second argument indicates the index's disk block number.
-- The lastrevmappage indicates the last disk block before the actual tuple data storage
-- The brin_ functions are exclusive to the BRIN disk page data.
CREATE EXTENSION pageinspect;
SELECT * FROM brin_metapage_info(get_raw_page('brin_minmax', 0));
magic     |version|pagesperrange|lastrevmappage|
0xA8109CFA|      1|          128|             1|
```

The **brin\_page\_items()** function converts BRIN index entries into readable summary data.                                It requires the binary data of the specific disk page and the name of the BRIN index. The function uses the index's operator class to decode the disk page binary data.

```sql
-- The BRIN operation class brin_minmax_ops is a support funtion
-- It defines how the index resumes data, using min/max bounds values
SELECT blknum, value
FROM brin_page_items(get_raw_page('brin_minmax', 2), 'brin_minmax') ORDER BY 1 LIMIT 3;
blknum|value                                             |
------+--------------------------------------------------+
     0|{2023-01-01 01:00:01+01 .. 2023-01-01 07:34:40+01}|
   128|{2023-01-01 07:34:41+01 .. 2023-01-01 14:09:20+01}|
   256|{2023-01-01 14:09:21+01 .. 2023-01-01 20:44:00+01}|
-- The disk pages aftr the lastrevmappage index contain the min/max values
```

The CREATE INDEX command includes both options and storage parameters that define the index entry **data format** and the **precision** of the BRIN index.                                                                                              The **operator class option** overrides the **default** operator class assigned by **PostgreSQL** to the indexed data type (such as int4\_minmax\_ops and int4\_bloom\_ops).                                                                  The **pages\_per\_range parameter** (default 128) indicates the fixed ratio of disk pages that each BRIN index range will summarize.

```sql
-- The index also includes default operation classes for non-linear data types 
-- The WITH() indicates the storage parameters 
-- Only multi-minmax and bloom operation classes include storage parameters
CREATE INDEX brin_minmax ON t 
USING brin (happened_at) WITH (pages_per_range = 10); -- 32kb

-- A higher pages_per_range creates a more lossy but smaller BRIN index
-- It results in less but larger ranges that require longer bitmap heap scans.
EXPLAIN (ANALYZE) SELECT * FROM t WHERE happened_at 
BETWEEN '2023-01-12 13:45:00 UTC' AND '2023-01-12 13:46:00 UTC';
--------------------------------------------------------------------------------------------
Bitmap Heap Scan on t (actual time=0.229..0.242 rows=61 loops=1)            
  Recheck Cond: ((happened_at >= '2023-01-12 14:45:00+01'::timestamp with time zone) ...
  Rows Removed by Index Recheck: 939 Heap Blocks: lossy=6
  ->  Bitmap Index Scan on brin_minmax (actual time=0.131..0.131 rows=60 loops=1)
        Index Cond: ((happened_at>= '2023-01-12 14:45:00+01'::timestamp with time zone) AND 
        (happened_at <= '2023-01-12 14:46:00+01'::timestamp with time zone))
Planning Time: 1.382 ms Execution Time: 0.270 ms   
```

1

### BRIN INDEX MAINTENANCE AND ARGUMENTS

The BRIN index doesn't store **TID pointers** for **each** of its indexed **column values**, like HASH or B-tree.&#x20;It's instead organized in ranges, which react differently to different commands.

The DELETE operations follow a **lazy behavior**. The **index won't update** the BRIN entry ranges,\
even if it deletes one of the **min/max bound values**. It would require a scan of every disk page within\
the specific range to find the next min/max value.\
The BRIN index instead maintains the entry ranges, resulting in a lossy and less precise structure,\
requiring **maintenance operations** to recalculate the bounds.

The INSERT operations lazy behavior depends entirely on its new **table row block number**.                       The PostgreSQL stores the new INSERT value in a **disk page** and returns the page (or block) value (B) of its **position in the table file**. It then uses the pages\_per\_range BRIN ratio to **calculate the range** the disk page is part of (**B/pages\_per\_range**). The PostgreSQL accesses the BRIN **revmap** and compares the entry range min/max values with the new table row.

If the disk page storing the new value is part of a **pre-existing range** and it exceeds the min/max values,&#x20;it will trigger the BRIN index to **change its current bounds**.\
If the disk page storing the new value is **not part of a pre-existing range**,&#x20;it will be marked as **InvalidItemPointer** and require an additional sequential scan for every query execution&#x20;involving its table row values.                                                                                                                                             The BRIN index follows lazy behavior for **unsummarized table row values** and maintains them until the maintenance&#x20;operations are run.

The **maintenance operations** are database tasks that optimize the **index metadata**.\
They are designed to apply to unsummarized data or recalculate BRIN entry ranges.&#x20;They can be defined during the initial CREATE INDEX command or applied to an existing index.\
The **autosummarize** option triggers automatically once a new disk page of unsummarized table rows is inserted.

```sql
-- Its set to "off" by defailt
CREATE INDEX brin_minmax ON t 
USING brin (happened_at) WITH (autosummarize = on); 

-- It can be aplied to an existing index, It's instant and doesnt require an index reset.
ALTER INDEX brin_minmax SET (autosummarize = on);
```

The BRIN index offers different maintenance operations for data produced by INSERT or DELETE commands.

The **summarize functions** apply to new INSERT **disk pages** marked as **InvalidItemPointer** by the revmap.&#x20;They calculate the **min/max values** for new range BRIN entries.&#x20;It's a local operation that can target specific disk pages or process all unsummarized disk pages&#x20;in the index.                                It **skips** all **unmarked disk pages**, resulting in a faster operation with minimal CPU cost&#x20;that doesn't lock the table file.

The REINDEX command is designed for BRIN **index ranges** that have become **lossy** due to DELETE operations&#x20;or corrupt index entries.&#x20;This is a global operation involving the entire index.\
It deletes the **current index**, accesses disk pages from the table file, and **recalculates BRIN index**\
**ranges** and their min/max values. This results in a slower operation with high I/O load.

```sql
-- It requires the BRIN index name
SELECT brin_summarize_range('my_brin_index', 10);  -- Summarize page 10
SELECT brin_summarize_new_values('my_brin_index'); -- Summarize all new values

-- If aplied to a table, it re-builds all its correlated indexes
REINDEX INDEX my_brin_index;       -- Reindex a specific index
REINDEX TABLE my_table;            -- Reindex all indexes on a table, including BRIN
```

1

The **efficiency** of a BRIN index doesn't depend entirely on correlation statistics, but also on the min/max&#x20;values contained by its ranges.\
A series of **outlier values** will force the boundary values to expand to include them,\
resulting in a lossy and imprecise BRIN index.

```sql
-- We create a copy of the t table with built-in outliers values
CREATE TABLE t_outliers AS
SELECT
    id,
    CASE
        WHEN id % 70 = 0
        THEN happened_at + INTERVAL '1 year' -- Outlier Value for each 70 rows
        ELSE happened_at
    END
FROM t ORDER BY id;
SELECT count(*), max(happened_at) FROM t;	-- 1000000	2023-01-12 13:46:40+00
-- The outlier values mostly don't affect the correlation
ANALYZE t_outliers;
SELECT correlation FROM pg_stats
WHERE tablename = 't_outliers' AND attname = 'happened_at'; -- correlation|  0.97056246|

CREATE INDEX t_outliers_minmax ON t_outliers
USING brin(happened_at) WITH (pages_per_range=10);

-- It results in a much slower query execution output (35ms)
EXPLAIN (ANALYZE) SELECT * FROM t_outliers
WHERE happened_at BETWEEN '2023-01-02 13:45:00 UTC' AND '2023-01-02 13:46:00 UTC';
-----------------------------------------------------------------------------------------
Bitmap Heap Scan on t_outliers  (rows=57 width=45) (actual time=35.457..35.555 rows=60)   
  Recheck Cond: ((happened_at >= '2023-01-02 14:45:00+01'::timestamp with time zone) ...
  Rows Removed by Index Recheck: 136710 Heap Blocks: lossy=1410
  ->  Bitmap Index Scan on t_outliers_minmax  
  			(actual time=0.542..0.543 rows=14100 loops=1)                                  
        Index Cond: ((happened_at>= '2023-01-02 14:45:00+01'::timestamp with time zone) AND 
        (happened_at <= '2023-01-02 14:46:00+01'::timestamp with time zone))
Planning Time: 0.114 ms Execution Time: 35.586 ms 
```

1

1

The PostgreSQL multi-range operator classes are designed to handle outlier values in the BRIN ranges.&#x20;It creates more complex ranges, made of multiple ranges and single values, which provide precise min/max bounds

```sql
-- The multi-range operation class needs to be explicitly set in the options
-- It results in a bigger index (24kb vs 120kb) 
CREATE INDEX t_outliers_minmax ON t_outliers
USING brin(happened_at timestamptz_minmax_multi_ops)
WITH (pages_per_range=10);

EXPLAIN (ANALYZE) SELECT * FROM t_outliers
WHERE happened_at BETWEEN '2023-01-02 13:45:00 UTC' AND '2023-01-02 13:46:00 UTC';

Bitmap Heap Scan on t_outliers (actual time=3.470..3.686 rows=60 loops=1)
  Recheck Cond: ((happened_at >= '2023-01-02 14:45:00+01'::timestamp with time zone) ...
  Rows Removed by Index Recheck: 910 Heap Blocks: lossy=10
  ->  Bitmap Index Scan on t_outliers_minmax (actual time=3.400..3.400 rows=100 loops=1)
        Index Cond: ((happened_at >= '2023-01-02 14:45:00+01'::timestamp with time zone) 
        AND (happened_at <= '2023-01-02 14:46:00+01'::timestamp with time zone))|
Planning Time: 1.817 ms Execution Time: 3.723 ms 
```

The minmax-multi operator class adds multiple fields to the range definitions.\
The nranges value is the number of ranges needed to represent the data contained within a BRIN entry summary.&#x20;The nvalues is the number of outlier values stored within the range, while maxvalues\
represents the reserved slots used to store the range boundaries.

```sql
-- It detects outliers values and group them in a separate object
-- It only uses the resulting subranges for the ranges bounds 
SELECT blknum, value
FROM brin_page_items (get_raw_page('t_outliers_minmax', 2), 't_outliers_minmax')
ORDER BY 1 LIMIT 3;

-- The default maxvalues is 32
-- It sums the single values (14) and counts the ranges as 2 values (2*2)
{{
	nranges: 2  nvalues: 14  maxvalues: 32 
	ranges: {
		"2023-01-01 13:07:31+01 ... 2023-01-01 13:12:39+01",
		"2023-01-01 13:12:41+01 ... 2023-01-01 13:23:40+01"
	} 
	values: {
		"2024-01-01 13:08:00+01",
		"2024-01-01 13:09:10+01",
		"2024-01-01 13:10:20+01",
		"2024-01-01 13:11:30+01",
		"..."
		"2024-01-01 13:23:10+01"
	}
}}
```

The values\_per\_ranges parameter is exclusive to multi-range operation classes. It defines the maxvalues value which controls the precision level with the ranges.

```sql
-- Its part of the CREATE INDEX operation class options
-- The maxvalues can have a minimun of 8 and maximum of 256
-- It creates a smaller index (72kb)
DROP index t_outliers_minmax;

CREATE INDEX t_outliers_minmax ON t_outliers
USING brin(happened_at timestamptz_minmax_multi_ops(values_per_range=8))
WITH (pages_per_range=10);

SELECT blknum, value
FROM brin_page_items(get_raw_page('t_outliers_minmax', 3), 't_outliers_minmax')
ORDER BY 1 LIMIT 3;

{{
	nranges: 2  nvalues: 2  maxvalues: 8 
	ranges: {
		"2023-01-02 03:08:11+01 ... 2023-01-02 03:24:19+01",
		"2024-01-02 03:10:20+01 ... 2024-01-02 03:23:10+01"
	} 
	values: {"2024-01-02 03:09:10+01","2024-01-02 03:24:20+01"}
}}
```

1

The multi-range structure depends on maxvalues and the INSERT order of the table row values. The values\_per\_range option limits the number of available slots to describe each BRIN range. The PostgreSQL follows a lazy approach and triggers a merging process only when the total number of subranges and single values exceeds maxvalues/2, which allows the range to maintain available space for successive large inserts.

The multi-ranges entry follows a specific pipeline for its values, which is completely dependent on their INSERT order. The PostgreSQL first checks for BRIN ranges that include the new value. If the value is adjacent to the current range min/max value, it includes it in the range and expands its bounds. If the value is not part of any range, it puts it into the values array. Once merging is triggered, it checks the distance between subranges to merge. In case of a tie, it follows the scanning direction.

```sql
-- The multi-range entries structure follows the values_per_range option
-- The RAISE NOTICE message returns the current range structure for each array value
-- The TRUNCATE command is aplied once and deletes all previous table rows before the loop
-- It follows the scans direction to form 3..4 range before the 1..2 range
CREATE TABLE t_brin (n int);

CREATE INDEX t_brin_index ON t_brin
USING brin(n int4_minmax_multi_ops(values_per_range=8)) WITH (pages_per_range=2);

DO $$
DECLARE
    n integer; page_items text := '';
BEGIN
    TRUNCATE t_brin;
    FOREACH n IN ARRAY array[5, 1, 10, 4, 2, 8, 3, 7, 9, 6] LOOP
        INSERT INTO t_brin(n) VALUES (n);
        SELECT value INTO page_items
        FROM brin_page_items(get_raw_page('t_brin_index', 2), 't_brin_index');
        RAISE NOTICE 'inserted=% --> %', n, page_items;
    END LOOP;
END $$;
```

\--> PUT THE TWO IMAGES

1

1

1

```sql
-- The index sizes and query execution times for teh different indexes
EXPLAIN (ANALYZE) SELECT * FROM t WHERE happened_at 
BETWEEN '2023-01-12 13:45:00 UTC' AND '2023-01-12 13:46:00 UTC';

CREATE INDEX brin_minmax ON t USING brin(happened_at);
-- Size: 24kb Planning Time: 0.119 ms  Execution Time: 1.731 ms
CREATE INDEX btree_tindex ON t(happened_at);
-- Size: 21Mb Planning Time: 0.112 ms  Execution Time: 0.034 ms 
create index gist_minmax on t using GIST(happened_at)
-- Size: 60Mb Planning Time: 0.091 ms  Execution Time: 0.094 ms
create index hash_minmax on t using hash(happened_at)
-- Size: 32Mb Planning Time: 0.076 ms Execution Time: 203.140 ms  
```

1

1

1

1

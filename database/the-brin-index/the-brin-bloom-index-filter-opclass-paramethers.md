# The BRIN bloom index filter opclass paramethers

* 1
* 1
* 1
* 1

1

The BRIN (Block Range Index) index access method manages the disk page data represented by the index entries. It's part of the CREATE INDEX storage parameters that define the index's physical properties.

The BRIN index framework is data-agnostic and relies on the operator classes to interpret the table rows data type. The PostgreSQL assigns a default BRIN operator class for each data type and the operator classes apply their specific support functions.

The minmax operator returns a single min/max range. It summarizes linear data types, including text (sorted lexicographically).&#x20;

The inclusion operator returns a union value. It summarizes the non-linear, non-sortable table rows into a single value . It's optimized for complex data types that cant be resumed into a single range.

The BRIN index acts as a generic framework, composed of independent ranges that aren't sorted by the index itself, like b-tree. It stores its entries as a single-level&#x20;array of disk pages that represent the current position of the table's data.

```sql
-- The operation class is defined after the table row name
CREATE INDEX t_outliers_minmax ON t_outliers 
USING brin(happened_at timestamptz_minmax_multi_ops);

-- The minmax operation classes are reserved for scalar data types
-- Their comparison operator can handle specific query conditions (=, <, <=, =, >=, >).
-- It follows the dataType_minmax_ops syntax 
Integers/floating_point: int4_minmax_ops, float8, numeric, etc.
Time: date_minmax_ops, timetz, timestamp, internal, etc. 
Text/identifiers: char_minmax_ops, name, iod, tid, etc.
Binary/Bit:	bytea_minmax_ops, bit, varbit

-- The inclusion operator classes are reserved for geometry data types.
-- The syntax is DataeType_inclusion_ops and it handles (<<, @>, &&) query conditions.
Box: box_inclusion_ops, inet (internet address, cannot use the contains operator @> )

-- The range data types are assigned inclusion operation classes by default
-- They can handle both sorting and spacial query condition
Ranges: range_inclusion_ops, range_minmax_ops
```

The range\_inclusion\_ops operator class is shared between multiple range data types. It's a polymorphic framework of C functions. It scans and extracts the data type, and applies its corresponding set of support functions. It returns a BRIN index range compatible with overlay and containment conditions (@> or &&).

The inclusion operator class is applied to ranges by default. The range\_minmax\_ops operator class treats ranges as individual blocks defined by their bounds; two ranges with the same bounds values are considered equal. The (<) and (>) operations compare ranges starting from the lower bound value and then the upper bound.

The Bloom operation class is part of the same BRIN framework used by the range. It includes an additional c function that converts the disk pages values into a bitmask.

1

### BLOOM INDEX

1

The Bloom operator class replaces the min/max range entry with bitmasks hash. A probabilistic structure designed to enforce strict equality. It's compatible with data types that support bitmap hashing like scalar data types.

The **Bitmap bit-size** (m) is calculated using the formula:

```sql
-- The N represents the n_distinct values contained in the bitmask
-- The P is teh false_positive_range which sets the bitmask precision 
m = - (n * ln(p)) / (ln 2)^2
```

1

The BRIN index Bloom entries are compatible with the pageinspect functions.

```sql
-- The pageinspect returns different data structures based on the operation class
create table testo( numero int4, padding text );
insert into testo 
select i, md5(i::text) from generate_series(1, 100000) as i;

-- The default int4_minmax_ops returns a series of BRIN range entries
create index testo_brin on testo using brin (numero)

SELECT itemoffset, blknum, value 
FROM brin_page_items(get_raw_page('testo_brin', 2), 'testo_brin');
-- itemoffset|blknum|value            |
-------------+------+-----------------+
--          1|     0|{1 .. 15360}     |	2|   128|{15361 .. 30720} |

-- The int4_bloom_ops returns an object with the bitmask properties
create index testo_brin on testo using brin (numero int4_bloom_ops);
itemoffset|blknum|value                                                       |
----------+------+------------------------------------------------------------+
         1|     0|{{mode: hashed  nhashes: 7  nbits: 35696  nbits_set: 33863}}|

-- The nbits_set/nbits ratio is higher than 50%, making it prone to false positives
-- It indicates a saturated bitmask where the number of n_distinct exceeds its bit length
```

The pageinspect returns the **Bitmask properties** for each BRIN range entry:

> The **nbits** column returns the total length of the bitmask in bits.
>
> The **nbits\_set** column returns the number of active (set to 1) bitmask bits.
>
> The **nhashes** column returns the number of hashing equations applied to each table row; it reflects the **precision level** of the bitmask filter.
>
> The **false\_positive\_rate** defines the size of the bitmask, which in turn defines the nhashes value.

```sql
-- It uses this formula to calculates the nhashes 
nhashes = (m/n) * ln(2)    -- m: nbits size, n: n_distinct_value
-- The n_distinct_values_per_range is defined by the Opclass paramethers
```

1

### Applyin g teh Bloom opclass pasramethers

1

The BRIN index Bloom **OPclass parameters** configurate the index's estimated **precision** and number of **distinct values**. It defines the data structure of the **bitmasks** for the ranges of **disk pages** set by the BRIN storage paramethers.

The **n\_distinct\_per\_range** parameter specifies a user-estimated number of distinct values within each **block range**. It's part of the Bloom support function that defines the **bitmask length** and the total BRIN index size. A positive number indicates a count of the distinct values, while a negative value (down to -1) represents a percentage of unique values relative to the total table rows in the range's disk pages. It's set by **default** to **-0.1**, which assumes that 10% of the values are unique. The Bloom support function **can't access** the system **view statistics**; the n\_distinct\_per\_range is independent from the standard n\_distinct system statistic.

The **false\_positive\_rate** parameter defines the index's false positive threshold and is part of the Bloom support function that defines the bitmask size. Its **default** value is **0.05**.  It mathematically sets the number of **hash functions** applied for each indexed **table row value** (nhashes). Each returns a different bit in the bitmask, and they generate a unique bit signature. The nbits\_set counts all the bitmask bits triggered by the indexed table rows.

1

```sql
-- We re-create the table, with unique padding but 100 (100000/1000) unique numero values
drop table testo;
create table testo( numero int4, padding text )

insert into testo 
select i/1000, md5(i::text) from generate_series(1, 100000) as i;

-- A lower number of indexed unique table rows doesn't modify the nhashes nor the nbits
-- It results in a lower nbits_set value 
create index testo_brin on testo using brin (numero int4_bloom_ops);
itemoffset|blknum|value                                                     |
----------+------+----------------------------------------------------------+
         1|     0|{{mode: hashed  nhashes: 7  nbits: 35696  nbits_set: 112}}|
```

1

<details>

<summary>How the Bloom OPclass paramethers modify the bitmask properties</summary>

The Bloom opclass parameters define the **static structure** of the **bitmask**, like its size and its behavior&#x20;rules for the filter. It sets the **nbits**&#x20;array size and the number of **hash functions** (nhashes) applied to indexed table rows.

The **nbits\_set** represents the **runtime bitmask** use. It tracks the number of&#x20;bits set to 1 by the hashed table rows within the BRIN range.&#x20;It varies depending on the number of **unique values** within its set **disk pages** and the nhashes count.

```sql
-- A lower precision lowers the bits part of the nbits_set and the nhashes
drop index testo_brin;
create index testo_brin on testo 
using brin (numero int4_bloom_ops (false_positive_rate = '0.15'));

itemoffset|blknum|value                                                    |
----------+------+---------------------------------------------------------+
         1|     0|{{mode: hashed  nhashes: 3  nbits: 14712  nbits_set: 48}}|
         
-- A closer n_distinct_per_range to the actual table rows lowers the nbits
create index testo_brin on testo using brin 
(numero int4_bloom_ops (n_distinct_per_range = 32, false_positive_rate = '0.15'));

itemoffset|blknum|value                                                  |
----------+------+-------------------------------------------------------+
         1|     0|{{mode: hashed  nhashes: 3  nbits: 128  nbits_set: 39}}|

-- A more small and imprecise bitmask makes the bitmap scan more complex
create index testo_brin on testo using brin 
(numero int4_bloom_ops (n_distinct_per_range = 32, false_positive_rate = '0.25'));

itemoffset|blknum|value                                                 |
----------+------+------------------------------------------------------+
         1|     0|{{mode: hashed  nhashes: 2  nbits: 96  nbits_set: 28}}|
```

</details>

1

The Bloom operator class parameters are part of the nbits size formula. The lower false\_positive\_limit is 0.0001 (0.01%); it can't be set to 0 as it is part of a **logarithmic operation**. Its upper limit is 0.25, as a higher value would make the BRIN index scan less efficient than a sequential scan.

The operator class **parameters** can't be changed after index creation; they are part of the **index metadata**. The ALTER INDEX command is limited to index storage options, like pages\_per\_range or autosummarize.

```sql
-- We apply the OPclass paramethers for the nbits formula
create index testo_brin on testo 
using brin (numero int4_bloom_ops (n_distinct_per_range= 64, false_positive_rate= '0.25'));
-- {{mode: hashed  nhashes: 2  nbits: 192  nbits_set: 29}}

nbits = -(n_distinct * ln(false_positive_range)) / (ln2)^2
-- It returns -(64 * ln(0.25)) / (ln(2)^2) = -(64 * -1.3862) / 0.48045 = 184.664
```

1

1

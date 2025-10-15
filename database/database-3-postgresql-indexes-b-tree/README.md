# Database 3:PostgreSQL Indexes, B-tree

1

1

An index is an additional **database structure** built with the specified columns of an existing table.

The index optimizes queries that reference its columns, which allows the query planner to avoid a **full table scan**.&#x20;The size and speed of an index depend on its design and the type of data it holds.

The CREATE INDEX command creates an index for its specified **table columns**.\
The <mark style="background-color:orange;">B-tree</mark> indexing method defines an index structure optimized for **equality** and **range** queries.

```sql
//Indexes are most effective on large datasets
CREATE TABLE tavole (
    id  SERIAL PRIMARY KEY, codice  INT
);

INSERT INTO tavole (codice, nome)
    SELECT FLOOR(RANDOM() * 10000) FROM GENERATE_SERIES(1, 10000);

//If no specified the database creates a B-tree index
CREATE INDEX indice ON tavole(codice);

//We disable the sec. scan to make the index be used by the query planner.
SET enable_seqscan = off;
SET enable_seqscan = on;

//Explain Analyze returns the scan stats
explain analyze select * from tavole where codice > 5000;
```

<figure><img src="../../.gitbook/assets/SequentialIndexScam.png" alt="" width="563"><figcaption><p>Sequential query scan and Bitmap index scan statistics</p></figcaption></figure>

The query planner uses an index if its **overhead cost** is lower than that of a sequential scan.\
It also depends on the query clauses (e.g., LIKE or JOIN), as some are not supported by certain index types.

An index can be **implicitly created** by a constraint and is **automatically deleted** when its associated table is dropped.

```sql
//The unique constraint implicitly creates a b-tree index
CREATE TABLE intervalli (
    inizio INTEGER, fine INTEGER,
    UNIQUE (inizio, fine)
);

//Equivalent to an EXCLUDE with extendable comparison conditions
CREATE TABLE intervalli1 (
    inizio INTEGER, fine INTEGER,
    EXCLUDE USING btree (inizio WITH =, fine with =)
);
```

The CREATE INDEX includes the **option** argument using the WITH keyword.\
It configures the **index access method** but offers a more limited set of available options compared to other index types.

```sql
//FILLFACTOR sets the percentage of a node that can be filled with entries.
CREATE INDEX my_index ON my_table (my_column) WITH (FILLFACTOR = 90);
```

The **placement** of the CREATE INDEX command influences the effect of certain options, as they are applied differently depending on whether the index is created on **existing data** or is filled later by INSERT **operations**.

```sql
//If placed after the INSERTs it will apply the FILLFACTOR option 
create table spazio( uno INT, due TEXT)

//It will populate the leaf nodes leaving a 30% empty dedicated for updates
create index spazio_idx on spazio(due) with (FILLFACTOR = 70);
insert into spazio select i, (md5(i::text),10) from GENERATE_SERIES(1,100) as i;  

//If before, it creates an empty index and treats any INSERT as a no FILLFACTOR update
create index spazio_idx on spazio(due) with (FILLFACTOR = 70);  
insert into spazio select i, (md5(i::text), 10) from GENERATE_SERIES(1,100) as i;
```

The CREATE UNIQUE INDEX command creates a B-tree that enforces uniqueness on its indexed column values. Some constraints, like UNIQUE, work by implicitly creating a B-tree index; the index command just explicitly creates the same structure to enforce the same rules.

A partial index is an index that contains only a subset of its indexed column values, created by adding a WHERE clause to the CREATE INDEX command.\
It's applied similarly to a CHECK constraint, but instead of blocking invalid data from entering the table, it limits which rows are included in the index, while allowing all data into the table itself.\
The smaller index allows for faster query scans and is affected by fewer table updates.

```sql
//ADD example here, not all values must be form the index for clause
//Only teh first value of teh indexes columns is allwed.
//Its usefull to limit teh valkues as non commons

```

1

1

A GiST (Generalized Search Tree) index organizes **complex** and **non-linear data** into a balanced tree.\
Its core algorithm acts as a flexible **framework**. It uses the operator class of the data type being indexed to define its internal logic and node structure.

The GIST index **access method** stores **multi-dimensional** data types, such as geometric data, images, arrays, timestamps, and full-text search strings. Its tree structure organizes the data based on **shared properties** specific to the indexed data type, resulting in a more complex index with slower operations.

```sql
//It can't be declared within a CREATE TABLE
//It provides multiple operator classes, each designed to index a specific data type
CREATE INDEX index_name ON table_name
USING gist (column_name);

CREATE INDEX text_search_idx ON documents
USING gist (to_tsvector('english', document_text));
```

It supports a wide range of specialized **comparison operators** for queries that access its indexed data types:

> Overlap (**&&**) and **containment** (**<@**, **@>**) for ranges values> \
> Is Adjacent To (**-|-**): Checks if two ranges touch at their boundaries without overlapping.> \
> Same As (**\~=**): Verifies if two geometric objects are identical.> \
> Distance (**<->**): Orders results based on their proximity to a target object.> \
> Strictly position (**<<** & **>>**): Checks if an object is positioned entirely to one side of another.> \
> Text Match (**@@**): Performs a full-text search on a document

The database first uses the **indexed columns** from the WHERE clause to retrieve the corresponding table rows. It then applies the remaining conditions to that resulting set of data.

The query planner creates its **excution plan** by estimating the most efficient WHERE clause condition.\
The ANALYZE command accesses the column data statistics from the **pg\_statistics** system catalog.

The **cardinality** property represents the number of unique values in a column. We calculate it using the **n\_distinct** and **MCV**(Most Common Values) statistics.

The **selectivity** property estimates the number of returned rows from a WHERE clause.\
The query planner uses the selectivity **estimation function**, specific to the comparison **operator**, to calculate the percentage of rows that will be returned by the query based on the column's cardinality.

```sql
//If multiple indexed columns, it will choose teh one with higher cardinality
//It wont accept the B-tree linear data types, for teh index creation, 
//If the specified daterange contains teh specified range, not just overlap,
-- Enable the trigram extension, which is needed for similarity searches
CREATE EXTENSION IF NOT EXISTS pg_trgm;

-- Clean up previous table if it exists
DROP TABLE IF EXISTS festivals;

-- Create the table, we are not searching for prefix searches, adapt to the B-tree
-- But for fuzzy name matching, we works with teh GIST or GIN and the extention.
CREATE TABLE festivals (
    id SERIAL PRIMARY KEY,
    nome TEXT, dates daterange
);

-- The difference between teh extention anme and teh specific operation class form it being aplied to teh GIST creation.
CREATE INDEX idx_festivals ON festivals USING gist (dates);
CREATE INDEX idx_nome ON festivals USING gist (nome gist_trgm_ops);

-- Insert data with some similar names
INSERT INTO festivals (nome, dates) VALUES
('Festive Summer', daterange('2025-08-01', '2025-08-10')),
('Summer Festival ',    daterange('2025-07-30', '2025-08-02', '[]')),
('Somber Festival', daterange('2025-08-03', '2025-08-05', '[]')),
('Sun Fest', daterange('2025-08-02', '2025-08-05', '[]'));

-- Analyze the table for up-to-date statistics
ANALYZE festivals;

-- symilar name, condition and overlap.for daterange
EXPLAIN ANALYZE
SELECT * FROM festivals
WHERE nome % 'Summer Fest' AND dates @> daterange('2025-08-02', '2025-08-05', '[]');
```

#### IMMAGGINE

//The combined bitmap scan of multiple indexed columns doesnt return the bitmap for each scan, nly the bitmap of the final bitmpa after the BITMAPAND operation.

The query uses a **combined bitmap** to find rows that match both the **trigram** and the date **contain** conditions. It merges the separate bitmaps created by the columns index scans and uses the result to scan the table heap.

<details>

<summary>1111</summary>

The combined bitmap is the product of the BitmapAnd operation and is processed in memory (RAM).

The database performs a logical AND operation between the bitmap location pointers.\
Both maps reference the same physical table, a pointer to a specific row or page identifies the exact same location in both. These shares locations are used to populate the combined bitmap.

The query planner creates the Recheck Condition directly from the original columns WHERE clauses. It aplies it for every row retieved by the combined bitmap.

The process still requires 2 series of I/O operations, the first for the index scans and the second for the table heap using the combined bitmap.

The BitmapAnd process can combine exact and lossy bitmaps; it uses the page value from the exact row pointers to confirm the lossy pages. The state of the combined bitmap will still depend on the total size of the shared pointers.

</details>

1

\\

\\

\\

1

### 1

### 1

### 1

1

### 1

### 1

1

1

1

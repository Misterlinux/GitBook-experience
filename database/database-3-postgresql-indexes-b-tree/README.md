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

### 1

1

1

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

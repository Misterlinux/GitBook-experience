# The HASH and the BRIN

* 1
* 1
* 1
* 1

The Hash Index access method aplies the **hashing function** to its **indexed columns values**.\
The **index entries** are composed of a **32-bit** value and a **TID pointer** to the actual data in the table heap.

The **hash index is data-agnostic**, it converts complex, non-linear data types into its **hash format**.              It contrast with B-Tree and GIST indexes, that store the actual column value and base their structure on its data type.\
This method is prone to **hash collisions**, where the function generates the same hash value for different column values.

It's optimized for **equality operations**, but due to the lack of linear order for its entries, it doesn't support range scans or sorting operations.

```sql
-- designed to index single table columns
-- The equality predicate wuld allow the hash to be used.equality comparison
-- INlcusing IN as its a series ofg =
-- It supports partial index conditions
create table cenere( name text, age INT )

create index cognome on cenere using hash(name);
insert into cenere(name) values ('car'), ('lontra'), ('ronin');
explain select name from cenere where name = 'car';

SELECT * FROM my_table WHERE column1 IN (1, 2, 3);
```

The **Hash index buckets** are organized by hash values, where the **32-bit hash** determines the entry's **index position**. This ensures that **identical data** always maps to the same bucket.

The TID **pointer** indicates the **row's physical disk location**. They allow the database to differentiate between multiple entries in a hash collision, where the same hash value is shared by entries that point to different values in the table heap.\
The database the stores the query condition in private memory and uses it to filter out 'false positives' from the query results by comparing the **query value** directly against the pointer retrieved **table data**

The hashing function is a **one-way process**, a 32-bit hash entry can't be converted back into the original row data.\
The database applies the hashing function to the **condition values** and it uses the resulting **hash code** to locate the **bucket** where the matching entries are stored.

1

1

1

— This processincludes hashing , not hash index

The database uses different memory data structures to handle **subquery operations**.                                 The **Hash Semi-Join** process collects and hashes the **subquery results** into a temporaru **work\_mem structure**, it's a 'semi-join' hash table because it **stores** only the **unique values** between the results. The database then **hashes** every **query column** value from the **main table** and compares it to the work\_mem hash table to **find matches** for the query output. This operation ignores the hash index and it's designed for **subqueries** returning a **large number** of values.

The **Nested Loop Join** triggers a hash index scan for each value returned by the subquery, it's suitable for subqueries that return a small number of results, as they dont justify the overhead cost of creating a temporary hash table. (so it creates a hash index for the subquery results?)

```sql
// Some code
-- The database choosesbetwene a Hash semi join ot 
SELECT * FROM my_table WHERE column1 IN (SELECT column2 FROM other_table);
```

1

The **Hash Index** doesn't implement any data-type-specific linear order, unlike B-tree or GiST indexes. Its **lossy structure** requires a **double equality operation** to find matching query values.\
The database hashes the query values and compares them to their corresponding hash index entries; it then follows the TID pointer to the table heap to verify the actual data. This second check is necessary to confirm that the table row has not been deleted and to avoid including collision values in the query output.

1

The Hash Index is **optimized** to store **large data values** because it converts all entries into a uniform 32-bit format.\
It's efficient for equality operations but, due to its lack of a tree-based structure (like nodes and branches), it can't enforce uniqueness. It would require accessing multiple TID pointers to verify existing values.\
The Hash Index logic is similar to a **HashAggregate**, as both use a hashing function to define the position of a column value. One locates the counter for a GROUP BY output result, while the other locates a bucket page location on the disk.

1

The Hash Index is organized into a **single-level** series of **bucket pages**. Its array structure allows for **direct access** to the data instead of traversing a tree structure like a B-tree or GiST.

All hash indexes include a **Metadata page** that tracks index statistics and manages the bucket data structures. It tracks the current number of bucket pages (**maxbuckets**), the number of entries in each bucket (**ntuples**), and the number of available **empty pages** used to handle overfilled buckets (the overflow free pages).

The Hash Index doesn't store a Free Space Map (FSM) file to map empty space within index nodes. It creates a **map of storage addresses** for all available overflow pages, used to handle the avaiable bucket space.

\--

The **separate chaining** **strategy** defines how the hash index buckets handle entry inserts that **exceed** their **storage capacity**; this differs from how leaf nodes split to handle index growth.\
The 8KB hash bucket appends an **overflow page** to include new entries. It uses a **pointer** to the physical location of the new page, which the database uses to **insert and retrieve data** during query executions. The **overflow pages** are part of a **local process** that handles a single bucket exceeding its storage capacity.

\--

The **Hash Split** instead refers to a **global process** triggered when the overall **index density** exceeds the defined **fillfactor**. The database determines the initial **number of buckets** using ANALYZE **statistics**, specifically the estimated row count (**reltuples**) and the average column width (**avg\_width**).&#x20;When the average number of hash entries exceeds the estimated **Load Factor**, the index doubles its bucket count by splitting existing buckets into two.

The **Buckets** represent the **logical units** in which the hash entries are organized. Their logic is defined by a **mathematical algorithm** that sets the entries' bucket position within the single-level hash index array.\
The **Page** represents the **physical 8KB storage unit** containing the hash entries; a single logical bucket can consist of entries stored within its primary bucket page and its overflow pages.

```sql
-- It aplies mathematical partitioning for the location, 
-- whcih removes the need for index tree navigaron in its single level arrays of buckets 
-. aplied to all hash index values to ensure even representetion
<CODICE>
```

The Buckets define the logical location of entries. The database applies the **MOD algorithm** to the entry's hash value and the current number of buckets to define the entry's bucket location.\
The **Linked List** represents the physical structure of how data is stored within the buckets; it includes all overflow pages appended to the bucket along with their values and entries.

— maintenance --

The **database** applies **maintenance operations** during both the hash index scan and the VACUUM operation.\
**Hash entries** pointing to **deleted table heap values** are marked as LP\_DEAD, which allows subsequent execution scans to **ignore** the entry.\
A **hash bucket** that exceeds its **fillfactor size limit** triggers a local VACUUM operation; it scans every hash entry, checks its corresponding table heap value, and frees space by **removing outdated entries**.

The **overflow pages** that become **empty** after maintenance are preserved, and their addresses are organized into the **overflow free map**, which allows them to be reused for different buckets during future data inserts.

1

The Hash and B-tree index structures adapt differently to **queries** that include **repeated values** or **long entry values**.

The Hash index uses a single-level structure for its **direct data mapping**.\
It's not designed to handle a series of repeated values, as duplicates generate identical hash entries which must share the same bucket. The **Hash index can't distribute** these values between **different buckets** because the mathematical algorithm governing the logical bucket order is fixed; this creates an inefficient linked list of overflow pages for execution scans.\
The hashing function allows the index to efficiently handle a series of **large insert values** due to its fixed 32-bit format. The index maintains its single-level structure, which requires only a single I/O access even if it exceeds the available RAM and is stored on disk.

The B-tree index uses a tree-like structure for its nodes.\
It includes optimization strategies to handle **repeated values**, such as **key deduplication** and horizontal access to leaf node entries. The index adapts its structure by **splitting data** across multiple **nodes** and maintains high index scan efficiency.\
The leaf node entries are limited to 1/3 of the disk page size, which makes the B-tree inefficent for storing large entries. An index that exceeds the available RAM **spills its nodes onto the disk**; causing every additional levels of the tree stored on the disk require an additional I/O access, making traversal less efficient.

```sql
// why we define handle, does it refer to both insert and retreave repeated values
-- we compare the timing they require to insetrs require for both to insert 
-- teh different type of table column data.
-- The function generating the long TEXT table column values
CREATE FUNCTION mkbig(integer) RETURNS text
   LANGUAGE sql as 
'SELECT repeat(md5($1::text), 20)';
 
drop table hashtest;
CREATE TABLE hashtest ( c2 bigint, c3 text );

-- We test both the generate_series() INSERT for the indexes
-- And the SELECT queries for the index entry values
-- One index at the time
drop index hashed;
create index hashed on hashtest using hash(c2); -- We test c2/c3 for each index in order 
create index treetest on hashtest using btree(c2);

-- It inserts both a repeated BIGINT, due to the truncated decimal value
-- And really long TEXT values created with the mkbig() function
INSERT INTO hashtest
SELECT i / 10000, mkbig(i)
FROM generate_series(1, 1000000) AS g(i);
-- Hash index c2 BIGINT value, 27s 46MB index size
-- Btree index c2 BIGINT value, 29s 6.3MB index size
-- Hash index c3 TEXT value, 23s 36MB index size
-- Btree index c3 TEXT value, 59s 96MB index size

VACUUM (ANALYZE) hashtest;

-- We retrieve a series of values from the table
-- We specify the condition based on the current indexed column
DO
$$DECLARE
   i bigint; r text;
BEGIN
   FOR i IN 1..100000 LOOP
      SELECT * INTO r
      FROM hashtest
      WHERE c3 = mkbig(i / 100);  -- We use the i/100 for c2 condition
   END LOOP;
END;$$;
-- Hash index on C2 BIGINT values return 6.294s
-- Btree index on C2 BIGINT values returns 0.677s
-- Hash index on C3 TEXT values returns 1.537s
-- Btree index on C3 TEXT values returns 4.346s
```

We test the Hash and B-Tree **index growth rates** using a serial query loop that INSERTS both unique (UUID) and duplicated values.

The **B-Tree** index structure **grows linearly**; the B-Tree adapts to increased data by adapting its node structure through splitting. It also optimizes for repeated values by using key deduplication.                        The **Hash** index presents a **'stair-step' growth** rate as it increases in size when a specific data threshold is met and the **buckets double**. The hash structure isn't optimized for repeated values; the size increases represent the creation of overflow page linked lists for buckets containing duplicate entries.

The **fillfactor** threshold defines the number of **entries** required to trigger a **node** or **bucket split**.              The B-Tree has a default fillfactor of 90%, while the Hash index defaults to 75%. A higher fillfactor allows for more **efficient storage** (for read-only data), whereas a lower value leaves more free space within nodes or buckets to allow for **easier updates** without restructuring the index.

The uuid\_generate\_v4() function creates a Universally Unique Identifier (UUID)—also known as a GUID in Microsoft environments. It is a **128-bit identifier** represented by 36 characters (including dashes), 32 of which are hexadecimal digits.

```sql
-- They follow the structure 8-4-4-4-12, 4bit digits for 32 digts making 128 bits.
-- UUID a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11
-- gen_random_uuid()

-- We raise an output print for each 10kto monitor ths apce eahc index occupies
-- We us eteh to_char() for the text result while teh use pg_size_pretty() for the bytes translate teh more readable.
The RAISE NOTICE is triggered once for each time teh counterreaches 10k entries being inserted in teh index.
It generates a stirng inicateing tehsize of teh index relations, 
-- The chatr uses the 999 string to display possible digits, acts as a limted

-- We test multiple indexes grow rate in multiple different columns
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

CREATE TABLE shorturl (
    id serial primary key, key text not null, url text not null
);

CREATE INDEX key_hash ON shorturl USING hash(key);
CREATE INDEX url_hash ON shorturl USING hash(url);

CREATE INDEX key_btree ON shorturl USING btree(key);
CREATE INDEX url_btree ON shorturl USING btree(url);

-- The loop value and random (10^6) defines a 10% of repeated values
DO $$
BEGIN
    FOR i IN 0..100000 loop
        INSERT INTO shorturl (key, url) VALUES (
        uuid_generate_v4(),
        'https://www.supercool-url.com/' || round(random() * 10 ^ 6)::text
    );
    if mod(i, 10000) = 0 THEN
        RAISE NOTICE 'rows:%  Hash key  %  B-Tree key:  %  Hash url:  %  B-Tree url: %',
            to_char(i, '9999999999'),
            pg_size_pretty(pg_relation_size('key_hash')),
            pg_size_pretty(pg_relation_size('key_btree')),
            pg_size_pretty(pg_relation_size('url_hash')),
            pg_size_pretty(pg_relation_size('url_btree'));

    END IF;
    END LOOP;
END;
$$;
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

1

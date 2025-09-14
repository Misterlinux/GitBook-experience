# Page

1

1

An index is an additional **database structure** built with the specified columns of an existing table.

The index optimizes queries that reference its columns, which allows the query planner to avoid a **full table scan**.&#x20;The size and speed of an index depend on its design and the type of data it holds.

A B-tree index is designed to store **sequential data,** and it uses its **operation classes** to organize entries in a **linear** order.

It is a **multi-way balanced** tree optimized for consistent **single-entry** and **range** data retrieval.\
It is `balanced` because all leaf nodes are located in the bottom level, ensuring every search passes through the same number of **layers**.\
It is `multi-way` because each node can branch out into multiple **child nodes**, making the tree wider and minimizing the number of levels necessary for a query search.

IMMAGGINE

The B-tree entries are stored in **physical disk pages**, while its structure is made of **logical nodes** that can have multiple roles depending on their position in the tree.

> The **root node**, located at the top of the tree, is the starting point for every query. It provides the initial pointers to the first set of internal nodes.
>
> The **internal nodes**, located between the root and leaf nodes, contain key-pointer pairs that direct the search to> &#x20;the corresponding child node.
>
> The **leaf node**, located at the bottom of the tree, contains the actual entries.

An index entry is composed of a **key-pointer** pair. The `key` is the exact **column value** being stored in the b-tree, and its **size** depends on its data type and length. The `pointer` is a fixed size adress that points to the **location** of the entry data.

The role of a **pointer** depends on the node it's placed in:\
In an **internal node**, the pointer acts as a roadmap, referencing the specific **child node** that includes the searched entry value in its range.\
In a **leaf node**, the pointer is a specific **address** to the **physical location** of the **table row** containing the entry key **column value**; it can be a tuple identifier or a memory block notation.

In a standard B-tree, the single pointers in the internal nodes can be used to retrieve the actual table data, depending on the implementation.

1

B-tree **nodes** are designed to have the same size as a **disk page**. This allows the entire node to be retrieved in a single I/O operation, making the query require only one **disk access** on each index level.

A B-tree node can contain data equivalent to multiple disk pages (e.g., 4KB, 8KB, or 16KB), allowing the index to **adapt** efficiently to larger databases.

1

The maximum size of a B-tree entry is 1/3 of the node it's placed in. This rule ensures that every node can branch out to a minimum number of child nodes, which helps maintain a wide, **horizontal index** structure.

The **order** (m) of a B-tree determines the maximum number of children an internal node can have.           It also defines the maximum number  (m−1) and the minimum number of keys (m/2) .an index can have.

In an internal node, pointers act as **dividers** that create **ranges** between their **key values**, which are then represented by the child nodes. In a leaf node, the the `m−1` key rule doesn't apply because each key-pointer pair points to the **single physical location** of its data.

An incomplete child node will only fill its empty spaces with entries that fall within the specific range implicitly defined by its parent node's pointers.

Once the number of **keys** in a node exceeds the index's order (m), the node **splits**.\
A new parent internal node is created containing the **lowest median key** value. The original node's entries are split between the newly created **child nodes**, using the internal node's median key as the **divider**.

The internal node contains only single values, not ranges like its child nodes. It gets updated each time one of its children splits.\
Once an internal node becomes full, it creates a new **parent internal node** with its median value.\
It splits the internal node's entries and their corresponding child nodes between the original node and the new **sibling node**.

The **ranges** defined by the **internal node's** entries have different boundaries for the left and right sides.\
The key-pointers for 5 and 12 will create child nodes that cover these respective ranges:                                               `x<5, 5≤x<12, x≥12`\
All nodes are sorted horizontally in a linear order.

1

### Operation Familes and class

The B-tree assigns an **operation class** to each **data type** allowed within the index.\
It contains the functions that set and maintain the linear sorting order of the index values, such as _sorting_ and _operator functions_.

The **sorting function** uses the **trichotomy rule** to determine the mathematical relationship between two values.&#x20;It establishes the **initial sorted order** of the index and maintains it for every new entry added.

The **ordering relations** are the fundamental mathematical properties necessary for the **B-tree's linear order**.&#x20;These rules are implicitly enforced by the sorting function.

> The index value must follow the **reflexive** rule (A=A), the **symmetric** rule (if A=B, then B=A), and the **transitive** rule (if A=B and B=C, then A=C) for the _equality operator_.> \
> It must follow the **irreflexible** rule (A\<A is always false) and the **transitive** rule (A\<B, B\<C, then C\<A) for the less than (<) and more than (>) operators.> \
> The **trichotomy** rule states that for any two values, A and B, only one of three conditions can be true: A\<B, A=B, or A>B. This rule ensures a single, **linear order**.

The **operator functions** handle the five comparison operations (<, >, =, ≤, and ≥) used for index **queries**.\
A single-value query uses the (<) and (>) operators to **navigate** the B-tree index positions, then confirms the matching value using the (=) operator.\
A range-value query uses the (≤) and (≥) operators to locate the start of the range and includes all entries until it finds the last key value that satisfies the condition.

1

**Operation families** group multiple operation **classes** for data types with equivalent **sorting rules**, like `int4` and `int8`, and allow for **cross-data type comparison** queries on the B-tree.

The database identifies the different data types in the index query and checks if their operation classes are part of the same operation family. This allows it to **implicitly convert** one of the data types during the cross-data type comparison.

Some compatible data types, like NUMERIC and FLOAT, are not suitable for cross-data type comparisons. Their **different precision levels** would result in a converted NUMERIC value being different from the original, which breaks the transitive rule (A != A).

The metadata for the operation class functions is stored in the **pg\_amop** and **pg\_amoproc** system catalogs.

The **Access Method Operator** (`pg_amop`) system catalog is a central registry that links high-level comparison **operators** to their corresponding low-level **comparison functions**.                                                      The **metadata** for each entry contains a combination of the specific operator, data type, and index type used in the query. The database uses the information of the **matching entry** to retrieve the **operator class** that can process the query (from the **pg\_opclass** catalog).                                                                               The catalog includes an operator family column, which is used by the database to validate cross-data type comparisons in a query.

The **pg\_amproc** (Access Method Process) system catalog contains the indexes' **sorting functions**.                    The database retrieves and automatically triggers the specific support function for the **data type** being used during the CREATE INDEX command. This registry is shared among all of the database's indexes.

1

### B-TREE SUPPORT FUNCTIONS

The **support functions** are specialized methods contained within each _operator class_.\
They optimize the B-tree's **core operations**, like the sorting function that sets and maintains the index's internal structure.

> The **ORDER** support function implements the **trichotomy** function for index entries.                                                    It compares two values and returns an int32 outcome representing their mathematical relationship. A specific version of this function is included for each data type allowed in the index.
>
> The **SortSupport** optional function optimizes index sorting.> \
> It acts as a helper for the ORDER function, loading all the indexed column data into RAM where it's efficiently sorted using a **block-focused algorithm**. The query planner chooses to use it for **large datasets**, where the single-time overhead of loading data into RAM is less than the cost of multiple, single-comparison disk operations performed by the ORDER function.> \
> The function's availability depends on the data type's operator class.
>
> The **in\_range** support function optimizes the index's range operations.> \
> On select queries, it helps the database quickly find specific ranges without a full table scan.                                  On insert, it speeds up the top-down B-tree navigation to the correct child node by efficiently matching the inserted value to the ranges defined by the internal nodes.
>
> >

1

The **equalimage** support function is an optional helper for **index deduplication**.\
It allows a B-tree index to store multiple table rows values in a single key, using a **posting list** of TIDs, and avoids the use of multiple keys for the same values.

The equalimage function is triggered for all **repeated values** within the index keys.\
It applies a **semantic check** to validate the key deduplication, which allows the database to create a **canonical image** of the values. It also includes NULL entries, which, unlike in standard SQL, aren't considered unique and can be grouped together.

The database deduplicates values during the CREATE INDEX and REINDEX operations, automatically checking for equal values within the index. On INSERT and UPDATE it acts as a **conditioned lazy process**.

The deduplicated key's posting list must fit within its **single node**. It can't span across multiple nodes, and if its size exceeds the node's capacity, the duplicate values will be stored as separate keys.

Columns added to an index using the `INCLUDE` clause can't be deduplicated. They are stored in the node as part of the index's posting list and can't be further deduplicated.&#x20;

```sql
//They are called covering indexes and the INCLUDE column is not part of the key.
CREATE INDEX my_index ON my_table(indexed_col) INCLUDE (included_col);
```

The function only applies to a single column and requires a deterministic collation to consistently create a semantic image for values.

<details>

<summary>Collation and deterministic image</summary>

The deduplication process relies on the database's _deterministic rules_ applied to its comparison operations.

The database includes 2 types of **equality:**                                                                                                              The **byte-by-byte** equality, for data types like INT where it compares the exact match between 2 single values.                                                                                                                                                              The **logical equality**, for data types like TEXT where we compare the logical results of the values, like the strings "straße" and "strasse".\
The deterministic rule ensures that any comparison will always yield the same result for the same values.

The **equalimage** function is triggered when the equality operator returns TRUE for repeated index values.\
The **deterministic rule** conveys the validity of the operation to the **equalimage** function.                       It allows the function's **collation rules** to create a **semantic image** that's compatible with any value returning TRUE to the **equality** operator, rather than being based on the specific string composition.\
The deterministic rule is active by default; if disabled, the comparison will still consider the logical values equal, but it won't allow equalimage to create the semantic image because the comparison is no longer consistent.

The equalimage function is only used once, during INDEX creation, to create the initial semantic image. On INSERT or UPDATE operations, the database relies on the deterministic result of the equality comparison to validate and add logically equal values to the posting list without needint to trigger the equalimage.

Certain compatible data types can't be deduplicated, such as FLOAT4 (REAL) and FLOAT8 (DOUBLE PRECISION), because of their different precision levels and how they handle specific values like +0 and -0.\
If an indexed column can hold multiple compatible data types (e.g., VARCHAR and TEXT), their values can be deduplicated on the same semantic image.

1

</details>

1

### INDEX MAINTENANCE on Update operation, for outdated entries.

The database stores a table's rows in a collection of **disk pages**, which is called a **table heap**.\
It's optimized for size, and organized into unordered tuples, with each containing the column data for a single table row.

The database will require a table scan to retrieve tuple data from the heap, or an index scan. The index includes a TID (Tuple Identifier) for each entry of its indexed columns, which is used to locate the specific tuple in the heap.

The table heap will append any new INSERT tuple to the end of the heap, without any page range condition or ordering like in the B-tree. On UPDATE operations, the new tuple's position depends on whether the updated **tuple columns** are part of a table's **index**.

An UPDATE value including an indexed column will be appended as a new tuple in the heap, and it creates a **new entry** in the **index**, which points to the **new version** of the **tuple** with its TID.\
Any outdated tuple and index entry will be kept and handled by the later maintenance operations.\
An UPDATE value that doesn't include an indexed column will instead result in a HOT update.

A **Heap-Only-Tuple**(HOT) UPDATE modifies the table heap without triggering an **index update**.\
It adds a new version of the tuple to the **same disk page** as the old one. It also adds a **forward pointer** to the old tuple, which allows its index entry TID to find the correct data.\
If there is no available space on the old tuple's disk page, it defaults to an indexed update.\
The database management system contains the rules for the HOT update. The **query planner** provides the **execution plan** for the database after analyzing a query.

The **version bloat** forms when the index or the table heap gets full of outdated values.\
A tuple is marked as dead on DELETE or UPDATE and will be removed after a **visibility check** during maintenance. An index entry isn't explicitly marked; its TID is checked to see if it points to a dead tuple.

The REINDEX command is a global maintenance operation. It performs a complete table scan to **rebuild** the index from the **current tuples** in the table heap, which is then used to replace and update the old index.\
It relies on a prior VACUUM visibility check to detect the live tuples; if not, it defaults to indexing all tuples, even outdated ones.

The BOTTOM-UP **defragmentation** process triggers when a full B-tree index page is about to split.\
It is a **localized** and immediate maintenance operation that cleans up outdated **index entries**.

The VACUUM operation is a page-by-page maintenance process that **deletes** **dead tuples** in the table heap and **marks outdated index** entries for removal.\
The VACUUM is triggered manually while the AUTOVACUUM is triggered on a schedule. The space from deleting dead tuples is not returned to the operator system; it is marked as **free internally** for new tuples in the same table heap.\
A table heap's size is reduced by relocating its data to a smaller set of disk pages, which requires blocking all transactions. The VACUUM mantains the table heap under the MVCC conditions, mantaining concurrent access during UPDATE operations.

The VACUUM FULL command **removes dead tuples** by replacing the old table heap with a new one with only live tuples. It includes a REINDEX for the index, rebuilding it using the live tuples from the new table heap.

The VACUUM operation uses the **transaction IDs** to determine the visibility state of a tuple.\
Every transaction is assigned a unique **XID**, which is assigned to the XMIN property of the new tuples it creates and the XMAX property of any tuples it updates or deletes.\
The VACUUM sets its cleanup cutoff point based on the XID of the **oldest currently active transaction**. It will remove tuples with an XMAX value lower than the XID, as they are no longer visible to any ongoing transactions.

1

### THE B+TREE

The B+tree is a streamlined version of the B-tree data structure.

The B+tree stores key-TID pairs only in the leaf nodes, using the internal nodes only for keys and child pointers. This is different from a standard B-tree, where the internal nodes also contain the TIDs of their single values that act as dividers.

The B+tree includes a **doubly linked list** that **horizontally** connects nodes at the same tree level.\
It optimizes range values retrievals by avoiding to vertically traverse the tree.\
It can slow down single-value queries because the search must always reach a leaf node, while a standard B-tree can retrieve the data from an internal node.

The B+tree **logical structure** is implemented differently depending n the _database management system_:

In MySQL, a B+ tree includes `previous` and `next` pointers for all nodes at every horizontal level, which allows for efficient horizontal retrieval of data.                                                                                                                       In PostgreSQL's, the B-tree only has the `previous` and `next` pointers at the last level for the leaf nodes.

Both follow the N keys for N+1 child pointers rule for their internal nodes.

The B+ tree is used for a table's primary key index. Its TID works as a unique row identifier.\
It's created by default on CREATE TABLE when a primary key is included in a column definition, it optimizes data retrieval on the table's heap.

1

### LINEAR INSERTS optimitaton

The index's INSERT operations are slowed down by **non-linear** or randomly generated data types.\
They can cause unpredictable page splits and lead to inefficient leaf-node entry distribution, but they won' affect SELECT operations.

The PostgreSQL SERIAL and MySQL AUTO\_INCREMENT constructs create **efficient sequences** of column values in the index. They generate values in a linear order, allowing for an efficient leaf-node storage and reducing the node splits.

The database loads copies of frequently accessed data pages into its RAM cache, allowing it to update the pages without I/O operations. It then pushes the changes to the physical disk before the page split.

The database uses a sequential **Write-Ahead Log** (WAL) on the disk to prevent data loss.\
Any change to a data page is first recorded in the WAL before it's applied to the page in the RAM cache. The database will replay the WAL operations to restore the data after a system crash.

The **entry's size** impacts the B-tree structure and efficency.\
A **large key** data type causes the node to contain fewer entries before it splits, making the index taller and slowing down the data retrieval.\
A **small key** data type may not provide enough unique values due to its shorter length.\
An INT 32-bit integer has 2.1 billion possible values, while a BIGINT 64-bit integer has 9.2 quintillion values, offering more unique values in exchange for a larger entry size.

1

1

1

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

1

1

1

1

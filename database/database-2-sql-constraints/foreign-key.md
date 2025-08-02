# Foreign key

1

The FOREIGN KEY constraint creates and enforces a **reference link** between 2 columns.\
It ensures that a **referencing column** (the child) can only contain values already present in its **referenced column** (the parent).

A FOREIGN KEY is defined within the child table, its REFERENCES keyword points to the **parent table** and its specific columns. Multiple child rows can reference the same parent column value.

```sql
//The child column must match the parent data type
//A PRIMARY KEY parent column avoids any duplicate column value and NULL for the child
CREATE TABLE products (
    chiave integer PRIMARY KEY, name text, price numeric
);
insert into products(chiave, name, price) values (15, 'brio',550), (10, 'malbo',200);

//Any existing constraint still applies within the child table
//If the parent column is omitted, it will use its own column name for the reference.
CREATE TABLE orders (
    order integer,   //UNIQUE can limit the child columns values
    chiavetta integer REFERENCES products (chiave)  //Will use chiavetta if omitted
);

//The FOREIGN KEY only allows values form the specified column, not the entire table.
insert into orders(order, chiavetta) values 
    (3, 15),
    (10, 10),  //The parent reference value can be repeated within the row
    (3, 15),   //Allowed if teh order child columns isn't UNIQUE
    (3, 200);  //Error, it violates the foreign key constraint
```

A child FOREIGN KEY must reference UNIQUE or PRIMARY KEY **parent columns**.                                                                                                                                                                   The JOIN operation combines table columns data using the FOREIGN KEY relations.

<details>

<summary>Referencing multiple parent column using the composite FOREIGN KEY constraint</summary>

A **composite FOREIGN KEY** references a unique **set** of parent columns.&#x20;\
All its child columns must be referenced from a _single_, explicitly defined composite constraint.

The FOREIGN KEY **clause** can omit the parent columns list if they reference a PRIMARY KEY constraint; the database will **implicitly map** the child columns by position.                                              While the UNIQUE parent columns must always be **explicitly listed**.

Invalid FOREIGN KEY constraints:

* A FOREIGN KEY can't reference a **subset** of columns from a composite parent constraint.  \
  A unique composite **set** can contain **repeated individual column** values (like (1, 10) and (1, 20)), making the database unable to identify parent rows based on a single referenced column.
* A composite FOREIGN KEY cannot reference multiple independent parent columns.  \
  It must reference a single composite constraint that contains all the parent columns within its unique set.

We **can't directly modify** a foreign key; we must first DROP and ADD a **named** new one within the child table.&#x20;Any new foreign key added must reference existing constraints in the parent table.

```sql
//uno being both part of UNIQUE and PRIMARY KEY constraints
create table singolo(
    uno INT primary key, due INT not null,
    unique (uno, due)
)
insert into singolo(uno, due) values (15, 30), (3, 12);

//We use CONSTRAINT to name a foreign key
//Named single column: 'base TEXT constraint based REFERENCES uno'
create table doppio(
    base TEXT, primo INT, secondo INT,  
    //We must specify the parent columns in an UNIQUE constraint reference
    CONSTRAINT coppia
        foreign key (primo, secondo) references singolo(uno, due)
)

into doppio(base, primo, secondo) values
    ('you', 15, 30), ('you', 3, 12),
    ('you', 15, 12);    //ERROR, it violates the coppia set in FOREIGN KEY

//We can ALTER only named constraints
ALTER TABLE doppio DROP CONSTRAINT coppia;
//It references the PRIMARY KEY constraint also aplied to the uno column
//Only create table keys can omit the PRIMARY KEY parent columns list
ALTER TABLE doppio ADD constraint nuovo 
    FOREIGN KEY (primo) REFERENCES singolo(uno);

//The new constraint allows repeated uno values from a PRIMARY KEY parent
//The PRIMARY KEY constraint applies to the parent column, not the child's.
insert into doppio(base, primo, secondo) values ('you', 15, 12);    

base|primo|secondo|
----+-----+-------+
you |   15|     30|
you |    3|     12|
you |   15|     12|
```

</details>

A column that **references** the PRIMARY KEY of a row within the **same table** is called **self-referencing**.\
It creates a hierarchical **structure** where rows are organized into **branches** based on the PRIMARY KEY they reference, with rows containing NULL **foreign key** values serving as the **root nodes** that originate the structure.\
It enabled the query to retrieve and order data based on the relations between the table rows.

A query involving self-referencing columns uses multiple **instances** of the **same table** within its FROM clause.\
The first instance (t1) represents the **current row** being processed, while the second instance (t2) is used in the **matching operation**. This comparison links the foreign key value from the first instance to its referenced parent primary key, found using the second instance.

<pre class="language-sql"><code class="lang-sql">//It references the PRIMARY KEY by default.
CREATE TABLE tree (
    node_id integer PRIMARY KEY, name TEXT, 
    parent_id integer REFERENCES tree -- Self-referencing foreign key
);

INSERT INTO tree (node_id, parent_id, name) values 
    (1, NULL, 'base camp'), (2, NULL, 'aviation'), (101, 1, 'tank'), 
    (102, 1, 'jep'), (201, 101, 'soldier'), (301, 2, 'plane'), 
    (302, 302, 'suplies'),  -- self referring column
    (305, 90, 'dog');       -- ERROR, no PRIMARY KEY to reference

//The INNER JOIN clause only returns matching row values in the output.
select 
    t1.node_id, t1.name, t2.name as parent_name
from
    tree as t1
left join 
    tree as t2 on t1.parent_id = t2.node_id
order by t1.node_id

<strong>node_id|name     |parent_name|
</strong>-------+---------+-----------|101|tank     |base camp  |
      1|base camp|           |102|jep      |base camp  |301|plane    |aviation   |
      2|aviation |           |201|soldier  |tank       |302|suplies  |suplies    |
</code></pre>

The LEFT JOIN clause includes all rows from the **current** table instance (the **left operand**) in the query's output. It appends the result columns from the matching operation, assigning NULL to unmatched rows. It effectively flattens the output, presenting both child and parent information within the same row.

1

There are multipe types of tables relationships, based on their **cardinality**:

{% tabs %}
{% tab title="Many-To-Many" %}
It requires an extra linking table that references the **parent primary keys** in its child columns.\
The composite primary key **combines** the foreign key columns, with each column referencing a different table.

```sql
//Both parent tables are referenced using their PRIMARY KEY
//A FOREIGN KEY keyword can't define it, as it can only reference one parent.
create table meal(
    porta INT primary key, name TEXT
)
insert into meal(porta, name) values (5, 'steak'), (12, 'frittata');

create table dessert(
    vaso INT primary key, name TEXT
)
insert into dessert(vaso, name) values (2, 'torta'), (7, 'bigne');

//The sets of the linking table's composite primary key
CREATE TABLE cena(
    meal_id integer REFERENCES meal, vaso_id integer REFERENCES dessert,
    cost integer, PRIMARY KEY (meal_id, vaso_id)
);
insert into cena(meal_id, vaso_id, cost) values 
    (5, 7, 40),
    (12, 7, 40),    //Repeated vaso_id primary key value allowed
    (5, 7, 120);    //Error, repeated primary key set 
```

Parent column values are defined once but can be referenced **many** times in the linking table.\
Any update will only affect the parent table it's declared in, with no need to change its corresponding reference in the linking table.
{% endtab %}

{% tab title="One-To-Many" %}
Each parent PRIMARY KEY column can be referenced by **multiple child columns**. The FOREIGN KEY can accept repeated values that reference the same parent.

```sql
//No unique constraint in the FOREING KEY allows repeated values
create table singolo1(
    indice INT PRIMARY key, valore INT
)
insert into singolo(indice, valore) values (11, 101), (12, 101);
insert into singolo(indice, valore) values (11, 44);  //Error, duplicated parent

create table solo1(
    mano INT references singolo, libro INT
)

insert into solo1(mano, libro) values (11, 101), (12, 99);
insert into solo1(mano, libro) values (11, 44);  //Allowed repeated child value
```
{% endtab %}

{% tab title="One-To-One" %}
Each parent PRIMARY KEY column is referenced by a **single unique** FOREIGN KEY. A specific child column value can be related to a speific parent only once.

```sql
//By using an additional UNIQUE constraint in the FOREIGN KEY
create table singolo(
    indice INT PRIMARY key, valore INT
)

insert into singolo(indice, valore) values (11, 101), (12, 101);
insert into singolo(indice, valore) values (11, 44);  //Error, repeated primary

create table solo(
    mano INT unique references singolo, libro INT
)

insert into solo(mano, libro) values (11, 101), (12, 99);
insert into solo(mano, libro) values (11, 44);  //Error, repeated child value
```
{% endtab %}
{% endtabs %}

1

The database **restricts** any change made to parent columns currently being referenced.                                                                                                                                                                                      The FOREIGN KEYS must follow the **referencial integrity rule**, which states that all **non-NULL child values** must references an existing parent column.

The **event specifiers** set the parent row actions that will trigger a child's column response.\
The **referential actions** then define how the foreign key values will be modified to maintain the referential integrity after parent changes.

1

{% tabs %}
{% tab title="CASCADE option" %}
The CASCADE option, with the ON DELETE event specifier, ensures that deleting a parent row also deletes all its related child rows, preventing any unassigned non-NULL child values.

```sql
//The action specifier REQUIRES a referencial action option to work
create table base(
    primo INT primary key, name TEXT
)
insert into base(primo, name) values (12, 'moto'), (8, 'car'), (99, 'lambo');

create table casca(
    secondo INT references base on delete CASCADE, name TEXT
)
insert into casca(secondo, name) values (12, 'mirai'), (99 ,'goldie');

//The entire child row gets deleted, not just the referencing columns
delete from base where primo = 12;
select * from casca;
secondo|cognome|
-------+-------+
     99|goldie |
```
{% endtab %}

{% tab title="RESTRICT option" %}
The RESTRICT option, set by default, prevents the deletion of any parent row **while it's being referenced** by a child row.

```sql
create table base1(
    primo INT primary key, name TEXT
)
insert into base1(primo, name) values (12, 'moto'), (8, 'car'), (99, 'lambo');

create table resto(
    secondo INT references base1 on delete RESTRICT, name TEXT
)
insert into resto(secondo, cognome) values (12, 'mirai'), (99 ,'goldie');

//Error, it violates the foreign key relation rule
delete from base1 where primo = 12; 
```
{% endtab %}
{% endtabs %}

1

The NO ACTION option prevents parent operations, symilar to RESTRICT.\
It's the only referential action that can be combined with the DEFERRABLE clause, which enables it to **delay** the referential integrity check until the end of its **transaction**.  It allows **subsequent operations** within the same transaction to resolve any potential integrity violations before the final **commit**.

The DEFERRABLE clause, by itself, results in an INITIALLY IMMEDIATE **integrity check**. We need to **explicitly** specify the INITIALLY DEFERRED clause to enable the delayed referential check.

{% tabs %}
{% tab title="INITIALLY DEFERRED clause" %}
Many database editors **group multiple** operations into a single transaction, allowing a DEFERRED clause to place its referential integrity check at the transaction's final commit.

```sql
//NO ACTION and RESTRICT are exactly the same in some database systems
create table base2(
    primo INT primary key, name TEXT
)
insert into base2(primo, name) values (12, 'uno'), (5, 'due'), (42, 'tre');

create table azione(
  catena INT references base2 on delete NO action DEFERRABLE INITIALLY DEFERRED,
  modo TEXT
)
insert into azione values (12, 'primo'), (5, 'due'), (42, 'terzo');

//We delete both parent and childs in the same transaction to keep the integrity
delete from base2 where primo = 12;     //By itself: Error.
delete from azione where catena = 12;   //Executed together: Correct.

select * from azione;
```

We can change the FOREIGN KEY's current DEFERRABLE **setting** using the SET CONSTRAINTS command.
{% endtab %}

{% tab title="SET CONSTRAINT command" %}
The SET CONSTRAINTS command can dynamically change the DEFERRABLE behavior of a named NO ACTION constraint.\
The command will be applied only to the specific set of operations it's included in.

```sql
//It is usefull when editing columns within child tables
//and unorganized data loads where referential integrity is handled internally.
create table base2(
    primo INT primary key, name TEXT
)
insert into base2(primo, name) values (12, 'uno'), (5, 'due'), (42, 'tre');
insert into base2(primo, name) values (12, 'uno');

create table azione(
  catena INT constraint chiave references base2 on delete NO action DEFERRABLE,
  modo TEXT
)
insert into azione values (12, 'primo'), (5, 'due'), (42, 'terzo');

//We change the DEFFERRABLE default initially immediate with DEFERRED
//SET CONSTRAINTS { ALL | constraint_name [, ...] } { DEFERRED | IMMEDIATE };
//It will not change the table set DEFERRABLE constraint
SET CONSTRAINTS chiave DEFERRED;
delete from base2 where primo = 12;
delete from azione where catena = 12;
```
{% endtab %}
{% endtabs %}

The SET NULL and SET DEFAULT options maintain referential integrity by changing the foreign key column values within the child rows.

{% tabs %}
{% tab title="SET NULL on DELETE " %}
The SET NULL option, applied ON DELETE, sets the foreign key columns in the child row to NULL when its referenced parent row gets deleted. The NULL value, following the **referential integrity rule**, implicitly **removes** the parent-child reference in its FOREIGN KEY column.

```sql
//It can be aplied only to NULLABLE columns
create table base3(
    base INT primary key, name TEXT
)
insert into base3(base, name) values (9, 'scala'), (12, 'piano'), (33, 'molo');

create table resetta(
    foglio INT references base3 on delete set null, name TEXT
)
insert into resetta(foglio, name) values (9, 'piede'),(12, 'sala'),(33, 'nave');

delete from base3 where base = 9;
select * from resetta;
foglio|name |
------+-----+
    12|sala |
    33|nave |
      |piede|
```
{% endtab %}

{% tab title="SET DEFAULT on DELETE" %}
The SET DEFAULT option sets the foreign key columns to their predefined default values when its referencing parent row is deleted. The referential integrity is maintained only if its non-NULL default values **reference an existing parent column**.

```sql
//If no default is specified, it will act as SET NULL using NULL defaults.
create table base4(
    base INT primary key, name TEXT
)
insert into base4(base, name) values (9, 'scala'), (12, 'piano'), (33, 'molo');

create table defalta(
    foglio INT default 50 references base4 on delete set default, name TEXT
)
insert into defalta(foglio, name) values (9, 'piede'),(12, 'sala'),(33, 'nave');

delete from base4 where base = 9;   //Error, there is no parent with 50 as value
insert into base4(base, name) values (50, 'torre');
delete from base4 where base = 9;   //Allowed, now it references a parent row
delete from base4 where base = 33;  //Its default references the same parent.

select * from defalta;
foglio|name |
------+-----+
    12|sala |
    50|piede|
    50|nave |
```
{% endtab %}
{% endtabs %}

The event specifiers must still satisfy any pre-existing constraints on their foreign key child columns.

1

Both **event specifiers** and **referential actions** can't be directly applied to PRIMARY KEY columns.

We call **identifying foreign keys** the foreign keys columns that are used as PRIMARY KEYS values.   They will be implicitly set as NOT NULL and UNIQUE, while also needing to reference **existing parent column** values. This dual role limits the types of referential actions that can be applied to them.

```sql
//It is mostly used in many-to-many relations
create table student1(
    stud_id INT primary key, name TEXT
)
insert into student1(stud_id, name) values (12, 'volan'), (1, 'prima'), (7, 'emmy');

create table corsi1(
    cors_id INT primary key, name TEXT
)
insert into corsi1(cors_id, name) values (5, 'bio'), (12, 'volan'), (17, 'ruota');

create table classe1(
    student_id INTEGER REFERENCES student1, course_id INT references corsi1,
    PRIMARY KEY (student_id, course_id)
)
insert into classe1(student_id, course_id) values (12, 5);
insert into classe1(student_id, course_id) values (7, 12);
insert into classe1(student_id, course_id) values (12, 12);

select * from classe1;
```

{% tabs %}
{% tab title="SET NULL option " %}
We can add a SET NULL option to an identifying FOREING KEY, but the database won't execute it.&#x20;If a referenced parent row is deleted, the foreign key columns cannot be updated to NULL because of their **implicit** NOT NULL constraint when used as **primary keys.**

```sql
//The parent delete will return error
delete from student1 where stud_id=12;

create table classe1(
    student_id INTEGER REFERENCES student1 ON DELETE set NULL,
    ...
)
```
{% endtab %}

{% tab title="CASCADE option  " %}
The CASCADE option will delete the entire child row of the identifying foreign key.\
It implicitly satisfies the primary key's NOT NULL constraint **by removing** the entire row, and it will delete **all other columns** included in the primary key, regardless of their individual options.

```sql
//It keeps the only student_id values that weren't 12
delete from student1 where stud_id=12;

create table classe1(
    student_id INTEGER REFERENCES student1 ON DELETE CASCADE,
    course_id INT references corsi1 ON DELETE NOT NULL,
    PRIMARY KEY (student_id, course_id)
)

select * from classe1;
student_id|course_id|
----------+---------+
         7|       12|
```

1

1

1
{% endtab %}

{% tab title="SET DEFAULT" %}
The SET DEFAULT option usually doesn't work with an identifying foreign key. The default value must satisfy the UNIQUE constraint among the **existing** identifying foreign key values.

```sql
//A default 7 will return error, for the existing (7, 12) primary key.
delete from student1 where stud_id=12;

create table classe1(
    student_id INTEGER default 1 REFERENCES student1 ON DELETE set DEFAULT,
    ...
)
```
{% endtab %}

{% tab title="initially DEFERRED" %}
The NO ACTION DEFERRABLE initially DEFERRED option can be applied to an identifying foreign key. It allows for multiple operations in a single transaction involving the child column value.

```sql
//We re-assign the identifying foreing key value to an existing parent
delete from student1 where stud_id=12;
update classe1 set student_id=1 where student_id=12;

//We can delete the child row using the identifying foreign key
delete from student1 where stud_id=12;
DELETE FROM coppia1 WHERE student_id = 12; 

create table classe1(
    student_id INTEGER REFERENCES student1 ON DELETE 
        no action deferrable initially DEFERRED,
    ...
)
select * from classe1;
```
{% endtab %}
{% endtabs %}

1

1

<details>

<summary>Defining a Foreign Key Hierarchy in a Child Table.</summary>

We can create a hierarchy of **multiple foreign keys** within the same **child table**, where each is defined by the specific **referential action** triggered by its parent table event.

We define the **main foreign key** column as part of the composite primary key.\
The foreign key's CASCADE referential action establishes the referenced table as the **main parent**, and makes the specific foreign key a necessary component in the child table.

We define the **secondary child column** as part of a composite foreign key referencing another table.&#x20;The foreign key's SET NULL referencial action defines the parent columns's values as **non-essential** for the child table.\
We can remove the reference link by deleting its specified foreign key columns values, while maintaining the rest of the child row and preserving the specified identifying foreign key.

```sql
//The main parent table is referenced in the secondary parent table
CREATE TABLE tenants (
    tenant_id integer PRIMARY KEY
);
insert into tenants(tenant_id) values (1), (2), (4);

//The secondary parent table includes added columns in its primary key
CREATE TABLE users (
    tenant_id integer REFERENCES tenants ON DELETE CASCADE, user_id integer,
    PRIMARY KEY (tenant_id, user_id)
);
insert into users(tenant_id, user_id) values (1, 15), (2, 12), (4, 9);

//The author_id references the second column (user_id) of the users parent
CREATE TABLE posts (
    tenant_id integer REFERENCES tenants ON DELETE CASCADE,
    post_id integer NOT NULL, author_id integer,
    PRIMARY KEY (tenant_id, post_id),
    FOREIGN KEY (tenant_id, author_id) 
        REFERENCES users ON DELETE SET NULL (author_id)
);
insert into posts(tenant_id, post_id, author_id) values 
    (1, 3340, 15), (2, 5570, 12), (4, 1800, 9);

//The on delete on the primary foreign key will delete the entire row 
delete from tenants where tenant_id=1;  //
//The on delete on the secondary foreing key will SET NULL some columns
delete from users where user_id=9;  

select * from posts;
```

</details>

1

1

The ON UPDATE event specifier **implements** its referential actions differently from ON DELETE.\
Because the parent row **still exists** after the update, the database must either **maintain the reference** to the updated parent row or delete the foreign key's reference without deleting the entire child row.

{% tabs %}
{% tab title="on update CASCADE" %}
The CASCADE option maintains the referential integrity by **updating** the child column values to **match** the new parent column.

```sql
//It can update a single column from a composite foreign key.
create table base6(
    uno int, due TEXT,
    primary KEY(uno, due)
)
insert into base6(uno, due) values (1, 'copiato'), (2, 'secondo'), (3, 'terzo');
update base6 set uno=11 where uno=1 returning *;

create table cambio(
    base INT, primo INT, secondo TEXT,
    foreign KEY(primo, secondo) references base6 on update CASCADE
)
insert into cambio(base, primo, secondo) values (12, 1, 'copiato');
insert into cambio(base, primo, secondo) values (12, 2, 'secondo');
insert into cambio(base, primo, secondo) values (3, 1, 'copiato');

select * from cambio;
base|primo|secondo|
----+-----+-------+
  12|    2|secondo|
  12|   11|copiato|
   3|   11|copiato|
```
{% endtab %}

{% tab title="on update  SET NULL" %}
The SET NULL option updates all the child columns related to the updated parent row to NULL.  It maintains the referential integrity by using NULL to indicate a removed foreign key reference.

```sql
//The UPDATE event isn't usually used to delete child rows.
create table base61(
    uno int, due TEXT,
    primary KEY(uno, due)
)
insert into base61(uno, due) values (1, 'copiato'),(2, 'secondo'),(3, 'terzo');
update base61 set uno=11 where uno=1 returning *;

create table cambio6(
    base INT, primo INT, secondo TEXT,
    foreign KEY(primo, secondo) references base61 on update set null
)
insert into cambio6(base, primo, secondo) values (12, 1, 'copiato');
insert into cambio6(base, primo, secondo) values (12, 2, 'secondo');
insert into cambio6(base, primo, secondo) values (3, 1, 'copiato');  

select * from cambio6;
base|primo|secondo|
----+-----+-------+
  12|    2|secondo|
  12|     |       |
   3|     |       |
```
{% endtab %}
{% endtabs %}

The MATCH SIMPLE option, set by default, considers a foreign key not part of any reference if any of its columns are NULL.\
The MATCH FULL option requires all foreign key columns to be NULL for it to be considered **unreferenced**.&#x20;It blocks any operation that might result in a partially NULL foreign key, such as SET NULL with specified columns, or SET DEFAULT where some default values are NULL.

```sql
//MATCH FULL requires all foreign keys to be either null or not-null.
//The option goes before the event specifier.
create table base7(
    primo INT, secondo INT, name TEXT, 
    primary KEY(primo, secondo)
)
insert into base7(primo, secondo, name) values 
    (1, 12, 'vaso'), (3, 22, 'sedia'), (5, 8, 'root');

delete from base7 where primo = 3;  //ERROR, partial set null.

create table tutti(
    uno INT, due INT, name TEXT,
    foreign key (uno, due) references base7 match full on delete set NULL(uno)
)
insert into tutti(uno, due, name) values 
    (1, 12, 'altro'), (3, 22, 'basso'), (5, 8, 'lato');
```

The values of the PRIMARY KEY and UNIQUE **parent** columns are implicitly stored in an **index** structure.\
It validates the child columns that reference the parent's values, but it doesn't store any data about the relation.

Referential actions like CASCADE, SET NULL, and SET DEFAULT involve **locating child column** values after parent row operations. We use an additional index on the foreign key columns to optimize the retrieval.

```sql
//It can optimize JOIN operations that use the foreign key columns.
create table tutti(
    uno INT, due INT, name TEXT,
    foreign key (uno, due) references base7 match full on delete set NULL
)
CREATE INDEX tutti_index ON tutti (uno, due);
```

The foreign key constraint specifies the rules for column values, but it is not a column definition that can directly include an index statement.

1

1

1

1

# Foreign key

1

1

1

1

There are multipe types of tables relationships, based on their **cardinality**:

{% tabs %}
{% tab title="Many-To-Many" %}
It requires an extra **linking table** that references the **parent primary keys** in its child columns.\
Its **foreign keys** are then used to form the child's composite primary key.

Similar to a one-to-many relationship, where child values can repeat for a single parent column, the many-to-many allows **repeated values** from both parents to be used within the linking table's composite primary key.

```sql
//Both parent tables are referenced using their PRIMARY KEY
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
It's the only referential action that can be combined with the DEFERRABLE clause, which enables it to **delay** the referential integrity check until the end of its **transaction**. It allows **subsequent operations** within the same transaction to resolve any potential integrity violations before the final **commit**.

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

1

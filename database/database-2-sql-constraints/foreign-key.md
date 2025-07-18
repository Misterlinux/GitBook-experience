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

1

1

1

1

1

1

1

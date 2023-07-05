# DATABASE 1

* elements
* In my mind
* siamo

A **database** is a structured set of data, it checks **data validity** and **sets relations** between data structures. There are many **Database types**, _relational databases_ (the most used), _key/value_, _graph_, and _document databases_(called NoSQL).

**SQL**, **S**tructured **Q**uery **L**anguage, is the standard language for **relational databases**, it is used to <mark style="background-color:blue;">create</mark>, <mark style="background-color:blue;">insert</mark>, <mark style="background-color:blue;">alter</mark>, <mark style="background-color:blue;">update</mark>, and <mark style="background-color:blue;">drop</mark> database records.                                                                   **SQL scripts** are processed by an **RDBMS**, **R**elational **D**atabase **M**anagement **S**ystem, to manage a relational database, like _PostgreSQL_.

**PostgreSQL**, an **O**bject-**R**elational **d**atabase **s**ystem, started in 1986 and _extends_ the SQL language to _store and scale_ databases.

**DBeaver** is a _SQL client software application_ and a _relational database administrative tool_, we create PostgreSQL database connections and SQL scripts to create database objects (like tables, etc).

### DBeaver tables and SQL script&#x20;

On DBeaver we create a **connection**-> select the **PostSQL driver**-> open the **SQL script** file (f3).            To find our tables we _schemas_-> _public_-> _tables_.

<figure><img src="../.gitbook/assets/Dbeaverstart.png" alt="" width="563"><figcaption><p>Deaver database startup for the SQL scriopt</p></figcaption></figure>

In a **relational database**, data is stored in **tables** of rows and columns.

We **create table()**, and set its **column** properties **name (keys)** and **data type**, **not null** is for mandatory values on user rows.                                                                                                                         **Varchar(n)** for strings, **int** for integers, **date** for date() objects, and **boolean** for true/false values.    The **Serial primary key** is a unique identifier for the user rows, it increments after each insert.

```
//The Varchar(n) is the max string.length(n)
//In SQL you need to select the code and execute it with crtl+enter

create table multi(
  id		SERIAL primary key,
  name		VARCHAR(15) not null,
  employed	boolean,
  age		int not null,
  birthday	date not null
)

```

<details>

<summary>Reference keys between tables</summary>

To avoid repeating data between related tables we **reference keys** (also called foreign keys).

We set a **data type** (most of the time ID int) and **reference** another **table** property, the referenced key will contain the entire table row.

```
//when selecting/executing multiple scripts, remember to use (;)

CREATE TABLE customers (
  id        SERIAL PRIMARY KEY,
  name      VARCHAR(30) NOT NULL,
  email     VARCHAR(120) NOT NULL,
  address   VARCHAR(120),
  city      VARCHAR(30),
  postcode  VARCHAR(12),
  country   VARCHAR(20)
);

//The customer int ID row == bookings.customer_id
CREATE TABLE bookings (
  id               SERIAL PRIMARY KEY,
  customer_id      INT REFERENCES customers(id),
  hotel_id         INT REFERENCES hotels(id),
  checkin_date     DATE NOT NULL,
  nights           INT NOT NULL
);

```

To **insert** values in a **table** with **referenced keys** we:

```
//we are basically creating an expanded row with added column 

insert into bookings 
( customer_id, hotel_id, checkin_date, nights) 
values 
( 1, 1, '10/4/2010', 5 )
```

</details>

<figure><img src="../.gitbook/assets/TableReferenceKeys.png" alt="" width="563"><figcaption><p>Reference keys on different tables</p></figcaption></figure>

To add a row we **Insert into table\_name() Values()**.

```
//We use -- to comment in a SQL file, any inserted string needs ''
//to add multiple values use (,)
//Table keys and inserted values need to be the same number

INSERT INTO hotels (name, rooms, postcode) 
VALUES 
('Triple Point Hotel', 10, 'CM194JS'),
('The 2-star palace', 7, 'AW124T5');

```

We use **select from** to see Tables' **data**, and we can add logical operators.

```
//* selects every column, WHERE used for keys conditions

Select * from customers
select name, email from customers 
SELECT * FROM hotels WHERE rooms > 7;
SELECT * FROM bookings WHERE checkin_date > '2019/10/01' AND nights >= 2;
SELECT * FROM hotels WHERE name='Royal Cosmos Hotel' OR rooms > 10;
```

<details>

<summary>non c'è</summary>

**ORDER** the table rows based on columns.

```
//crescent order is the default, add DESC for de-crescent order

select * from varietas order by anni
select * from varietas order by anni DESC

```

1

1

1

1

1

1

1

1

</details>

**Alter table** and **Update** are used to modify the **table's keys** and row **properties** respectively.

```
//a new date_of_birth column was added to the customer's table, 
//it will be empty for every pre-existing row
ALTER TABLE customers ADD COLUMN date_of_birth DATE;

//The night's property on the bookings table will be set at 25 
//if id and customer_id are 1
update bookings set nights=25 where id= 1 and customer_id= 1
```

<details>

<summary>More Alter columns SQL scripts</summary>

**Alter table** can include more table operations, like **drop column**.

```
alter table multi drop column employed
```

**Rename** to change the name of the table.

```
alter table multi rename to varietas
```

**Rename column** to change a column's name.

```
alter table varietas rename column age to anni;
```

We can "**drop**" the **not null** constraint from keys and **change** their **data type**.

```
//The key drops or can become mandatory in each INSERT
alter table varietas alter column birthday drop not null/set not null

//Will work only if the previous column elements are convertible
//(int -> Varchar()) will work (varchar() to int) won't
alter table varietas alter column anni type VARCHAR(5)

```

</details>

**INNER** joins multiple **table columns**, we use **(.)** to access a _table property_ and add logical operators, we can select columns from **multiple tables**, as long as we JOIN them.

```
//We JOIN the customers and bookings columns when their properties are the same
SELECT * FROM customers INNER JOIN bookings ON customers.id = bookings.customer_id;

//A select made of columns from different tables, with condition
//remember to use the FROM table as the second element in any logical operation
SELECT bookings.checkin_date, customers.name, hotels.name FROM bookings
INNER JOIN customers ON customers.id=bookings.customer_id
INNER JOIN hotels ON hotels.id=bookings.hotel_id
WHERE customers.id=1;

```

**DROP** and **DELETE** to remove _tables_ and _rows_ respectively:

```
//To remove a table
table drop varietas

//delete a row with condition
delete from varietas where id=4;

```

1

1

1

1

# SQL table Data Types

* 1
* 1
* 1
* 1

**Data types** in PostgreSQL define the data format within table column.

```jsx
//Data types within column definition
TEXT and CHAR(n), VARCHAR(n):        text based types, 
DATE, TIME, TIMESTAMP, and INTERVAL: include date and time types
JSON:                                for semi structured data, 
INT, REAL:                           for numeric types and
ENUM, ARRAY:                         specialized types
```

The **TEXT**, **CHAR(n),** and **VARCHAR(n)** data types store **string** values in table columns.\
TEXT allows strings of any **length**. While both CHAR(n) and VARCHAR(n) set a **maximum n** length.\
The CHAR(n) is also a **fixed-length** type, it will pad the end of the string with trailing spaces in order to mantain its specified length.

```sql
//CHAR and VARCHAR without (n) will be treated as CHAR(1) and TEXT respectively.
CREATE TABLE resorce (
    primo   CHAR(20), secondo VARCHAR(20), terzo   TEXT
);

insert into resorce(primo, secondo, terzo) 
values (' accidenti ', ' accidenti ', ' accidenti ');
//(" accidenti              "," accidenti "," accidenti ")
```

The **||** operator concatenates string values, with the return string converted to TEXT regardless of the data types being used.\
The **substring()** function returns a segment of a string, starting at the FROM index and continuing for the FOR length.\
The **replace()** function searches each string in a column for a specified substring and replaces all occurrences with a given new string.

```sql
//We can concatenate different data types.
select (text 'prima ' || ' e ' || 211 );    //TEXT "prima  e 211" 

//We can concatenate string values from columns.
SELECT nome || ' ' || titolo as new_sting FROM parole;

//The strings follow the 1-index rule, like the arrays
select substring('parola'::text from 1 for 3);      //par
SELECT substring(nome FROM 1 FOR 5) FROM parole;    //It selects from table columns

//It can either be a specific column or a stirng value, it replaces the substring
select replace('mottura'::text, 'ttu', 'lla' ); //mollara
SELECT replace(nome, 'pre', 'post') FROM parole where id=2;
```

The **INTERVAL** data type represents a time _duration_, which allows adding and subtracting intervals from date and time values.\
The standard format for an interval value is **years-months days hours:minutes:seconds**, with the hyphen(-) specifically separating years and months.

```sql
//It is not affected by time zones
//We can use either numerical and string values, singular or plural.
create table inte(
    id serial primary key, numero INTERVAL
);

INSERT INTO inte (numero) 
    VALUES ('47'); //single values seconds by default
    VALUES ('12:50');  //Hours and Minutes
    VALUES ('00:21:47');   //necessary 00 for Minutes and Seconds
    VALUES ('21 minutes 47');  //String interval integration

    VALUES ('2 12:12:50');    //Days value
    VALUES ('1 2 12:12:50');   //error, Year required for month int value
    VALUES ('0-1 2 12:12:50');  //correct only month interval value
    VALUES ('1 months 12:12:50'); //stirng intervals can skip element
```

The **justify\_interval** function converts intervals and **interval operations** that exceed their time unit limit.

> The normalized intervals will be more context-dependent than purely arithmetic.
>
> * The **hours-days** and **days-months** (30 days) overflows are more common and standardized conversions.
> * The **months-years** convertion isn't standardized, the number of months in a year can be context-dependants (like in accounting systems).
> * The **seconds-minutes** and **minutes-hours** follow fixed rules and don't typically require justification.

```sql
//Type casting not needed if no operations
select justify_interval('0-1 20 100:20:20');  -- 1 mon 24 days 04:20:20
select justify_interval('2 days 10:90:69');   -- error outide the allowed interval
select justify_interval('0-1 50 10:12:12');   -- 2 mons 20 days 10:12:12

insert into inte(numero) values 
    ('99:59:59' - '10:00:00');                      -- error, type casting necessary
    (interval '99:59:59' - '10 hours 10 seconds');  -- 89:59:49
    ('99:59:59'::interval - '10 hours 10' );        -- 89:59:49

//The justify_interval converts literal/integer string results
select tempo, justify_interval(tempo) as new_value from lista where id=2; 
-- numero       new_value
-- 89:59:49     3 days 17:59:49

//INTERVAL operations on TIME values return TIME.
SELECT TIME '12:30:00' + INTERVAL '30 minutes' AS new_time;     //13:00:00
```

The **JSON** and **JSONB** data types store and manipulate JSON data in the table rows.\
JSON mantains its original plain text format, while JSONB store data in a **decomposed binary** format, which enhances query performance and storage efficiency.

PostgreSQL validates any JSON data inserted into a JSONB column, converting it to an internal binary format. Upon retrieval, this format is automatically converted back to a JSON literal; the binary representation is for **internal use** only.

```sql
//JSONB supports GIN indexing 
//JSONB also removes any unnecessary whitespace and orders the keys
create table basico(
    id SERIAL primary key,
    parte JSONB
);

insert into basico(parte) values 
('{"name": "Alice", "age": 30, "city": "New York"}'),
('{"products": [{"id": 1, "name": "Laptop"}, {"id": 2, "name": "Mouse"}]}');
```

The '**->**' operator retrieves the JSON value associated with the selected key, preserving its original type.

```sql
//->> will return the string-converted value
//For nested JSON we repeat -> for each layer, using the index to access arrays values
SELECT parte -> 'numero' FROM basico where id = 1;  //30 jsonb 
SELECT parte -> 'products' -> 1 ->> 'name' FROM basico where id = 2;  //Mouse text

//The #> operator extracts JSON values based on a specified path.
//And #>> returns the value as string.
select parte #> '{numero}' from basico where id = 1;    //30 jsonb 
select parte #>> '{products,1,numero}' from basico where id = 2;    //'67' text
```

<figure><img src="../../.gitbook/assets/Jsonfieldtypes.png" alt=""><figcaption><p>Different data types on JSON select</p></figcaption></figure>

The **@>** operator checks if a JSON object contains a specified subset.

```sql
//While the <@ to check if its contained within another
SELECT * FROM basico WHERE parte @> '{"city": "New York"}';
SELECT * FROM basico WHERE '{"city": "New York"}' <@ parte;
```

The **?** operator checks if a single key exists at the _top level_ of a JSON object and returns the matching JSON object.

```sql
//Can't access nested objects, can return multiple rows
SELECT * FROM basico WHERE parte ? 'numero';    //{..., "numero": 30}
```

The **?|** operator returns rows where any string from the array exists as a top-level key in the JSON.

```sql
//It can return multiple objects, can't access nested objects
select * from basico where torta ?| ARRAY['name', 'chiavi'];  //{"name": "Alice", ...}
```

The **&?** operator returns rows where all keys from the array exist as top-level keys in the JSON.

```sql
//It can return multiple objects, can't access nested objects
select * from basico where torta ?& ARRAY['name', 'chiavi'];  //none
select * from basico where torta ?& ARRAY['name', 'city'];    //{"name":"Alice", ...}.
```

The **REAL** data type stores floating-point and scientific notation values with _approximate precision_.

```sql
//REAL stores data in 4 bytes instead of the 8 of FLOAT
//Can be used fo exponential values or pi()
create table basico(
    id SERIAL primary key,
    numa    REAL
);

insert into basico(numa) values (12.999);
insert into basico(numa) values (1.0e6);
insert into basico(numa) values (1.6E+19);
insert into basico(numa) values ( pi() );
```

The **ENUM** custom data type defines a **set** of allowed values for a table column.\
It cannot be defined directly in a column definition; we must **CREATE TYPE** it outside the table.&#x20;

PostgreSQL stores ENUM values _internally_ as small **integers**. The **pg\_enum** system catalog maps the integers to theirs ENUM values during queries.

We can add new ENUM values using ALTER TYPE, but we cannot remove or rename existing values, nor change their order.

```sql
//The ENUM order will affect its values sorting and comparison operations
CREATE TYPE status AS ENUM ('primo', 'secondo', 'terzo', 'quarto');
//DROP TYPE status; //It won't remove automatically on table delete

CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username TEXT NOT NULL,
    account_status status 
);

//We can only add column values from the ENUM 
INSERT INTO users (username, account_status) VALUES ('Alice', 'primo'); //secondo, ...

//ENUM order on comparison operators
select * from users where account_status < 'secondo';   //'primo'
select * from users where account_status > 'secondo';   //'terzo', 'quarto'

//error: non ENUM value being added
INSERT INTO users (username, account_status) VALUES ('yanico', 'sesto');   

//The BEFORE, AFTER clauses set the position of new ENUM values
ALTER TYPE status ADD VALUE 'quinto' AFTER 'quarto';        //('quarto', 'quinto')
ALTER TYPE status ADD VALUE 'semisecondo' BEFORE 'terzo';   //('semisecondo', 'terzo')
INSERT INTO users (username, account_status) VALUES ('Alice', 'quinto');
INSERT INTO users (username, account_status) VALUES ('Alice', 'semisecondo');
```

The ARRAY data type stores an ordered collection of elements within a single column.

During column definition, we specify both the data type of the ARRAY elements and the ARRAY dimension(check below for details), which determines the nesting level of accepted values.

We INSERT array values as strings using curly braces {} or the ARRAY\[] constructor, which are then converted\
to their column data type.

```sql
//The [] backets have a different use in the PostgreSQL parser.
//Nested array values must contain the same number of elements. 
CREATE TABLE resorce (
    id SERIAL PRIMARY KEY,
    lista   INT[2],
    testo   VARCHAR(5)[]
);

//NULL can be used to keep the same length between nested arrays
insert into resorce(lista, TESTO) values 
( '{{12, 12}, {45, null}}', '{"fuori", "corto", "min"}' );

//Error, 2-dimention INT array with different nested lengths and strng above teh varChar() length
insert into resorce(lista, TESTO) values ('{{12, 12}, {45}}', '{"ridotto"}');

//The PostgreSQL arrays use 1-based indexing for retrieving data.
select testo from resorce where id = 1;         --{fuori,corto,min}
select testo[1] from resorce where id = 1;      --fuori
select lista[1][1] from resorce where id = 1;   --12
```

The \[start:end] syntax slices its array, with both its indices being inclusive.\
In multi-dimensional arrays, it will only slice the outer level, due to how PostgreSQL structures arrays.

```sql
//[:] will include the entire array, due to how the default bounds work
//The {} data infer works on INSERT operations, if not we explicitly define its array data type

SELECT testo[3:] from resorce;
SELECT (ARRAY[1, 2, 3, 45, 4])[3:];     //{3,45,4}
SELECT ('{12, 34, 56, 67}'::int[])[:2]; //{12,34}
```

PostgreSQL provides operators like @> (contains) and && (overlaps), along with functions for modifying arrays, such as array\_append() and array\_length().

The array\_append() function returns a new array with the specified element appended at the end, it doesn't support multi-dimantional arrays.\
We use the UPDATE to edit the array in the table.

```sql
//It appends to each element within the column if not specified
select array_append(testo, 'ultimo') from resorce;  //{out,corto,min1,ultimo}, {..., ultimo}
update resorce set testo = array_append(testo, 'speci') where id = 1;  
```

The array\_remove function ....

The array\_length() function returns the length of an array, requiring both the array column and its dimension.

```sql
//Both arguments are required
//It returns the lengths of the arrays contained in the testo columns
select array_length(testo, 1) from resorce;     //1, 1, 2
select array_length(array[ [12, 55], [null, 32], [99, null] ], 2);  //2
```

Array operations in PostgreSQL require identical data types; TEXT\[] and VARCHAR\[] arrays, both containing string literals values, will be treated as different types.\
For arrays defined with curly braces {}, the PostgreSQL parser performs implicit conversions. It infers and adjusts the array data to match the target column.

The ARRAY\[] constructor interprets its string literals TEXT by default and do not convert their data based on the target column.\
Explicit type casting is needed to convert TEXT arrays to the target column's data type. This is particularly useful in complex table structures.

```sql
//The {} will return error if the values can't be converted to the data type.
//We ::dataType and add [] to specify that its an array containg the values.

insert into resorce(lista, TESTO) values ( '{{12, 12}}', '{"min"}' );
insert into resorce(lista, TESTO) values ( '{{92, 12}}', '{"min1"}' );

//The testo column is VARCHAR(5)[]
SELECT * from resorce where testo  @> ARRAY['min1', 'min1']::VARCHAR(5)[];  //{out,corto,min1}

SELECT * from resorce where lista  @> ARRAY[12, 12];    //{{12,12}, ...}, {{99,12}, ...}
SELECT * from resorce where lista  @> ARRAY[12];    //{{12,12}, ...}, {{99,12}, ...}
//The overlap && will check only for the presence of any shared values
```

The containment operator @> checks if all elements of one array are present in another, regardless of declared dimensions. Duplicates are ignored, so ARRAY\[1] and ARRAY\[1, 1] are considered equivalent.

PostgreSQL does not enforce multidimensional arrays; when declared in table column they serve as user documentation.\
At runtime they are treated as one-dimentional arrays containing nested arrays, rather than as true two-dimensional arrays.

The ANY keyword is designed to compare a single value to array columns, returning true if any element matches.\
It functions similarly to WHERE for single values and supports various comparison operators (<, >, =).

```sql
//Any array that includes the single value will be returned
create table doppio(
    id  serial primary key,
    serie   INT[],
    numeri  INT
)

INSERT into doppio (serie) VALUES (array[101, 12]), (array[12]);
insert into doppio (numeri) values (101), (12);

select * from doppio where 30 > any (serie);    //{12}, {101, 12}
select * from doppio where 30 > numeri;     //12

select 4 = any (array[1, 2, 3, 4, 5]);  //true, an element within the array is = 4
```

ANY is a quantifier. It modifies the comparison process to compare each array element to the provided single value.\
The ANY keyword must be placed on the right side of the comparison operator to be processed correctly. On the left side, it is treated as a function, resulting in an invalid comparison.

```sql
//Different ways to return arrays without the single value.
SELECT * FROM numeri WHERE 23 <> any (schedule); 
SELECT * FROM numeri WHERE 23 != any (schedule); 
SELECT * FROM numeri WHERE NOT (23 = ANY (schedule));
```

Both ANY and IN compare a value against a series of values.\
ANY works directly with array literals and table array columns, enabling element-wise comparisons,\
IN is designed for explicit comma-separated value sets and subqueries, requiring UNNEST for table arrays.

```sql
//UNNEST will return the array as a list for the IN
SELECT 4 = ANY ('{1, 2, 3, 4, 5}'::INT[]);
select 4 in (1, 2,3, 4, 5);

//Subqueries return result list, compatible with both ANY and IN
SELECT * FROM numeri
WHERE column1 IN (SELECT column2 FROM table2 WHERE condition);

SELECT *
FROM table1
WHERE column1 = ANY (SELECT column2 FROM table2 WHERE condition);
```

The && operator checks for overlap between arrays and ranges, returning true if any elements are shared.\
It requires identical data types between the values and doesn't work on subqueries.

```sql
//It performs the OR operator between the array/range elements.
CREATE TABLE numeri (
    employee_id SERIAL PRIMARY KEY,
    schedule INT[]
);

INSERT into doppio (serie) VALUES
(array[101]), (array[12]), (array[23]);

select * from doppio where ARRAY[30, 12] && serie;  //{12}

select ARRAY[1, 2, 3] && ARRAY[2, 4, 5];    //true
```

The ALL operator compares a single value to an array column and returns true only if each element satisfies the comparison.\
It supports multiple math and logical operators (>, <, !=) and works on subqueries;

```sql
//Not suited for multi-dimantional arrays unless UNNEST
//ALL needs to be set in the right side, like ANY.
create table doppio(
    serie   INT[];
)

//If 50 is > than all elements in the table array columns
INSERT into doppio (serie) VALUES (array[12, 45]), (array[56, 12]);
select * from doppio where 50 > ALL(serie); //{12,45}

//10 is not equal to than ALL array elements
SELECT 10 != ALL (ARRAY[5, 8, 2, 5]);   //true
```

The EXISTS operator returns true if its subquery returns at least one row.

We create a correlation between the outer query and the subquery, by incorporating outer query data within\
the subquery, so that each row evaluates its EXISTS condition individually.

```sql
//Without correlation it might use the first EXISTS result for the other rows withoun re valuating
//We correlate the empjob form the ouyter query to the depname of the subquery
//Each row evaluation of the outer query SELECT will update the subquery comparison and 
//trigger the EXISTS update, results
CREATE TABLE departments (
    depid   SERIAL PRIMARY KEY,
    depname VARCHAR(100)
);

CREATE TABLE employees (
    empid   SERIAL PRIMARY KEY,
    empname VARCHAR(100),
    empjob  TEXT
);

insert into departments(depname) values ('human'), ('info'), ('market');

insert into employees(empname, empjob) values
('lowrence', 'market'), ('mike', 'bus'), ('dory', 'walk');

SELECT empname FROM employees AS emp
WHERE EXISTS (
    SELECT 1
    FROM departments AS dept
    WHERE emp.empjob = dept.depname
);
//lowrence
```

Both IN and JOIN can perform similar operations to EXISTS, but they require the\
complete evaluation of the subquery or table join. While EXISTS stops\
evaluation at the first match, avoiding full table scans.

<details>

<summary>JOIN and IN on conditional returns, alternatives methods</summary>

JOIN is more efficient for creating explicit relationships between tables. While IN can be more readable.

We query JOIN the tables based on the matching condition, to then SELECT the columns form teh joined table

```sql
//It has to precess the entire tables
//We use one column (empjon) for teh comparison but then select the otehr empname
SELECT emp.empname 
FROM employees AS emp
JOIN departments AS dept ON emp.empjob = dept.depname;
```

The query uses the IN operator to compare the empjob column against a list of\
department names generated by a subquery. It then selects the empname for any matching rows.

```sql
//Th esubquery needs to process the entire table to return its set.
SELECT empname
FROM employees AS emp
WHERE empjob IN (SELECT depname FROM departments);
```

</details>

The EXISTS operator offers advantages when dealing with large tables correlated to smaller ones,\
as it can terminate comparisons faster than processing the entire table.

```sql
//We create and populate the table with a large number of values
CREATE TABLE large_customers (
    customer_id SERIAL PRIMARY KEY,
    customer_name VARCHAR(100)
);

//The generate_series() creates a series of unique values for the table
//It is not compatible with INSERT, it returns an unique series instead of being a multiple series of VALUES.
//The SELECT takes data and insertes it into the table
INSERT INTO large_customers (customer_name)
SELECT 'Customer ' || generate_series(1, 1000);

-- we create a smaller table with the same ID as the big one, but with less elements

//We create a smaller table that references the ID values form the largests.
CREATE TABLE customers_with_orders (
    customer_id INT REFERENCES large_customers(customer_id)
);

-- Populate with some customers who have placed orders

//We fill the table with 1/100 of the larget table values,  
INSERT INTO customers_with_orders (customer_id)
SELECT customer_id
FROM large_customers
WHERE customer_id % 100 = 0;

-- Query using EXISTS (efficient)

//The query will return the correlated values faster thna IN and JOIN
SELECT customer_name
FROM large_customers c
WHERE EXISTS (
    SELECT 1
    FROM customers_with_orders o
    WHERE o.customer_id = c.customer_id
);
```

1

1

1

1

1

1

1

1

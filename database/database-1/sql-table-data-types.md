# SQL table Data Types

* 1
* 1
* 1
* 1

PostgreSQL data types define the data **format** of data within table columns.\
These types can be either built-in or user-defined, encompassing various categories:

```jsx
//Types can be 
TEXT and CHAR(n), VARCHAR(n):        text based types, 
DATE, TIME, TIMESTAMP, and INTERVAL: include date and time types
JSON:                                for semi structured data, 
INT, REAL:                           for numeric types and
ENUM, ARRAY:                         specialized types
```

The **TEXT**, **CHAR(n),** and **VARCHAR(n)** data types store **string** values in table columns.\
The TEXT data type allows strings of any **length**. While both CHAR(n) and VARCHAR(n) set a **maximum n** length for their strings.\
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

We can proceed string operations with string values within column values.\
The string values can be concatenated using the **||** operator, which wil convert the resulting string into TEXT,&#x20;regardless of the previous VARCHAR() or CHAR() VALUES or diffferent data types.

We can perform operations in the column string values, within PostgreSQL.\
The **||** operator concatenates string values, with the return string converted to TEXT regardless of the data types being used.\
The **substring()** function returns a segment of a string, starting at the index specified by FROM and continuing for the length specified by FOR.\
The **replace()** function searches each string in a column for a specified substring and replaces all occurrences with a given new string.

```sql
//We can concatenate different data types.
select (text 'prima ' || ' e ' || 211 );

//We can concatenate string values from columns.
SELECT nome || ' ' || titolo as new_sting FROM parole;

//The strings follow the 1-index rule, like the arrays
select substring('parola'::text from 1 for 3);  //par
SELECT substring(nome FROM 1 FOR 5) FROM parole;    //It selects from table columns

//It can either be a specific column or a stirng value, it replaces the substring
select replace('mottura'::text, 'ttu', 'lla' ); //mollara
SELECT replace(nome, 'pre', 'post') FROM parole where id=2;
```

The **INTERVAL** data type represents a duration of time, enabling the addition or subtraction of time intervals from date and time values.\
The standard format for an interval value is **years-months days hours:minutes:seconds**, with the hyphen specifically separating years and months.

```sql
//It is not affected by time zones
//We can use either numerical and string values, singular or plural depending on.
//The returned interval value will depend on the positions of the values.
create table inte(
    id serial primary key, numero INTERVAL
);

INSERT INTO inte (numero) VALUES ('47'); //single values seconds by default
INSERT INTO inte (numero) VALUES ('12:50');  //Hours and Minutes
INSERT INTO inte (numero) VALUES ('00:21:47');   //necessary 00 for Minutes and Seconds
INSERT INTO inte (numero) VALUES ('21 minutes 47');  //String interval integration

INSERT INTO inte (numero) VALUES ('2 12:12:50');    //Days value
INSERT INTO inte (numero) VALUES ('1 2 12:12:50');   //error, Year required for month int value
INSERT INTO inte (numero) VALUES ('0-1 2 12:12:50');  //correct only month interval value
INSERT INTO inte (numero) VALUES ('1 months 12:12:50'); //stirng intervals can skip elemtns
```

The justify\_interval function adjusts the intervals to the largests appropriate unit, used for when intervals\
exceed natural time unit limits and to reconcile intervals created from literal or numerical operations.

```sql
//Evene within the justify_interval, minutes and seconds can't be above 59.
//We perform INTERVAL operations using +/-

//04:31:30
insert into inte(numero) values (INTERVAL '5 hours 1 minute 30 seconds' - INTERVAL '30 minutes');  

insert into inte(numero) values (INTERVAL '5:01:30' - INTERVAL '100 minutes');  //04:31:30
INSERT INTO inte(numero) values ('5:01:30'::INTERVAL - INTERVAL '200 minutes'); //01:41:30

//The SELECT for the justified interval can include teh old value.
insert into inte(numero) values ('99:59:59');
select numero, justify_interval(numero) as new_value from inte where id = 5; 
//numero      new_value
//99:59:59    4 days 03:59:59

//INTERVAL operations on TIME values return TIME.
SELECT TIME '12:30:00' + INTERVAL '30 minutes' AS new_time;     //13:00:00
```

The **JSON** and **JSONB** data types store and manipulate JSON data, like semi-structural data and web APIs.\
JSON mantains its original plain text format, while JSONB store data in a decomposed binary format, which enhances query performance and storage efficiency.\
PostgreSQL validates any JSON data inserted into a JSONB column, converting it to an internal binary format. Upon retrieval, this format is automatically converted back to a JSON literal; the binary representation is for internal use only.

```sql
//JSONB also removes any unnecessary whitespace and orders the keys
create table basico(
    id SERIAL primary key,
    torta   JSONB
);

insert into basico(torta) values 
('{"name": "Alice", "age": 30, "city": "New York"}'),
('{"products": [{"id": 1, "name": "Laptop"}, {"id": 2, "name": "Mouse"}]}');
```

The '->' operator retrieves the JSON value associated with the selected key, preserving its original type.

```sql
//->> will return the string-converted value
//For nested JSON we repeat -> for each layer
('{"name": "Alice", "numero": 30, "city": "New York"}'),
('{"products": [{"id": 1, "name": "Laptop"}, {"id": 2, "name": "Mouse", "numero": 67 }]}');

SELECT torta -> 'numero' FROM basico where id = 1;  //30, int 
SELECT torta -> 'products' -> 1 ->> 'numero' FROM basico where id = 2;  //

//The #> operator extracts JSON values based on a specified path.
//And #>> returns the value as string.
select torta #> '{numero}' from basico where id = 1;    //30
select torta #>> '{products,1,numero}' from basico where id = 2;    //'67'
```

-— ADD IMAGE for different type on dbeaver

The @> operator checks if a JSON object contains a specified subset.

```sql
//While the <@ to check if its contained within another
SELECT * FROM basico WHERE torta @> '{"city": "New York"}';
SELECT * FROM basico WHERE '{"city": "New York"}' <@ torta ;
```

The ? operator checks if a single key exists at the top level of a JSON object and returns the matching JSON object.

```sql
//It's not designed for path navigation
SELECT * FROM basico WHERE torta ? 'numero';    //{..., "numero": 30}
```

The ?| operator checks if any key from an array exists in a JSON object.

```sql
//It can return multiple objects
select * from basico where torta ?| ARRAY[ 'name', 'chiavi' ];  //{"name": "Alice", ....}
```

The &? operator checks if all keys in an array are present in a JSON object.

```sql
//
select * from basico where torta ?& ARRAY[ 'name', 'chiavi' ];  //none
select * from basico where torta ?& ARRAY[ 'name', 'city' ];    //{"name":"Alice", ...}.
```

The REAL data type stores floating-point and scientific notation values with approximate precision.

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

The ENUM type defines a set of allowed values for a table column.\
It defines the type of teh data stored in teh column,

It cant be defined in teh cpliumn definition, it has to be declared as a separate object,\
requires a create ype statement,

The order of the ENUM listed values wil affect the valeus when being sorted and\
when using comparison ooperators on them.\
The ENUM values will b einternally stored as small integers for efficenty,\
WE can ALTER the ENUM and ADD VALUE to teh ENUM list of avaiable\
BUT we cannnot remove or RENAME existing enum values,\
and their order cannot be changed,

The ENUM data type defines a set of allowed values for a table column.\
Unlike most data types, ENUM types cannot be defined directly in a column definition; they must be created separately using CREATE TYPE.

The order of ENUM values affects their sorting and their comparison operations. They are stored internally as\
small integers.

We can add new ENUM values using ALTER TYPE, but we cannot remove or rename existing values, nor change their order.

```sql
//The ENUM has to be declared outside
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

1

1

1

1

1

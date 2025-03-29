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

1

1

1

1

1

1

1

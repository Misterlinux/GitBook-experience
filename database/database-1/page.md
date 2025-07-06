# Custom Range and base data types and PostgreSQL binary data

* [PostgreSQL base custom data type](page.md#postgresql-base-custom-data-type)
* [PostgreSQL binary data storage and SQL standard syntax](page.md#postgresql-binary-data-storage-and-sql-standard-syntax)

The RANGE custom data type allows us to define an **interval of values** for a specified **subtype**. It offers options to specify the data type's behavior.\
The **subtype\_diff** function calculates the distance between two values of quantifiable subtypes, which helps optimize indexing. The **canonical** function standardizes the range boundaries, ensuring a consistent representation of borders.

```sql
//UUID and text are avaiable subtypes but are not quantifiable
//This is the structure of a CREATE TYPE range
//The [,] is for optional fields while | is for optional values
CREATE TYPE name AS RANGE (
    SUBTYPE = subtype
    [ , SUBTYPE_OPCLASS = subtype_operator_class ]
    [ , COLLATION = collation ]
    [ , CANONICAL = canonical_function ]
    [ , SUBTYPE_DIFF = subtype_diff_function ]
    [ , MULTIRANGE_TYPE_NAME = multirange_type_name ]
)
```

It defines ranges for data types that lack **built-in range support**.

```sql
//DO anonymous blocks can't issue a RETURN statement
//The %ROWTYPE variable requires an entire table row, not a single column
CREATE TYPE durate AS RANGE (
    SUBTYPE = interval
);

CREATE TABLE igno (
    sensor_id SERIAL PRIMARY KEY, time_window durate
);

INSERT INTO igno (time_window) VALUES 
    ('[0.5 hours, 5 hours)'::durate), ('[5 hours, 15 hours)'::durate);

DO $$
declare
    --linea igno%ROWTYPE;    //Select * from ...
    linea durate;            //Select time_window from ...
begin 
    SELECT time_window INTO linea from igno where sensor_id = 2;
    RAISE NOTICE 'We found the %', linea;
    
    IF (linea) @> '10 hours'::interval THEN
        RAISE NOTICE 'within the interval range';
    ELSE
        RAISE NOTICE 'outside the interval';
    END IF;
    --RETURN 'The inteval being "'|| linea ||'" ';    //Error
end;
$$ language PLPGSQL;

//We found the [05:00:00,15:00:00)
//within the interval range
```

<details>

<summary>Range custom data type as function argument</summary>

In a PL/pgSQL function, you can return a table %ROWTYPE from a query and compare it with a function argument that contains a composite data type.

```sql
//We compare 2 Range custom data type within the PL/pgSQL function
CREATE TYPE user_info AS (
    phone_number TEXT, email TEXT
);

CREATE TYPE durate AS RANGE (
    SUBTYPE = interval
);

select '[2 days, 5 days)'::durate && '[1 day, 4 days]'::durate; -- true

CREATE TABLE tavola (
    sensor_id SERIAL PRIMARY KEY, location TEXT,
    time_window durate,
    operator_contact user_info
);

INSERT INTO tavola (location, time_window, operator_contact)
VALUES
    ('quick', '[2.5 h, 10 h)'::durate, ROW('123', 'north@m.com')::user_info),
    ('full', '[10 h, 2 d)'::durate, ('987', 'south@m.com')::user_info);

//The table stores both custom types
CREATE OR REPLACE FUNCTION car_check(
    arg_id INT, arg_time durate, arg_user user_info
)
RETURNS TEXT LANGUAGE plpgsql
AS $$
DECLARE
    tavola_row tavola%ROWTYPE;
BEGIN
    //The %ROWTYPE returned from teh ID query
    SELECT * INTO tavola_row FROM tavola WHERE sensor_id = arg_id;

    IF NOT FOUND THEN
        RETURN 'Error: Sensor ID ' || arg_id || ' not found.';
    END IF;

    RAISE NOTICE 'Query returned location: % ', tavola_row.location;
    RAISE NOTICE 'Extracted table interval: %', tavola_row.time_window;
    RAISE NOTICE 'Funtion argument interval: %', arg_time;
    RAISE NOTICE 'Extracted argument composite fields contact: % (Email: %)', 
        (arg_user).phone_number, (arg_user).email;

    //We text-convert the boolean result between custom range and interval
    RETURN 'For a ' || tavola_row.location || ' the interval is ' || 
            (arg_time && tavola_row.time_window)::text;
END;
$$;

//Query returned location: quickfix 
//Extracted table interval: [02:30:00,10:00:00)
//Funtion argument interval: [01:30:00,04:00:00)
//Extracted argument composite fields contact: 111-222 (Email: newmail@m.com)
SELECT car_check(
    1, -- sensor_id
    '[1.5 hours, 4 hours)'::durate,    //Range custom data type argument
    ROW('111-222', 'newmail@mail.com')::user_info //Composite argument
```

</details>

ENUM Custom data types were exaplined in the [SQL data Types section](sql-table-data-types.md#enum-and-array).

### PostgreSQL base custom data type&#x20;

A base custom data type creates a **new, fundamental data** type using C code functions.\
The CREATE TYPE statement must include the custom **input** and **output functions**, which are used to convert between the **textual** representation and the **binary** storage format. Additionally, functions associated with the base data type can **forward reference** the created data type even if it has not been fully defined yet.

```sql
//The C custom functions can have the base custom data type as return or argument
CREATE FUNCTION mytype_in(cstring//mytype)  //mytype
RETURNS mytype//cstring     //even before the data type is defined.
AS 'path/to/my_type.so', 'mytype_in_c_function_name'
LANGUAGE C IMMUTABLE STRICT;

//We define the convertion logic functions within the fields
//We can include a receive and send function to transfer binary data back and forth
CREATE TYPE mytype (
    internallength = 16, //bytes of internal storage
    input = mytype_in, output = mytype_out,
);
```

**Custom data type functions** run directly in PostgreSQL's backend processes, granting them access to the database's internal memory. They must be declared as IMMUTABLE and STABLE in order to ensure data consistency within the database.

Both **PL/pgSQL** and **C functions** are managed and executed by the **backend process** within the server's **RAM**.&#x20;PostgreSQL stores their executable code in different parts of the file system.

> **PL/pgSQL** functions have their textual source code stored in the **pg\_proc system catalog**, within the database's internal data files. Once called by an SQL script, it gets retrieved and executed in the backend process after applying the PL/pgSQL **interpreter**.
>
> **C** functions are compiled into **shared library files**, which are reusable code sections that can be accessed across multiple processes. These files are part of the system files, separate from the database's internal data files. The pg\_proc system catalog stores the function's name and the **path** to its shared library file within its metadata, which the backend process then uses to retrieve and execute the function.

Each **custom** data type **includes** a corresponding **array** data type, which shares its original name with an underscore appended.

The associated array type employs its **array\_in function** for table columns containing arrays of custom data types. It **parses** each literal string element within the array and **delegates** their **conversion** to the **input function** provided by the custom data type.

The array\_in function then stores the resulting **binary data** within the **table column's** array structure.\
For single-value table columns, we directly pass the ROW literal string to the custom type's input function.

```sql
//It will include a _user_info array data type, which parses the INSERT values.
CREATE TYPE user_info AS (
    phone_number TEXT, email TEXT
);

CREATE TABLE esempio (
    sensor_id SERIAL PRIMARY KEY,
    operator_contact user_info[]
);

insert into esempio(operator_contact) values ( 
    ARRAY[('1240', 'malago')::user_info, ('3456', 'flotil')::user_info] 
);
```

### PostgreSQL binary data storage and SQL standard syntax.

The binary data's structure within the table column depends entirely on its data type's internal definition.&#x20;For composite types, this is a **sequence of binary representations for each individual field**.

**Binary data** is the compact, internal format in which all data in PostgreSQL is ultimately stored.                    The conversion between the SQL-level ROW representation and the internal binary format is handled by the input\_function of the composite type during data insertion, and by its output\_function when data is retrieved.

Let's clarify some key data concepts related to custom types in PostgreSQL:

> A **ROW** is the direct SQL representation of the data values, custom or built-in, used in SQL input and output operations.
>
> The **%ROWTYPE** is a composite data type that directly mirrors a table's column structure. It is used as a variable within a PL/pgSQL function to access and manipulate the table column data.
>
> **Binary data** is the underlying format PostgreSQL uses to store all database information on disk.

The **pg\_type system catalog** stores the properties and data type relations of the **custom data type**.\
The Unique Object ID (**OID**) identifies every element within a PostgreSQL database. Each custom data type includes the **typarray** property, which indicates the OID of its **corresponding array** data type. Conversely, every array data type contains the **typelem** property, which indicates the OID of the element contained within the array.

```sql
//System catalogs are built-in metadata tables, accessible without extensions.
//We seach the array data type using its _underscore name
//pg_type identifies the data type while pg_stat checks for the data type performances
CREATE TYPE user_info AS ( phone_number TEXT, email TEXT );

//OID is a serial number, stored in TYP (not type) properties
select 
    t1.typname, t1.oid, t1.typelem, t1.typarray
from pg_type as t1
where t1.typname = 'user_info';
    
--typname  |oid   |typelem|typarray|
-----------+------+-------+--------+
--user_info|245819|      0|  245818|
 
SELECT
    typname, oid, typelem, typarray
FROM pg_type as t2
WHERE t2.typname = '_user_info';  //Implicity array data type name

--typname   |oid   |typelem|typarray|
------------+------+-------+--------+
--_user_info|245818| 245819|       0|
```

The **SQL standard** defines the fundamental language baseline for all SQL-compliant database systems. Each **database vendor** incorporates its own extensions to this standard to offer unique features or optimize existing functionalities.

PostgreSQL **extensions** enable the creation of empty tables and data types, as well as the definition of custom types like ENUM, ARRAY and BASE——expanding upon the composite data types included in the SQL standard syntax.\
While these commands deviate from the official SQL standard, PostgreSQL fully understands and executes them within its own environment.

1

1

1

1

1

1

1

1

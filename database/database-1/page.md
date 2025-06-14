# Page

* 1
* 1
* 1

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

1

1

1

1

1

1

1

1

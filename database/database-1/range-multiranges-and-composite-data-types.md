# Range, Multiranges and composite data types

* [Multirange data type syntax and operations](range-multiranges-and-composite-data-types.md#multirange-data-type-syntax-and-operations)
* [Multirange on table column](range-multiranges-and-composite-data-types.md#multirange-on-table-column)
* [PostgreSQL composite custom data types](range-multiranges-and-composite-data-types.md#postgresql-composite-custom-data-types)
* [Composite data type arguments on PostgreSQL functions](range-multiranges-and-composite-data-types.md#composite-data-type-arguments-on-postgresql-functions)

The **RANGE** data type represents a contiguous intervals of values; it is defined by its lower and upper bound.

```sql
//It's compatible with previous postgreSQL data types
create table lista(
    id  serial primary  key,
    user_id int,
    raggio  int4range  //int8range, numrange, daterange, tsrange, tstzrange
)

insert into lista(raggio) values ('[12, 44]');
insert into lista(raggio8) values ('[12, 44]');
insert into lista(raggionum) values ('[12.12 , 44]'); //num for decimals
insert into lista(raggiodata) values ('[2021-01-12, 2021-01-15]');
insert into lista(raggiots) values ('[2021-01-12 12:12:12, 2021-01-15 15:15:15]');
insert into lista(raggiotstz)values('[2021-01-12 12:12:12+02,2021-01-15 15:15:15+02]')
```

By default, PostgreSQL ranges follow the **\[**&#x69;nclusive, exclusiv&#x65;**)** bounds format.

```sql
//Depending on the data type it increases the upper bound for consistency
//Necessary to optimize range operations
select int4range('[1, 3]'); -- [1,4)
select int4range(2, 15, ()); -- [3,15)  //It overrides range constructors
select daterange('[2001-05-12, 2001-05-14]'); -- [2001-05-12,2001-05-15)
select tsrange('[2001-05-12 12:12:10, 2001-05-14 12:12:30]'); 
    -- ["2001-05-12 12:12:10","2001-05-14 12:12:30"]
```

The **lower()** and **upper()** range functions retrieve their respective bounds.\
We can define infinite bounds using the **'infinity'** string (compatible with tsrange, tstzrange, daterange, and numrange in both constructors and literals), **NULL** (in range constructors), or an empty bound (in range literals).

```sql
//Both the NULL and empty bound are compatible with ts/tzrange
//No space fo the empty bound
select int4range(5, NULL) @> 250;    //true
SELECT tsrange('-infinity', '2023-06-30 10:00:00', '[]');
SELECT '["-infinity", "33.44"]'::numrange;
select '[, 450]'::int4range @> 250;
```

**Arrays** and **ranges** are both _collections_ of data types, but they use different operators.\
Array operators like ANY or EXISTS operate on a series of values, while a range represents a **single contiguous interval**.

```sql
//The @> and && operators are compatible with ranges.
//Some values like numrange require explicit casting or specific values
select 12 <@ '[10, 45]'::int8range;    //teue
select 12 <@ '[10, 45.45]'::numrange;  //not compatible data types integer <@ numrange
select 12::numeric <@ '[10, 45.45]'::numrange;  //true
select 12.12 <@ '[12, 45]'::numrange;           //true

//The && is only between ranges
select '[10, 45]'::int4range && '[30, 40]';    //true
```

Range operations include:

{% tabs %}
{% tab title="Range (&> and &<) operators" %}
The not-extend-to-the-left/right operators (**&>** and **&<**) return true depending on the starting and ending bound between two ranges.

```sql
//&> returns true if the first lower() is greater than the second.
//&< returns true if the first upper() is lower than the second.
SELECT int4range(7, 20) &> int4range(5, 10);  -- true, 7>5 no extend
SELECT int8range(3, 10) &> int8range(5, 12);  -- false, 
SELECT int8range(5, 10) &> int8range(5, 8);   -- true

SELECT int4range(7, 20) &< int4range(5, 10);  -- false, 20> 10
SELECT int8range(3, 10) &< int8range(5, 12);  -- true 10 < 12
SELECT int8range(5, 10) &< int8range(5, 10);  -- true
```
{% endtab %}

{% tab title="Range (<< and >>) operators" %}
The **<<** and **>>** operators return true if the first range lies strictly at the **left** or **right** of the second. It compares the first range **upper()**/l**ower()** with its **opposite** bound in the second

```sql
//It returns false for overlap and inclusive bounds
select int8range(1,10) << int8range(100,110);   -- true, 10< 100
select '[1,100]'::int4range << '[100,110]'::int4range;  -- false, 100 = 100
select '[45, 54]'::int8range >> '[12, 44]'::int8range;  -- true, 45 > 44
```
{% endtab %}

{% tab title="Range (-|-) operator" %}
The **adjacency** operator (**-|-**) checks the boundary points between 2 ranges.&#x20;It returns true if there is no gap or overlap between the bounds.

```sql
//It checks for adjacency for both its lower and upper bounds.
//A single inclusive bound will be considered as an overlap
select '[2, 40]'::int4range -|- '[40, 120]'::int4range; -- false
select '[2, 40]'::int4range -|- '[41, 120]'::int4range; -- true
select daterange('2025-04-15','2025-04-20')-|-daterange('2025-04-20','2025-04-25')
    -- true
select int8range(5, 12, '(]') -|- int8range(12, 49, '(]');  -- true
select '(40, 120)'::numrange -|- '[2, 40)'::numrange;   -- false
```
{% endtab %}
{% endtabs %}

We can process **math** operations using:

<details>

<summary>The ranges sum (+), subtaction (-) and intersections (*) operations</summary>

The **sum** operator (**+**) returns a single range from multiple overlapping and contigous ranges.

Ranges are considered 'contiguous' based on their data type. For **discrete** data types, contiguity means there are no skipped units between their bounds, while **continuous** data types require its ranges to share the same boundary point, defined by their precision.

```sql
//int4, int8 and date are discrete, num, tsrange, tstzrange are continous 
//In both cases, at least one inclusive bound is necessary for contiguity.
select ('[1, 20]'::int4range + int4range(5, 15, '[]') );  -- [1, 21)  overlap
select ('[1, 20]'::int4range + int4range(21, 35, '[]') ); -- [1,36) contiguous
select ('[1, 20]'::int4range + int4range(20, 35, '(]') ); -- [1,36) contiguous
select ('[1.5, 20]'::numrange + numrange(20, 35.5, '(]')); -- [1.5,35.5] 
select ('[1.5, 20.5]'::numrange + numrange(21.5, 35.5, '[]')); 
    -- error no contigous
select ('[1, 20]'::int4range + '[25, 30]'::int4range ); 
    -- Error: not contiguous
```

The **subtract** operation (**-**) returns the difference between two ranges.&#x20;It returns error if the second range is contained within the first.

```sql
//It has to return a single contiguous range, depending on the order
select ( int4range('[1, 20]') - int4range(5, 25, '[]') ); -- [1,5) overlap
select ('[1, 20]'::int4range - '[20, 25]'::int4range ); 
    -- [1, 20) adjacent, removed shared bound
select ('[1, 20]'::int4range - '[25, 30]'::int4range ); 
    -- [1, 21) outside, returns first range
select ('[1, 20]'::int4range - '[5, 15]'::int4range );  
    -- ERROR, result is not contigous range.

//Its a binary operator that requires (chaining) for more than 2 ranges.
select (int4range('[1, 20]') -int4range(5, 25, '[]')) -int4range(10, 12);
    -- [1,5)
```

The **intersection** operator (**\***) returns a single range of values shared by the two input ranges.

```sql
 //The order of ranges doesn't matter
select ('[1, 20]'::int4range * '[5, 25]'::int4range );  -- [5, 20] overlap
select ('[1, 20]'::int4range * '[20,25]'::int4range );  
 -- [20, 21) adjacent, returns the bound
select ('[1, 20]'::int4range * '[25, 30]'::int4range ); 
 -- empty for external ranges
select '[1, 20]'::int4range * '[5, 15]'::int4range * int4range(1, 30);  
 -- [5, 16] for contained ranges
```

</details>

### Multirange data type syntax and operations

The **multirange** data type, introduced in PostgreSQL 14, allows for the representation of **multiple non-contiguous** ranges in a single value.\
It normalizes any overlapping or adjacent ranges within it and orders them by their lower bound.

```sql
//We add "multi" to previous range data types
create table doppio(
    id serial primary key,
    tanti int4multirange -- int8range, numrange, tsrange, tstzrange, daterange
)

//Compatible with any range data type casting
select int4multirange(int4range(12, 35), int4range(30, 48), int4range(50, 55)); 
    -- {[12,48),[50,55)}
select int4multirange('[12, 48]'::int4range,'[50, 55]'::int4range);
    -- {[12,49),[50,56)}
select int4multirange('[21, 43]'::int4range, int4range(40, 48)); -- {[21,48)}
select '{(12, 45), (30, 48), (50, 55)}'::int4multirange;  -- {[13,48),[51,55)}
```

We can apply **range opration** on multiranges:

{% tabs %}
{% tab title="The @> on multirange " %}
The containment operator (**@>**) returns true if every value within the second element is completely&#x20;contained by at least one range in the first multirange.

```sql
//It's compatible with single values
select '{[2,4), (10, 11)}'::int4multirange @> '[2,3)'::int4range; --true
select '{[1,4.5), (10.5, 11.5)}'::nummultirange @> 3.5::numeric; --true
select '{[2,4), (10, 11)}'::int4multirange @> '10'::int4; --f, exclusive bound
select  int4multirange('[2, 4)', '(10, 11)') @> '3'::int4; @> '3'::int4; -- t

select'{[2,10), (15, 25)}'::int4multirange @>'{[2,3), (5,11)}'::int4multirange
    -- false, (5, 11) isn't completely contained in [2, 10)
select int4multirange('[2, 10]','(15, 25)') @>'{[2,3), (5,8)}'::int4multirange
    -- true, both ranges are contained within one of the ranges
```
{% endtab %}

{% tab title="The && on multirange" %}
The overlap operator (**&&**) returns true if any range overlaps between the second and first value.

```sql
//It is compatible with single ranges
select '{[2,4), (10, 11)}'::int4multirange && '[2,3)'::int4range;  
    -- true all elements overlap
select '{[2,4),(10, 13)}'::int4multirange && '{[2,3),(13, 15)}'::int4multirange 
    --true one element is enought
select '{[2,4],(10, 13)}'::int4multirange && '{(4,6),(13, 15)}'::int4multirange 
    --false, no elemenyt overlaps, ) and [ arent considered overlap
```
{% endtab %}

{% tab title="The << on multirange" %}
The **strict-left** operator (**<<**) returns true if all ranges in the first multirange lie entirely to the left of all ranges in the second.

```sql
//It is compatible with single ranges
select '{[1,10)}'::int8multirange << '[100,110)'::int8range;  --true
select '{[1,10), [105, 120]}'::int8multirange << '[100,110)'::int8range; --false
select '{[1,10)}'::int8multirange<<'{[100,110), [50, 70]}'::int8multirange --t

//The strict right operator (>>) works the same way
select '{[110,130)}'::int8multirange>>'{[100,110),[50, 70]}'::int8multirange --t
```
{% endtab %}

{% tab title="The -|- on multirange" %}
The **adjacent** operator (**-|-**) returns true if any range in the first multirange is adjacent to any range in the second.

```sql
//It is compatible with single ranges
SELECT '{[2,4), (10, 11)}'::int4multirange -|- '[3,5)'::int4range;  --f, overlap
SELECT '{[2,4), (10, 11)}'::int4multirange -|- '{[4,6)}'::int4multirange;   
    -- true, ) and [ discrete data types ranges are considered adjacence
SELECT '{[2,4),(10, 11)}'::int4multirange -|- '{[4,6),(15, 20)}'::int4multirange
    -- true, only one adjacency is necessary
```
{% endtab %}
{% endtabs %}

The **addition** operator (**+**) returns a new normalized multirange value.

```sql
SELECT '{
    [3, 15], (45, 66)}'::int4multirange + 
    int4multirange('[29, 32]'::int4range , int4range(50, 60)
); -- {[3,16),[29,33),[46,66)}
```

The **subtraction** operation (**-**) returns a multirange containing parts of the first multirange that **do not** **overlap** with any ranges in the second.

The **intersection** operation (**\***) returns a multirange containing parts of the first multirange that **do overlap** with any ranges in the second.

```sql
//They are not compatible with single ranges, but with single value multiranges
SELECT '{[2, 10)}'::int4multirange - '{[5, 7)}'::int4multirange; -- {[2,5),[7,10)}
SELECT '{[2, 10), (15, 24)}'::int4multirange - '{[5, 12), (20, 30)}'::int4multirange;
    -- {[2,5),[16,21)}

SELECT '{[2, 10)}'::int4multirange * '{[5, 7)}'::int4multirange; --  {[5,7)}
SELECT '{[2, 10), (15, 24)}'::int4multirange * '{[5, 12), (20, 30)}'::int4multirange;
    -- {[5,10),[21,24)}
```

### Multirange on table column

The **upper()**/**lower()** function provides the upper/lower bound among all ranges within a multirange value.

```sql
select lower( int4multirange('[1, 5)', '(33, 43)') ); -- 1
select upper( int4multirange('[1, 5)', '(33, 43)') ); -- 43

//It returns a column of bounds from the multirange rows values.
create table multi1( 
  inizio int4multirange, fine int4multirange 
);

insert into multi1(inizio, fine) values 
  (int4multirange('[3, 6]', '(10, 15]') , '{(10, 15], [3, 6]}'::int4multirange ),
  (int4multirange('[1, 5]', '(33, 43]') , '{(4, 17], [27, 31]}'::int4multirange );

select lower(fine) from multi1;  -- lower| 3| 5|
select lower(inizio) from multi1;  -- lower| 3| 1|
```

The **range\_agg()** function aggregates individual range values from multiple rows **into a single multirange.**

```sql
//Single values merged into range and then aggregated into a multirange
create table multi(
    id SERIAL PRIMARY key,
    inizio int4, fine int4
);

SELECT range_agg(int4range(inizio, fine)) FROM multi; -- {[1,30), ...}

//It returns a normalized and ordered multirange
create table raggio(
  serie  int4range
);

insert into raggio(serie) values (('(14, 22]'), ('(1, 12]'), ('(24, 30]');

select range_agg(serie) from raggio where id < 2;  -- {[2,23)}
select * from raggio;
```

### PostgreSQL composite custom data types

PostgreSQL data types include **composite**, **enum**, **range** and **base**.

The **composite** data type defines an **object**-like structure with multiple user-defined **fields** and their paired **data type**.\
**Type casting** during INSERT validates and **adapts** the data to the composite fields. If casting is not used, PostgreSQL **validates** each field individually and produces an error if the data does not match the field definitions.

```sql
//The composite fields values are based on the INSERT order
//Type casting is necessary during arrays INSERT.
//PostgreSQL truncates the string so to adapt it ot the field
CREATE TYPE tag_info AS (
    name VARCHAR(5), importance integer
);

CREATE TABLE customtipo (
    id SERIAL PRIMARY KEY,
    tags tag_info[],  -- Define tags as an array of the custom type tag_info
    uno tag_info
);  

--We only inserted oe value
insert into customtipo (tags) values (
    array[ ('mono', 1)::tag_info, ('database', 2)::tag_info]    
    -- |tags |{"(mono,1)","(datab,2)"}|                  
);

insert into customtipo (uno) values 
    (('allungato', 33)::tag_info),    -- (allun,33)
    (('allungato', 33));              -- Error

select * from customtipo;
//Use the text output format for a more clear structure of the composite columns
id|tags                    |uno       |
--+------------------------+----------+
 1|{"(mono,1)","(datab,2)"}|          |
 2|NULL                    |(allun,33)|
```

We can SELECT a **single** composite value **column** by specifying its name within parentheses ().\
By default, SQL interprets dot notation (.) as extracting columns; the parentheses force SQL to evaluate the value as a composite, enabling direct access to its individual fields in your SELECT query.

```sql
//uno.name would try to extract "name" from a uno sub-table, not a composite
//We can also reference the FROM table c variable to access it.
select
    (uno).name AS tag_name,
    (c.uno).importance AS tag_importance
FROM
    customtipo1 c;
   
-- tag_name|tag_importance|
-- --------+--------------+
--         |              |    -- First row contains tags, not the SELECT uno
-- allun   |            33|
```

We **update** a composite data type column by returning a new composite value that includes the appended element.

```sql
//Like with tables, it allows for dot notation(.) to access composite fields values
//We don't use a method to modify the composite, we return a new one with a function
UPDATE customtipo SET tags = array_append(tags, ('web', 3)::tag_info) WHERE id = 1;
-- {"(mono,1)","(datab,2)","(web,3)"}

select (tags[2]).name from customtipo;  
name |-----+ datab| NULL|
```

<details>

<summary>Collatation on composite fields and UNNEST FROM rules</summary>

We can define the **collatable sorting** rules for **text**-based fields within the composite data type.

```sql
//remember to check the list of avaiable collation rules using
SELECT collname FROM pg_collation;

CREATE TYPE tag_info AS (
    name TEXT COLLATE "en-US-x-icu", //The closests to "en_US" collation
    importance integer
);

CREATE TABLE customtipo (
    id SERIAL PRIMARY KEY, tags tag_info[], uno tag_info
);

//Only 2 rows of values are being inserted, with multiple composite rows
INSERT INTO customtipo (tags, uno) VALUES
(
    ARRAY[
        ROW('programming', 1)::tag_info, ROW('Database', 2)::tag_info,
        ROW('apple', 3)::tag_info,       ROW('Apple', 4)::tag_info,
        ROW('Zebra', 5)::tag_info
    ],
    ROW('web', 6)::tag_info
);

INSERT INTO customtipo (tags, uno) VALUES
(
    ARRAY[
        ROW('algorithm', 7)::tag_info, ROW('Networking', 8)::tag_info,
        ROW('Beta', 9)::tag_info
    ],
    ROW('cloud', 10)::tag_info
);

//Only the first value is shown with the rest in the expansible
1| >programming >1 |web   6
2| >algorithm   >7 |cloud 9
```

We use the UNNEST function to access composite values from a **table's array column**. It returns each **array element** as a **row in a new set**. We then alias this set of composite values and their fields.

The FROM clause specifies the **data sources** in a query. It automatically performs a CROSS JOIN with the rows returned by the expressions, making the data available in the SELECT.

```sql
//We first declare the table c-variable, necessary for the unnest() expression
//We ORDER BY using the data type field for collatiation
//The ALIAS field names will depend on their position
SELECT
    c.id,            --Table id
    t as tutto,      --The entire ROW composite instance 
    t.name as parte,    
    t.importance as metta
FROM
    customtipo c,
    UNNEST(c.tags) AS t(name, importance)    --Composite fields
ORDER BY
    t.name; -- Uses the "en_US" collation

//en-US-x-icu collatation rules
algorithm  |    7|
apple      |    3|
Apple      |    4|
Database   |    2|
Networking |    8|
programming|    1|
Zebra      |    5|

//We can change the ORDER BY collatation rules on teh same data type
SELECT
    c.id,
    t.name as nome,
    t.importance as value
FROM
    customtipo c,
    UNNEST(c.tags) AS t(name, importance)
ORDER BY
    t.name COLLATE "C";
    
Apple      |    4|
Database   |    2|
Networking |    8|
Zebra      |    5|
algorithm  |    7|
apple      |    3|
programming|    1|
```

</details>

### Composite data type arguments on PostgreSQL functions

A SQL function can accept a custom data type as its **argument**, allowing us to access and manipulate its fields during each invocation.

```sql
//We use them as reusable data types that are independent of table storage.
//RAISE NOTICE uses the printf syntax, while SQL string output relies on concatenation
CREATE TYPE user_info AS (
    id INT, name TEXT, email TEXT
);

CREATE OR REPLACE FUNCTION composite_user(p_user user_info)
RETURNS TEXT LANGUAGE plpgsql
AS $$
    BEGIN
    
    RAISE NOTICE 'user ID: %, name: %, email: %', p_user.id, p_user.name,p_user.email;
    RETURN 'User " ' || p_user.name || ' " (ID: ' || p_user.id || ') processed.';
    END;
$$;

SELECT composite_user((101, 'Alice', 'alice@example.com')::user_info);
-- user ID: 101, name: Alice, email: alice@example.com
-- User " Alice " (ID: 101) processed successfully.
```

A composite data type shares its structure with a **table**, as both are **ordered collections** of named attributes with assigned data types.

Every CREATE TABLE statement implicitly defines a **corresponding composite** data type, which serves as the **row structure** for the **instances** stored in the table. The table provides persistent storage for this data, managed by its composite row type.

We can use a table's implicit composite data type as a custom argument in SQL functions, passing specific instances of users%ROWTYPE directly from a SELECT query.

```sql
//INSERT and SELECT add and retrieve row instances from the table, respectively.
//We reference the exact field names from the table.
CREATE OR REPLACE FUNCTION table_user(p_user_data users)
RETURNS TEXT LANGUAGE plpgsql
AS $$ 
BEGIN
    RAISE NOTICE 'user ID: %, name: %, email: %', 
                 p_user_data.id, p_user_data.name, p_user_data.email;
    RETURN 'User "' || p_user_data.name || '" (ID: ' ||p_user_data.id|| ') processed';
END;
$$;

CREATE TABLE users (
    id INT PRIMARY KEY, name TEXT, email TEXT
);
INSERT INTO users VALUES (104, 'Charlie', 'charlie@example.com');

SELECT table_user(u) FROM users u WHERE u.id = 104;  -- u users (data type)
-- user ID: 104, name: Charlie, email: charlie@example.com
-- User "Charlie" (ID: 104) processed successfully.

//We can convert the table ROW into a different composite type argument
SELECT composite_user(ROW(u.id,u.name,u.email)::user_info) FROM users u WHERE u.id=103
-- row user_info (data type)
```

Instead of passing a query result as a SQL function argument, we can **process the query** within a PL/pgSQL DO block.

The declared variable **data type** %ROWTYPE will hold the **row structure** of the instances returned by the SELECT ... INTO **query**. It contains the implicit composite value of the table row, allowing us to directly access and manipulate its fields.

```sql
//An anonymous DO clock doesnt need SQL arguments.
//We can UPDATE the original table within the DO block.
CREATE TABLE page (
    id SERIAL PRIMARY KEY,
    title TEXT, content TEXT
);

INSERT INTO page (title, content) VALUES
    ('My First Blog Post', 'This is the first.'),
    ('About Us', 'Learn more about our second.'),
    ('Contact Information', 'Reach out to third.');

//We specify the table name in page%ROWTYPE
DO $$
DECLARE
    my_page_row page%ROWTYPE;
BEGIN
    SELECT * INTO my_page_row FROM page WHERE id = 2;
    RAISE NOTICE 'Page ID: %,Title: %',my_page_row.id, my_page_row.title || ' concat';
    
    //The %ROWTYPE fields can be used for internal operations
    IF LENGTH(my_page_row.content) < 50 THEN
        RAISE NOTICE 'This page has short content.';
    ELSE
        RAISE NOTICE 'This page has substantial content.';
    END IF;
    
    //We UPDATE the table with the variable contaning a new %ROWTYPE value
    my_page_row.title := my_page_row.title || ' (UPDATED)';
    UPDATE page SET title = my_page_row.title WHERE id = my_page_row.id;
END;
$$ LANGUAGE plpgsql;
-- Page ID: 2, Title: About Us concat
-- This page has short content.

select * from page where id = 2;
--  2|About Us (UPDATED) |Learn more about our second.|
```

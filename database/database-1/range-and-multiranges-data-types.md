# Range and Multiranges data types

* 1
* 1
* 1

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
select '[10, 45]'::int4range && '[30, 40]'; //true
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

1

1

1

1

1

1

1

1

1

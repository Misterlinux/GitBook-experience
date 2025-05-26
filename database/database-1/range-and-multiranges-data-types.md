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

The **<<** and **>>** operators return true if the first range lies strictly at the **left** or **right** of the second.

```sql
//It compares the first range upper()/lower() with its opposite bound in the second
//It returns false for overlap and inclusive bounds
select int8range(1,10) << int8range(100,110);   -- true, 10< 100
select '[1,100]'::int4range << '[100,110]'::int4range;  -- false, 100 = 100
select '[45, 54]'::int8range >> '[12, 44]'::int8range;  -- true, 45 > 44
```

The **adjacency** operator (**-|-**) checks the boundary points between 2 ranges.&#x20;It returns true if there is no gap or overlap between the bounds.

```sql
//It checks for adjacency for both its lower and upper bounds.
//A single inclusive bound will be considered as an overlap
select '[2, 40]'::int4range -|- '[40, 120]'::int4range; -- false
select '[2, 40]'::int4range -|- '[41, 120]'::int4range; -- true
select  daterange('2025-04-15','2025-04-20') -|- daterange('2025-04-20','2025-04-25');
    -- true
select int8range(5, 12, '(]') -|- int8range(12, 49, '(]');  -- true
select '(40, 120)'::numrange -|- '[2, 40)'::numrange;   -- false
```

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

### Multiranges data type

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

The containment operator (**@>**) returns true if every value within the second element is completely\
contained by at least one range in the first multirange.

```sql
//It's compatible with single values
select '{[2,4), (10, 11)}'::int4multirange @> '[2,3)'::int4range; --true
select '{[1,4.5), (10.5, 11.5)}'::nummultirange @> 3.5::numeric; --true
select '{[2,4), (10, 11)}'::int4multirange @> '10'::int4; -- false, exclusive bound
select  int4multirange('[2, 4)', '(10, 11)') @> '3'::int4; @> '3'::int4;  -- true

select '{[2,10), (15, 25)}'::int4multirange @> '{ [2,3), (5,11) }'::int4multirange; 
    -- false, (5, 11) isn't completely contained in [2, 10)
select int4multirange('[2, 10]', '(15, 25)') @> '{[2,3), (5,8)}'::int4multirange;   
    -- true, both ranges are contained within one of the ranges
```

The overlap operator (**&&**) returns true if any range overlaps between the second and first value.

```sql
//It is compatible with single ranges
select '{[2,4), (10, 11)}'::int4multirange && '[2,3)'::int4range;  
    -- true all elements overlap
select '{[2,4), (10, 13)}'::int4multirange && '{[2,3), (13, 15)}'::int4multirange;  
    --true one element is enought
select '{[2,4], (10, 13)}'::int4multirange && '{(4,6), (13, 15)}'::int4multirange;  
    --false, no elemenyt overlaps, ) and [ arent considered overlap
```

The **strict-left** operator (**<<**) returns true if all ranges in the first multirange lie entirely to the left of all ranges in the second.

```sql
//It is compatible with single ranges
select '{[1,10)}'::int8multirange << '[100,110)'::int8range;  --true
select '{[1,10), [105, 120]}'::int8multirange << '[100,110)'::int8range;  --false
select '{[1,10)}'::int8multirange << '{[100,110), [50, 70]}'::int8multirange;  --true

//The strict right operator (>>) works the same way
select '{[110,130)}'::int8multirange >> '{[100,110),[50, 70]}'::int8multirange; --true
```

The **adjacent** operator (**-|-**) returns true if any range in the first multirange is adjacent to any range in the second.

```sql
//It is compatible with single ranges
SELECT '{[2,4), (10, 11)}'::int4multirange -|- '[3,5)'::int4range;  --false, overlap
SELECT '{[2,4), (10, 11)}'::int4multirange -|- '{[4,6)}'::int4multirange;   
    -- true, ) and [ discrete data types ranges are considered adjacence
SELECT '{[2,4), (10, 11)}'::int4multirange -|- '{[4,6), (15, 20)}'::int4multirange; 
    -- true, only one adjacency is necessary
```

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

multirange columns

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
// Some code
create table raggio(
  serie  int4range
);

insert into raggio(serie) values ('(1, 12]'), ('(14, 22]'), ('(24, 30]');

select range_agg(serie) from raggio where id < 2;  -- {[2,23)}
select * from raggio;

//It returns a single multirange containing all teh values into a multirange
create table multi(
    id SERIAL PRIMARY key,
    inizio int4, fine int4
);
SELECT range_agg(int4range(inizio, fine)) FROM multi; -- {[1,30), ...}
```

1

1

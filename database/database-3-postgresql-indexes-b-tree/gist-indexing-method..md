# Gist indexing method.

* 1
* 1
* 1

1

1

1

1

1

1

A GiST index can be used to implement an EXCLUDE constraint, which prevents rows with conflicting column values from being inserted into the table.\
The index is active during the INDEX CREATION and the CONSTRAINT ENFORCEMENT.

The index is populated by the indexed column values included in its arguments; we can specify a subset of rows to be included in the index using the WHERE keyword, and we can use functions to transform the column values that will be present in the index and used for the EXCLUDE constraint.\
The resulting values are organized into the GiST tree, which is used by the EXCLUDE constraint to perform an optimized query of the index to find conflicting values.

An EXCLUDE constraint uses its GiST index during INSERT and UPDATE operations to prevent data conflicts. It applies any functions from the index definition to the new column values and then uses the constraint's comparison operators to query the index for any conflicting rows.\
The new data is held in the RAM memory for the duration of the operation, and if the check passes, the data is committed to both the table and the index in an atomic operation that maintains table consistency.

```sql
//The returned value extends the ends of the tsrange value by the interval argument
create or replace FUNCTION sessione(tsrange, interval)
  RETURNS tsrange AS
$$
  SELECT tsrange( lower($1) - $2, upper($1) + $2 );
$$ 
LANGUAGE 'sql' IMMUTABLE;

//Example of the tsrange function returns: [11:45, 14:02]
select sessione('[15-09-2023 12:00, 15-09-2023 13:47]', '15 minutes'::interval);

//We limit the rows in the exclude GIST to the ones with sepcific user_id values
create table lista(
  user_id TEXT, pal tsrange,
  exclude using GIST(lower(user_id) with =, sessione(pal,'2 hours'::interval) with &&)
    where ( lower(user_id) in ('freeuser', 'trialuser', 'bonususer') )
)

insert into lista(user_id, pal) values 
 ('freeuser','[2021-05-12 12:00, 2021-06-07 05:00 ]'), 
 ('Freeuser','[2021-06-07 07:00, 2021-06-07 12:00 ]'), //Error, same user and overlay
 ('freeuseR','[2021-06-07 09:01, 2021-06-07 12:00 ]'), //Same user but no overlay
 ('bonususer','[2021-05-12 12:00, 2021-06-07 05:00]'),//Different user even if overlay
 ('Luka','[2021-05-12 12:00, 2021-06-07 05:00 ]');    //Different user_id 

select * from lista;
```

1

1

1

1

1

1

1

1

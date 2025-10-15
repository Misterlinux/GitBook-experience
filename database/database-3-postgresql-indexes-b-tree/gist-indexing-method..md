# Gist indexing method.

* 1
* 1
* 1

1

1

1

CONFLICT AND GIST EXCLUE PROPERTIES

//There is a difference between teh GIST beign ipmplementedinto an exclude constraint, and one for teh

//GIST tree by itself, queries,

//as in a simple query value being searched in teh GIST tree, a one-way operaon for.

//while the exclude proceeses to compare, multipe, pairs, of value compared.

//The comparison is only one for each gist value in a query, as it checkes teh conditon one directiopn only.

//while a exclude WOULD NEED to COMPARE IT 2 DIRECTION, as THE new VALUE is COMPARED as first and second element in TEH COPARISON

// X \<operator> Y and Y \<operator> X

//The GIST index, has not rules fo rthe values it can exclude, excep tof rocmpatibility, buyt teh ESXCDLUE containt adds

//constraints to the type of values it will allow to populate it with, eactly liek a unique b.-tree index wil apply teh unique clause to a

//normal index b-tree, aplying notnn native rules, to limiet the values allowe,d

//used or teh data in teh structure, to impose its rules, requires extra operator, and it has to be commutative for both, ends, and to do so it uses teh

//GIST optimizes fro check to perform this high level operation, and limits teh columns valeus being compared, only to teh ones that could.

//The GIST optimitation optimizes the continous comparisons, required to validate all GSIT values with the condition exlcude

//So teh exclude in order to assure that there is no CONFLIC between teh values that will be present in teh GIST index that it uses

//checks for teh prperties to hve the comutative properties, before allowing it in teh be used for teh index creation,

//a commutative operatior acts returns teh same reuslti for the x operator y and vice versa,

//The commutative property of an operator is contained within its OPERATION class, and the is necessary for teh exclude to use it as an operator in the

//GIST index it uses,

//The commutative functions define a no directional comparison where the dixed operator for 2 values gives the

//same result for any order of teh two values, it returns true it gets interpreted as conflic and THATS why the EXLCUE needs it,

//The exclude constrant wil enforce that npo 2 values ar in a non-directional comaprison results, which is a conflict

//to do so, itrequires specific operators to use in the GIST index it creates, enforcing have to include the

//commutational property, which allows it to exluce any conflciting value

//the operations with teh commutatinal property ensure that a true for AN OPERATION IT HAS A conflict CONFIRMS

\-- The GIST optimitation allows the comparison to be optimizes, or any new insert, without needing to check all

\-- the rules of teh xlcue are triggered form teh second insert, regardless of data creation or insert new.

1

1

(to fix)

The GIST index can replicate the structure, and use, and data types contain from other indeing methds, like B-tre, into nstalled the system catalog, adat the structure, add teh excluve features, allows for linear data indexing in a GIST, to index linear data types,

//Not included by deafutl.

btree\_gist. mosty used for exclusion constraints\
Allows for implement a linear order ino the

The index is lossy, it can retusn false positives, it requires extra scan to filter those, each entry gets\
translated into a bit sized string signature.\
It includes multiple extentions liek\
btree\_gist\
B-tree equivalent functionality for several data types\
cube\
Indexing for multidimensional cubes\
hstore\
Module for storing (key, value) pairs\
intarray\
RD-Tree for one-dimensional array of int4 values\
ltree\
Indexing for tree-like structures\
pg\_trgm\
Text similarity using trigram matching\
seg\
Indexing for “float ranges”

TSVECTOR operation on TSQUERY

The simplest way to build a GiST index is just to insert all the entries, one by one. This tends to be slow for large indexes, because if the index tuples are scattered across the index and the index is large enough to not fit in cache, a lot of random I/O will be needed. PostgreSQL supports two alternative methods for initial build of a GiST index: sorted and buffered modes.

The sorted method is only available if each of the opclasses used by the index provides a sortsupport function, as described in Section 65.2.3. If they do, this method is usually the best, so it is used by default.

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

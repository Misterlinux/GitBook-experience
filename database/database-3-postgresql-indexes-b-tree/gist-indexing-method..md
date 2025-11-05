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

1

1

The GIST insdex is uyse for speed processes, it uses its operator classes to create a tree to fast searches, all teh functins are internal, operation class that trigger after teh SQL commands that set the GIST tree, the operator class functions incldueteh inherent functins triggered that hadl ethe @@ and @> comparison for teh GIST tree values comparisons,

Adapts to its multiple allowed data types, query types, an extensive framework, which doesnt depend on comparison values or linear properties for its values like B-tree, it defies specific rules to order its, Operators used to navigate the query, and secific rules define with. Multiple operation classes contrary to teh B-tree that includes a linear ordering, aplies, for all the data types allowed within the index. The GIST aplies containment, insersection ad proiximity rules to order its data types. as it includes multiple operaator classes, for multiple data types, more, and with each defineing its rules and operators used to access the tree struture, based on teh data type. It provides its set own interface to build, a strcutrue for custom data types with defining their own rules, the framework allows.

The base for the tree organitation si predicate, a boolean that is preset in every row, which also includes the TID and key which is expressed in teh predicate. teh predicate on teh internla node COMPRISES the child row that contains predicates that satisfy the, condiion. in teh leaf present in the child row, APLIED with teh CONSISTENCY function, used to search teh tree, that compares the search predicateto the oenes in the rows, entries in teh nodes, it follows a top down order from teh rrot node, the consistency unction varies based on teh operator class of the data type, and is not related to any greater than or less as teh bò-tree is ordered, Any new values inserted into the leaf node will be optimized to increase as little as possible, if at all, the predicate defining the leaf node, while if a value is removed the predicat edoesnt get changed. dueing a split the predicates fro teh new leaf nodes gets redo from scrath, + Instead of a one dimentional range, it uses a BOUDING PROPERTY, multi dimentional, called a predicate, A range represents a one dimentional linear order, of data, comparison, while we reate BOUNDING BOX multi dimentional, focused on a shared property, dependant on teh data types. The internal node can be defined as a bigger box that inclues all teh smaller box of, broather propertt, that includes the child ones, A query on teh GIST tree includes a SEARCH PREDICATE, FOR ITS SPECIFIC VALUES BEING searched, its aplied to the root node, from, and checks which child node is CONSISTENT , with the search predicate, once it reaches teh final leaf node, its copares the contaed values in teh entry, with teh predicate, to return TRUE, The consistency function is a specialized function for the data type, and itcan use complex operators like @@, instead of the simplest in B-tree.

the difference between the btree ORDER and gist PROPERTY, as how they content of their leaf nodes is organized, a btree is based on an linear order for their single values, a single rule. for all its data types, a GIST is based on a SPECIFIC property, to create a list for teh values iwthin a leaf node, but teh prperty CHOSEN to be selected by teh bpounding box, is decided by teh set of rules,

teh enties in a GIST contain multiple properties, and one is chosen by teh operator class of tehdata type being indexed.

IMAGE EXAMPLE for aresas R-TREE (point '(1,1)'), (point '(3,2)'), (point '(6,3)'), (point '(5,5)'), (point '(7,8)'), (point '(8,6)');

select \* from points where p <@ box '(2,1),(7,4)';

1

The pginspect can accept teh GIST index value, but will return binry data in a hexodecimal format. due to teh different structures provided by duifferent operation classes The b.tree is accessed using the function bt-pag-items, used to access it, but it has a linear rule, order, structure defined by a one dimentional rule, which makes all structure the same indipendenty from the different data type and operatio classes. the pageinspect accesses the index structure, and cant retrieve the operation class behind it, ALSO, the possibility to create custom data types index, within teh GIST makes it uncapable to access it by any funciton it may have, as teh GIST index strcture depends on teh operation classs.

We need to install an extention, to access the precise number of pages, sizes. http://www.sai.msu.su/\~megera/wiki/Gevel select \* from gist\_stat('airports\_coordinates\_idx');

The also includes the sorting functions used y ORDER BY clauses to order the retruned data.The search operator is the symbol used in teh query, while the query conditon/PREDICATE , is teh complete statementa baout ehdat awe are searching in the index, and returns TRUE FALSE, including a column, an perator symbol and a values,

\--> difference between the sorting function search operator, ordering operator, As the uses teh search point in the query condition value to check teh distance between teh bounding boxes presetned in the internal nodes form teh rooot node, untill it gets to the final entry in teh leaf node, final level. disnatce clculated by teh predicate, it can understmate, include extra includes that might be farther away,

The internal point bounding idexes, are used for an initials values to prioritize the searches, for possible closets values, as the bounding boxes are chose for teh POSSIBLE minimun distance, that all of its internal points are gona include, which we confirm only for teh true distance after accessing teh final boudnig box represented by teh leaf node. as a close bounding box initially, can include its pint fart away from teh edge, while a farther box can include its poits closets. The search will include multiple braches AS LONG as teh disntace between the internla node boundng boxes ks LESS than teh true disntacne found for the initial closest box, multiple steps,

The farthet point in the first bouding box, closets bounding box, being retrieved, true distance is compared to teh other bounding box internal nodes, if closets those are checked too, the algorith ORGANIZES the, navigates the tree.

The INTERNAL NODE, ocntains a predicate and a pointers, the predicate including all teh bounding boxes containing the points in teh child nodes, A bounding box, for al the points, entries present in teh tree, organized to fit the disk page, and to fit a logical bounding bpx, and the physical space of a disk page, as to be as small as possibe, created to be, organized into. ANY NEW VALUE will be insert into teh leaf node, that defines the values in a bounding box for which predicate needs to be changed as little as possible to let the new value in, teh algorith checks all leaf nodes and calculates teh level of change they will need, abd chooses teh most fit. DURING A SPLIT the closest entries wil be organized into a new predicate that contains them, while a internla node gets created that contains all teh child node boxes included within, the nternla nde will include MULTIPLE ENTRIES, Its not a single value like teh b-tree, as it there cant be any greater than and less conditions that can be desumed form its value, for the pointers, child nodes. each with its own predicate and pointer to teh bounding box with teh values within

In a bò-tree index, the sngle value implicity creates ranges, as thats the logic of teh entire btree structure, while the internal ndoe contains a series of pointers predicate pairs, that explicitly define the child nodes being contained with eth eir TID, adn the algorith compares the seach operator for each, to make sure its gets the right one. The predicate is rated form teh specific angles of its rectable, both the operator ,, and the query value speicifed i teh query are used to compare to it, in the DISTANCE FUNCTION, the returned values are then used to be listed by teh database and compared to see whch ones shoudl be prioritized or pruned to access, start accessing the internla point values, entries, as the boudning box wil be as small as possible, as empty space reduces effectivity in teh index, the bounding box is defined by two points as teh loswest x, y and the maximum xy extracted form teh pints being contained within.

FOR BOUNDING INTERVALS, instead of bounding boex, as different data types choose, teh provided by teh operarot class, and its dynamically calculated and stored for weach disk page predicate, and useging the query value AND stored the values to forteh closests ones, based on teh data types, that configurates teh GIST remember the GIST is the organitation, tree, framework that handles teh spaces, INDEXING ENGINE, teh spaces BUT the operator class provides teh functions used to organize its specific data type, te ruels etc. Every data type, that might include a k-nn proximity query operation includes its OWN version of the distance functions, as teh GIST odenst know how to compare, organize that specific data type, Contrary to btree that has teh same rules for every data type it allows, GIST doesnt and requires the operator class to provide. It organizes them by larger bounding intervals, that include in it teh smaller ones, and is used in teh naviagting teh tree, to knwo which nodesto prioritize and which to ignore, while executing teh query operation, even if its a 1 dimentional data type, its not a linear one, a btree couldnt index it, as ordering a smaller lower bounding is not the same as define overlap i btree rules. GIST can do it not because of some GIST structure, but for the fact that it can accep operation class that include intrval,

The GIST exclude constraints, includes \~= for spacial EXACT SPCE, contrar to =, and teh -|- adjacent.

THE SEARCH TEXT FUNCTIN, GIST, is selects from a document set, a documenta that matcheshe query search conditions, it uses a specialized ata type, tsvector, and does it by using, lexemes (as the units the data is organzied in) and pointers. It contains teh LEXAMESA and the DOCUMENT position. The tsvector() copnverts the string into a series of lexames wors, all in lowercase, and with some words, repeated o too short, or too used, as DROP WORDS, while for the query we needs a specificdata type TSQUERY(), that can include logical operations for the lexemes. in teh query.

select to\_tsvector('There was a crooked man, and he walked a crooked mile'); select to\_tsquery('man & (walking | running)'); select to\_tsvector('There was a crooked man, and he walked a crooked mile') @@ to\_tsquery('man & (walking | running)');

The @@ is the only match operator usable for mathing text, but GIST is also a surface level general purpose text search, which ca ìn hanlde divides the strings into its unique words as lexemes, while GIN is a more insight index, gist organizes the lexemes into a lossy and confined reoresentation. it can produce false positiones, dueto teh compact level of the information, and adds a recheck for its scan, that cakn slow down, GIST is used for its smaller index and easy to update index, but form a more in depth and efficent index you must use GIN. A signature tree, IS ORGANIZED BY HASHING EVERY SINGLE DOCUMENT WE WANT TO GET LEXAMENES FOR, we represent the contents of the element, with categories organized in bitmasks, a series of bytes, data, where every single lexemen gets translated into a single bit and in its position where declared, its a lossy structuyre because, due to the limited number of bytes and avaiable positions, multiple words can flip tehsame, set, turn on teh same buit, so a specific word is ot assured to be there, The signatres are used to organize the tree, as EACH entry in the will contain a specific signature for teh document of teh document set its supposed to repsent, the INTERNAL NODE will include a BITWISE OR, operation, for a sinature that includes all teh entries signatire in its child nodes, The search query value gets translated tinto a signature, and matched agains the union signatures of the internal nodes, it allows it to quickly filter all the ranches that for sure DO NOT contain teh value, as a non set bit signifies the absence of teh value, then it reaches the lòeaf node, that can retrieve the position of its searched document POSITION, The different internal nodes will not be exclusive for teh values the containe between each other, but teh database will try to create specific union signatures, that arent too generic, as a only 1 bitmask would be useless. ANY new signatue added to the tree will be set in teh least enlarge union aspect, as i means flipping that alrady hasthe bit set, as to not change the union. IT CHECKS, the algorith, the entire branch that might contain teh signature, and once it gets to teh LEAF NODE, it retirevs the actual document from the entry poiter and RECHECKS the query condition. It query, algoruth, searches for BOThteh rbranches if both have all teh bits included in teh signatures, as it mush search for possibke matches, not exact, once it gets to the leaf node, it retirevs ALL THE ENTIRES that have the signature bits AND RETRIEVES rtheir actual row value, using teh tid, and aplies teh @@ operation to see ig the tsvector() contained... (?) The @@ operation is made twice wihtin a GIST index, the first is when the we compare bitmasks between each other, bitmalkss, a FAST BITWISE operaton, AND TEH TSVECRO VALUE ALL ITS WORDS GET CONVERTED, HASHED, TO A BITMASK FOR THE INDEXED, the second tme, IS ON THE leaf node, for all teh possible matches the table row is retrieved, BUT it compares the tsvecor in teh table and the seach query value stored beforehand. teh TSQUERY value stored in memory is compared to teh retirevd and converted leaf node entry, to teh second @ò.

The TSQUERY, is follows teh structure of the vector as single unique words, BUT it also includes teh SEACRH CRITERIA, as the logical operations, by the search, on the single words, as which ones is searchng for by the query with 'quick' & ('cat' | 'dog') & !'fox'. in their format optimized for searching. and you need to ptovide teh logical values for each, to\_tsquery('english', 'big & cats') unless you want i to cmpare teh simpler values as in plainto\_tsquery(). The tsquery compiles a SEARCH PLAN, a set of rukles that can be used by teh gist, The small tree organized by the tsvetor is used to navgate the gist, as it asks each internal node, the PREDICATE, they check(?), it works for bitmasks, and it uses its tsvalues to create a bitmasks that gets compared to teh prediactes of teh GIST internal NODES, but it aplies its own logical operators. The TSQUERY creates a query tree, thats used for its bitmaks, used to compare i naviagte the GIST table organized into internla nodes with PREDICATES translated into bitmakss too.

THE LEXEME, is a normalized base for of a ward, in the tsvecotr() the words get underscore and stemming, that removes any plural or verb form of teh oword to reach its base form. The tsvecotr() creates a lexemen colum (?) in the table tuple, which will be indicated by the index entru when retrieving. The position is STORED in the lexemen, including its multiple positions within teh string, using an integer, it is used by the FOLLOWED BY <-> tsquery() operator. The stop words are common words, of low semanitc value for the string, which are not converted into lexemen, The @@ is the primary operator for GIST text search queries, BUT it must not be confused with teh TSWQUERY() operations, internal, used to create the query tree ,like &&, |, ! and <->. while @@is used inteh actua query....

THE LEXEMES are physically stored in a new column in the tuple, (how?) and are referenced, the compact list of lexemes and positions, The leaf nodes,will stores multiple entries, which each SINGLE tsvector form teh corresponding table row indicated by teh TID, once retrived, due to the lossy nature of the bitmask retrieve, it will extract teh LEXEMEN value form teh column and compare to teh ORIGINAL TSQUERY() In the query, The lexemes column is created during teh CREATE TABLE for the column with ts\_vecotr() that has the lexemes not the string we planted inside Each signature wil be ong 128 bytes, regaldess of the length of teh string that got ts\_vector.

THE OPERATOR CLASS iwhin the gist that tsvector\_ops, will optimize only one type of poperations, the @@ matching one between a ts\_vector and a ts\_query. whil eth @> is used for tsquery queries like to\_tsquery('cat | dog') @> to\_tsquery('cat'), used to analyze search queries. used for when we store a series of tsquery search paramethers. compare them.

THE PREDICATE is part of teh GIST structure geeralized structure, it changes based on the data typoes that teh GIST is made of, for geometric data is bouding box, for text search is a bitmaks. The execution plan of a tsquery for a GIST tree, can include multiple entries from multiple branches dependong on its tsquery condition.

WE analyze a tsvector gist index using the GEvel select level, a from gist\_print('mail\_messages\_tsv\_idx') as t(level int, valid bool, a gtsvector) where a is not null;

We can EXTEND the data types used by the GIST, stored, ordered by, using the extentions, which allows use to order cube data, https://postgrespro.com/docs/postgresql/9.6/cube the ltree https://postgrespro.com/docs/postgresql/9.6/ltree intarray https://postgrespro.com/docs/postgresql/9.6/intarray seg https://postgrespro.com/docs/postgresql/9.6/seg And the pg\_trgm used for trigrams text searches https://postgrespro.com/docs/postgresql/9.6/pgtrgm

1

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

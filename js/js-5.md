# JS 5

* ERROR and stuff
* there is more
* accidenti

We can use **ES6** Syntax for **default parameter** in a function:

```
function defaultParameter(name = "sam") {
  return console.log( name);
}

defaultParameter("oltre")    //"oltre"
defaultParameter()           //"sam"
```

The **rest/spread operators** store and expand array/object elements.

{% tabs %}
{% tab title="Array iterals" %}
The Rest operator can store only at the end of its array.

```
//Rest collect the array values as a variable
let [uno, ...due] = [1,2,2,3,4,5,6,8]
console.log( due )                      //[2, 2, 3, 4, 5, 6, 8]

//Spread expands an army into the element
const oneToFive = [1, 2, 3, 4, 5];
const oneToTen = [...oneToFive, 6, 7, 8];    //[1, 2, 3, 4, 5, 6, 7, 8]
```

We can **spread** the **rest** variable in **any** array **index** position.

```
let deck1 = [1,500,1,2,3,55,6,5,4,6]

const [uno, due, ...rest] = deck
console.log( [due, ...rest, uno] )    //[500, 1, 2, 3, 55, 6, 5, 4, 6, 1]

//without the ...spread it would return an array
console.log( [due, rest, uno] )    //[500, [1, 2, 3, 55, 6, 5, 4, 6] , 1]
```

We can **rest** a **parameter** and use **empty commas** to skip array elements:

```
let deck = [ 100, 23, 45, 12, 34 ]

function skip( [, , ...rest] ){
  return [..rest]    
}

skip( deck )    //[45, 12, 34]
```

1
{% endtab %}

{% tab title="Objects iterals" %}
To **spread objects** you still need to use use **{}:**

```
//If we concat() objects with the same property there is overwrite
const obj1 = { foo: 'bar', x: 42 };
const obj2 = { foo: 'baz', y: 13 };

console.log( {...obj1, ...obj2} }    //{foo: "baz"; x: 42; y: 13 }

//you cant iterate objects into an array wisth spread
console.log( [ ...obj1, ...obj2] )    //ERROR, obje is not iterable

const obj = { 0: "a", 1: "b", length: 2 };
const [a, b] = obj;                //ERROR obj is not iterable

//But arrays can iterate in object
const array = [11, 21, 31];
console.log( {..array} )    //{ 0: 11; 1: 21; 2: 3
```
{% endtab %}
{% endtabs %}

It can work as a **concat**() and **split**() method, also for **Math** operations.

```
//Spread as concat() will render using the variables order
function combineArrays(arr1, arr2, arr3) {
  return [...arr3 , ...arr2, ...arr1];
}
combineArrays( [12, 34], [23, 45], [100] )    //[ 100, 23, 45, 12, 34 ]

//We can use Spread on strings
let siamo = "siamo"
[...siamo]                   //['s', 'i', 'a', 'm', 'o']
siamo.split("")              //['s', 'i', 'a', 'm', 'o']

//On Math we don't need loops/filters
let mat = [1, 25, 50, 100]
let maximus = Math.max( ...mat )              //100 
```

Which is also the better way to **clone**:

```
let newDeck = [...deck];
```

We can **parse variables** on **function return**:

```
//We can take a function returned values
const f = () => { [12, 13] }

const [a,b]= f()
console.log(a, b)    //12, 13
```

The _rest operator_ can **edit**, **add**, and keep object **properties**.

```
//We added and edited properties while keeping the rest with ...dog
const dog = {
  name: "Lucas",
  age: 7,
  breed: "cocker"
}
const dogOwner = { ...dog, owner: "Juan", breed: "cocker spaniel"}

console.log( dogOwner )         
//{ name: 'Lucas', age: 7, breed: 'cocker spaniel', owner: 'Juan' }
```

We can use **destructuring assignment** syntax to extract values from **objects**:

```
let horse = {
  name: "uni", age: 10, breed: "winn"
}

//We keep the object's property name on objects as parameters
function destructuring(obj) {
  let { name, age, breed } = obj
  return age ;
}
destructuring(horse)              //10

let {name, breed} = horse
console.log( breed )              //"winn"
```

And for _nested objects,_ we can assign**:**

```
//You can deconstruct object but you neeed to keep the property's name
let scato = {
  yuse: {
    mode: "wannabe",
    ultro: 1234
  },
  listato: [123, 456],
  alto: true
}

//so, we can assign object properties {yuse} and array [listato]
let {
  yuse: {
    mode, 
    ultro
  },
  listato: [unato, duato]
} = scato

//and we can call an objects and array element
console.log( unato + " " + mode)                        //123 wannabe
console.log( scato.listato[0] + " " + scato.yuse.mode ) //123 wannabe

```

You can also use **deconstruct objects as parameters**, but need to keep the property name:

```
//The decostructed properties will pass the property VALUE
const meta11={
    uno: "darth",
    more:{
        lazer: "guns",
        ammo: "fire"
    }
}

function show11( {more:{lazer}} ){
    console.log( lazer )
}

show11(meta11)        //"guns"
//we can also have objects parameters AND default values in functions

function drawChart({ size = 'big', coords = { x: 0, y: 20 }, radius = 25 } = {}){
  console.log(size, coords, radius);
}

drawChart( {
  coords: { x: 18, y: 30 },
  radius: 130,
});
//"big {x: 18, y: 30} 130", ALSO, coords object will return both default properties
//ONLY IF the coords: argument is totally absent

```

In **functions** we can also return assigns:

```
function guiss(q,w){
    let tron = q+w
    let bron = q-w
    return [tron, bron]
}

let [gui, pui] = guiss(5,1)        //the returns will be in array order
console.log( gui, pui )            //6, 4
```

We can deconstruct an array of function **return results**:

```
//function to get the longest word in a string
//without the rest pattern we couldn't work on the array by .split() and .map() for length
function longino(sent){
  return Math.max(
    ...sent.split(" ").map(word=> word.length )
  )
}

console.log( longino("Lorem ipsum dolor sit amet consectetur, adipisicing elit. Assumenda, consequatur?"))

```

And in **function parameters**:

```
let obb = {
    type: "working",
    uno: 123,
}
//here we need to use the same name in the objects
const dritto = ( {type, uno } ) => (type, uno)
console.log( dritto(obb) )                      //working 123

//we can nest a new object to the existing object
obb.new = {
    citta: "unova",
    number: 34
}
//and to call it in the parameter we just need an extra {} for the object
const dentro = ( {new:{number} } ) => console.log(number + " this is the nested values")
dentro(obb)                  //34 this is the nested values

let options = {
  title: "Js book",
  items: ["Item1", "Item2"]
};

//we create a default property in the function parameter
function showBook({
  title = "Javascript",
  pages = 200,
  species  = "programming",
  items = []    //we get the default things EXCEPT THE ITEMS THAT ARE GONNA BE FROM OBJECT
}) {
  console.log(`${title} ${species} ${pages}`); // Javascript programming 200
  console.log(items); // Item1, Item2
}

showBook(options);  //setting the object used

```

We can also **Loop** through a **deconstruct object parameter**:

```
//We loop trought an array of objects

const people = [
  {
    name: 'MIKE',
    family: {
      mother: 'Jane Smith',
      father: 'Harry Smith',
      sister: 'Samantha Smith',
    }
  },
  {
    name: 'TOM',
    family: {
      mother: 'Norah Jones',
      father: 'Richard Jones',
      brother: 'Howard Jones',
    }
  }
];

//while being const we can't modify property n and father, AND first we deconstruct
//Then we loop with OF array
for (const { name: n, family: { father } } of people) {
  console.log(`Name: ${n}, Father: ${father}`);
}

//Name: MIKE, Father: Harry Smith; Name: TOM, Father: Richard Jones

```

And we can also get **.entries()** for property/key-value pairs, **array iteration**:

```
//arrays will have their index
let frut = [123, "wall", "proll"]   //we get index and value
const f = frut.entries()          

for( const entry of f){
  console.log( entry )              //[ 0, 123 ],[ 1, 'wall' ],[ 2, 'proll' ]
}


//for Objects we would need a loop to show the array iteration object
let ultron = {
  doing: "firsto",
  dell: "secondo"
}
Object.entries(ultron)      //[ [ 'doing', 'firsto' ], [ 'dell', 'secondo' ] ]
//we get the an array with arrays of pairs

const l = Object.entries(ultron)
for(let x of l){
  console.log( x )          //  ['doing', 'firsto'], ['dell','secondo']
}
```

1

1

**Error is a javascript object**, with **.message** and **.name** being its main **property**:

```
//we can create Error objects with their message, THEN throw them if we want to block
//the code with red messages

let errore= "Error number " + Math.random()
const blokka= new Error(errore)    //we include in () the Error.message

console.log(blokka)
console.log(blokka.message)
throw blokka                       //throw new Error(errore)

//when we throw we block the entire code sequence

```

<figure><img src="../.gitbook/assets/ERRORATO.PNG" alt=""><figcaption><p>we get the error printed and its .message property</p></figcaption></figure>

There are **Built-in constructor errors** SyntaxError, ReferenceError and TypeError:

```
//their object.name is included

let error = new ReferenceError("this is the argument");

console.log(error.name);    // ReferenceError
console.log(error.message); // this is the argument

```

To Error handling the code we use **TRY** and **CATCH**:

```
//We use TRY to see if we get a throw Error, if we do we CATCH the (error) and perform an action

try{
    throw new Error("printed after try")
}catch(error){
    console.log(error.message)        //printed after try
    //we get printed the error.message without blocking the code 
}

```

**Try** and **Catch** will work only on runnable code:

```
//if the code itself has syntaxErrors it won't work

try {
  {{{{{{{{{{{{
} catch (err) {
  console.log("this won't work")
}

```

If errors occur in "scheduled code" like setTimeout(), we need to place the Try and Catch inside:

```
//The function will be executed after the try catch, so it won't work

try {
  setTimeout(function() {
    //won't be catched
  }, 1000);
} catch (err) {
  alert( "won't work" );
}

//catching the entire setTimeout function()
setTimeout(function() {
  try {
    error;         //will be cought
  } catch(err) {
    alert(err)
  }
}, 1000);

```

When **parsing** some **JSON() code** into a **Js object** we can test more errors:

```
try {
  let jj= { errorato }      
  let user = JSON.parse(jj); 
} catch (err) {
  console.log( err );      //ReferenceError for errorato not being defined
}

```

We may need to **throw new Errors** for Errors that don't stop the runtime code:

```
//This code returns undefined, but we need for it to be visible as an error

let jia = '{ "age": 301 }'; 

try {
  let userino = JSON.parse(jia); 

  if(!userino.name){
    throw new SyntaxError("why no name?")
  }
  
} catch (err) {
  console.log( "even if .name returns undefined, we need it as an Error" );
}

```

We use **reThrowing** when we need a specific type of Error:

```
//Catch gets an object, we check if it's an instance of the error we want

let jin = '{ "age": 30 }'; 

try {
  uker = JSON.parse(pizza);   //object being not defined
} catch (err) {

  if (err instanceof ReferenceError) {
    console.log('just ReferenceError'); 
  }
}

```

We can have multiple and specific **Try, Catch** in and out of a function:

```
//The function will return an error only for a specific error

function readData() {
  let json = '{ "age": 30 }';

  try {
    blabla();                  //this ReferenceError won't be cought  
  } catch (err) {
  
    if (!(err instanceof SyntaxError)) {
      throw err;               //will block the runtime only on SyntaxError
    }
  }
}

//Any error in the function will be cought by this catch tho
try {
  readData();
} catch (err) {
  console.log( "EXTERNAL : " + err ); // caught any!
}

```

If we need code executed _no matter if an error gets caught_ or not we use **finally{}:**

```
//We create an alert with Error catch/ or not

try {
  alert( 'try' );
  if (confirm('Make an error?')) BAD_CODE();
} catch (err) {
  alert( 'catch' );
} finally {
  alert( 'finally' );
}

//the finally alert will play in any case

```

Without a Catch{}, Finally{} will execute **right before** the error code crashes, even before the _return_ in the try{} _:_

```
//Even if the function returns a ReferenceError, we will get the finally

function funcin() {
    console.log(nonBe)
}

try{
  funcin()
}finally{
  console.log("play before crash")    //just before the crash
}

```

In this **Fibonacci** example we use outside variables and Date():

```
//we get an input alert with the NUMbers to have in the secuence

let num = +prompt("Enter a positive integer number?", 35)
let diff, result;

//de define the function fib()
function fib(n) {
  if (n < 0 || Math.trunc(n) != n) {
    throw new Error("Must not be negative, and also an integer.");
  }

  return n <= 1 ? n : fib(n - 1) + fib(n - 2);
}

let start = Date.now();

try {
  result = fib(num);
} catch (err) {
  result = 0;
} finally {
  diff = Date.now() - start;
}

alert(result || "error occurred");
alert(`execution took ${diff}ms` );
//comparing the Date() we get the ms it took for the operation, be it error or not

```

We **extend objects to** have specific **errors**:

```
class CustomError extends Error {
    //if it has a constructor() you need to include a Super() for it to work
}

try {
    throw new CustomError();
} catch (error) {
  if (error instanceof CustomError) {
    console.log('The error thrown is an instance of the CustomError');
  }else{
    console.log("This didn't work")
  }
}

```


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

We can also use the **spread/rest operator** on arrays and objects:

```
//The REST operator can store the latter portion of the array,
//It has to be the last element of the array because it can't be followed by a comma
let [uno, ...due] = [1,2,2,3,4,5,6,8]
console.log( due )                      //[2, 2, 3, 4, 5, 6, 8]

//The SPREAD operator can sum arrays or use it to perform operations
const oneToFive = [1, 2, 3, 4, 5];
const oneToTen = [...oneToFive, 6, 7, 8, 9, 10];

console.log(oneToTen)        //[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

//We can use it as .concat() method followng the order of the function parameters
function combineArrays(arr1, arr2, arr3) {
  return [...arr3 , ...arr2, ...arr1];
}
combineArrays( [12, 34], [23, 45], [100] )    //[ 100, 23, 45, 12, 34 ]

//Or as .split("") method on strings
let siamo = "siamo"
[...siamo]                   //['s', 'i', 'a', 'm', 'o']
siamo.split("")              //['s', 'i', 'a', 'm', 'o']

//Or for Math operations without needing to loop/filter the array elemets
let mat = [1, 25, 50, 100]
let maximus = Math.max( ...mat )              //100 

```

This depends on **iterals,** including objects, **spread** enumerates/expands its elements while **rest** "condenses" them:

```
//we can .concat() objects, but if same properties overwrite happens

const obj1 = { foo: 'bar', x: 42 };
const obj2 = { foo: 'baz', y: 13 };

console.log( {...obj1, ...obj2} )    //{foo: "baz"; x: 42; y: 13 } including a [[prototype]]

//BUT you won't be able to iterate an object into an array in any way

const obje = { key1: 'value1' }
const hel = { key2: "value2"}
console.log( [ ...obj, ...hel] )    //ERROR, obje is not iterable

const obj = { 0: "a", 1: "b", length: 2 };
const [a, b] = obj;                //ERROR obj ot iterable

//BUT ALSO you can iterate arrays into objects
//in an array, all indices are enumerable's own properties 

const array = [11, 21, 31];
console.log( {..array} )    //{ 0: 11; 1: 21; 2: 31 }

```

Also kind of fringe but we can **parse variables over functions**:

```
//We can take a function returned values

function f(){
    return [12, 13]
}

const [a,b]= f()

console.log(a, b)    //12, 13

```

This allows us to work with **Index** _without_ \[] or methods:

```
//For example by using REST on the other arrays elements, we don't get an array in this case
//instead of using .shift()
let deck = [1,1,1,2,3,4,55,6,6,5,4,6]

function getFirstCard(deck) {
    const [uno, ...rest] = deck
    return uno
}

console.log( getFirstCard(deck) )        // 1  

//and we can use the SPREAD to change the order of array indexes
let deck1 = [1,500,1,2,3,55,6,5,4,6]

function getFirstCard(deck) {
  const [uno, due, ...rest] = deck
  return [due, ...rest, uno]
}

console.log( getFirstCard(deck1) )        //[500, 1, 2, 3, 55, 6, 5, 4, 6, 1]

```

As for using them **as parameters and variables** remember that:

```
//we can destruct the array parameter of a function

let deck = [ 100, 23, 45, 12, 34 ]

function first( [uno,due, ...resto ]=deck ){
  return [...rest, uno, due]      //[45, 12, 34, 100, 23]
  return [rest, uno, due]         //[Array(3), 100, 23]
}

//resto variable can be used without the ..., instead of just passing its values it becomes an array
//we can use it to also pick indexes

function second([,due]= deck ){
  return due          //2
  return [due]        //[2] if you need it as an array
}

```

****

****

**Error is a javascript object**, with **.message** being its main **property**:

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

To work with Errors _without blocking the code_ we use **TRY** and **CATCH**:

```
//We use TRY to see if we get a throw Error, if we do we CATCH the (error) and perform an action

try{
    throw new Error("printed after try")
}catch(errorino){
    console.log(errorino.message)
    //we get printed the error.message without blocking the code 
}

```


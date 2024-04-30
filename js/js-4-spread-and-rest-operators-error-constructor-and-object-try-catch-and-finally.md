# JS 4, spread and rest operators, Error constructor and object, TRY, CATCH and FINALLY

* [Error Object and TRY, CATCH, and FINALLY constructors.](js-4-spread-and-rest-operators-error-constructor-and-object-try-catch-and-finally.md#error-constructor-and-object)

We can use **ES6** Syntax for **default parameter** in a function:

```jsx
function defaultParameter(name = "sam") {
  return console.log( name);
}

defaultParameter("oltre")    //"oltre"
defaultParameter()           //"sam"
```

The **rest/spread** operator is a **syntax** that creates a copy of an iterable object, it can unpack and edit existing objects.&#x20;

Its syntax _doesn't mutate_ the iterable, it's not a function or a loop and we can't access the iteration process.

```jsx
//The longer the array is, the more complex the copy operation becomes
//The push() method keeps its complexity indipendently of the length
//The push() will print the new length of the mutated array.

let base = [1, 2, 3, 4, 5]	
let base1 = [...base, 12]	
console.log( base1 )   //(6)[1, 2, 3, 4, 5, 12]
console.log( base )    //(5)[1, 2, 3, 4, 5]

let mosso = [1, 2, 3, 4, 5]
console.log( mosso.push(13) )	//6
console.log( mosso )		//(6)[1, 2, 3, 4, 5, 13]
```

{% tabs %}
{% tab title="Array iterals" %}
The Rest operator can store only at the end of its array.

```jsx
//Rest collect the array values as a variable
let [uno, ...due] = [1,2,2,3,4,5,6,8]
console.log( due )                      //[2, 2, 3, 4, 5, 6, 8]

//Spread expands an army into the element
const oneToFive = [1, 2, 3, 4, 5];
const oneToTen = [...oneToFive, 6, 7, 8];    //[1, 2, 3, 4, 5, 6, 7, 8]
```

We can **spread** the **rest** variables in **any** array **index** position.

```jsx
let deck1 = [1,500,1,2,3,55,6,5,4,6]

const [uno, due, ...rest] = deck
console.log( [due, ...rest, uno] )    //[500, 1, 2, 3, 55, 6, 5, 4, 6, 1]

//Without the ...spread it would return an array
console.log( [due, rest, uno] )    //[500, [1, 2, 3, 55, 6, 5, 4, 6] , 1]
```

We can **rest** a **parameter** and use **empty commas** to skip array elements:

```jsx
let deck = [ 100, 23, 45, 12, 34 ]

function skip( [, , ...rest] ){
  return [..rest]    
}
skip( deck )    //[45, 12, 34]
```
{% endtab %}

{% tab title="Objects iterals" %}
To **spread objects** you still need to use **{}:**

```jsx
//If we concat() objects with the same property there is an overwrite
const obj1 = { foo: 'bar', x: 42 };
const obj2 = { foo: 'baz', y: 13 };

console.log( {...obj1, ...obj2} }    //{foo: "baz"; x: 42; y: 13 }

//you cant iterate objects into an array with spread
console.log( [ ...obj1, ...obj2] )    //ERROR, obje is not iterable

const obj = { 0: "a", 1: "b", length: 2 };
const [a, b] = obj;                //ERROR obj is not iterable

//But arrays can iterate in object
const array = [11, 21, 31];
console.log( {..array} )    //{ 0: 11; 1: 21; 2: 3
```

The **destructuring assignment** syntax extracts **properties** by **keep**ing their **names**:

```jsx
let horse = {
  name: "uni", age: 10, breed: "winn"
}

function destructuring(obj) {
  let { name, age, breed } = obj
  return age ;
}
destructuring(horse)              //10

let {name, breed} = horse
console.log( name )              //"uni"

//We set the extracted property's new name with
let {name: nuovo, breed } = horse
console.log( nuovo )             //"uni
```

The _rest operator_ can **edit**, **add**, and keep object **properties**.

```jsx
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

We **extract nested** objects with double **{}**:

```jsx
let scato = {
  yuse: {
    mode: "wannabe",
    ultro: 1234
  },
  listato: [123, 456],
  alto: true
}

let {yuse: {mode, ultro},listato: [unato, duato] } = scato

console.log( unato + " " + mode)                        //123 wannabe
console.log( scato.listato[0] + " " + scato.yuse.mode ) //123 wannabe
```

We can also **Loop** through a **deconstructed object parameter**:

```jsx
//We loop through an array of objects
const people = [
  {
    name: 'MIKE',
    family: {
      mother: 'Jane Smith',
      father: 'Harry Smith',
      sister: 'Samantha Smith',
    }
  },
  ...
];

//We deconstruct and OF loop
for (const { name: n, family: { father } } of people) {
  console.log(`Name: ${n}, Father: ${father}`);
}

//Name: MIKE, Father: Harry Smith; Name: TOM, Father: Richard Jones
```
{% endtab %}
{% endtabs %}

It can also work as a concat() and split() method for **Math** operations.

```jsx
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

```jsx
let newDeck = [...deck];
```

We can **parse variables** on **function return**:

```jsx
//We can take a function returns values
const f = () => { [12, 13] }

const [a,b]= f()
console.log(a, b)    //12, 13
```

And **deconstruct** functions' arrays/objects **returns**.&#x20;

```jsx
function guiss(q,w){
    let tron = q+w
    let bron = q-w
    return [tron, bron]
}

let [gui, pui] = guiss(5,1)        //the returns will be in array order
console.log( gui, pui )            //6, 4
```

### Error constructor and Object

**Error is a javascript object**, with **.message** and **.name** being its main **property,** and when **throw** it will stop the code.

```jsx
//new Error being its constructor
let errore= "Error number " + Math.random()
const blokka= new Error(errore)    //the argument being the error.message

console.log(blokka)
console.log(blokka.message)
throw blokka                       //throw new Error(errore)
```

<figure><img src="../.gitbook/assets/ERRORATO.PNG" alt=""><figcaption><p>we get the error printed and its .message property</p></figcaption></figure>

There are **Built-in constructor errors** SyntaxError, ReferenceError, and TypeError:

```jsx
//their object.name is included
let error = new ReferenceError("this is the argument");

console.log(error.name);    // ReferenceError
console.log(error.message); // this is the argument
```

To _Error handling_, we **TRY** (the throw error) and **CATCH** (a response) without breaking the code:

```jsx
//Both need to share the same space (like in a function)

try{
    throw new Error("printed after try")
}catch(error){
    console.log(error.message)        //printed after try
}
```

**TRY** can also catch standard code errors:

```jsx
try {
  let jj= { element }      
  let user = JSON.parse(jj); 
} catch (err) {
  console.log( err );      //ReferenceError for element not being defined
}
```

<details>

<summary>Filtering and reThrowing Errors</summary>

We can put **TRY conditions** to trigger and catch a **new Error**():

```jsx
let jia = '{ "age": 301 }'; 

try {
  let userino = JSON.parse(jia); 

  if(!userino.name){
    throw new SyntaxError("why no name?")
  }
  
} catch (err) {
  console.log( "There is no name, use a backup" );
}
```

We **filter** the **error**.name with **instanceof**  on **catch**{}

```jsx
catch (err) {
  if (err instanceof ReferenceError) {
    console.log( "There is no name, use a backup" );
  }
}
```

In this **Fibonacci** example we use outside variables and Date():

```jsx
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
//comparing the Date() we get the ms it took for the operation, 
//be it error or not
```

</details>

The **finally{}** code gets executed right **before** an error is **catch**:

```jsx
//finally{} can trigger without a catch{}

try {
  alert( 'try' );
  if (confirm('Make an error?')) BAD_CODE();
} catch (err) {
  alert( 'catch' );
} finally {
  alert( 'finally' );
}
```


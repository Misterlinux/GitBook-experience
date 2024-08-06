# JS 1: variables, functions, parameters, array methods, data types, Math operations  and HTML implementation

* [Math Operations, Booleans and Arrays methods](js-1.md#math-operations-booleans-and-arrays.)
* [Js in HTML implementation](js-1.md#js-in-html-implementation)
* [Js in Dom with array and events](js-1.md#undefined)

### Var, const, and functions.

**Javascript** is an **Object-Oriented language** _based_ on **Prototypes** (not on classes )

**var, const, and let** are **keywords** used to _store data_:

{% tabs %}
{% tab title="var, let" %}
**let** was introduced in 2015 and declares a **block-scoped**, **local variable**.

```jsx
//The assign operator = stores the value, we repeat the assign to edit it
let uno = 'coming'
uno = 'The javascript is ' + uno

console.log(uno)        //"The javascript is coming"

//multiple assigns
let primo= 1, secondo= 2, terzo= 3;

//ALSO, we can only use _ or - on let names
```

We can create **multi-line** **strings** using **' '** and **/n.**

```jsx
let poetry = `
Nel mezzo del cammin di nostra vita \n
mi ritrovai per una selva oscura, \n `

console.log( poetry )        //Nel mezzo del cammin di nostra vita
                             //mi ritrovai per una selva oscura,
```
{% endtab %}

{% tab title="Const" %}
Data stored in **const** cannot be re-assigned with =;

**const** declares a block-scoped, _read-only_ named constant.

```jsx
//it can't be re-assigned with =,will result in "constant value error"
const UNO = 20

console.log(UNO + 3)    //23
//can change for single instances
```
{% endtab %}
{% endtabs %}

<details>

<summary>Code Blocks and function/block scope in variables</summary>

JavaScript **blocks** are code sections enclosed in curly brackets **{}**, and functions are a type of block that can be invoked.

* The let variable is **block-scoped**, it's only accessible within the block it's declared in.&#x20;
* The var variable is **function-scoped**, if declared in a block that's not a function, it becomes part of the global object, making it accessible anywhere.

<pre class="language-jsx"><code class="lang-jsx"><strong>//Variables accessible outside blocks
</strong>if (true) {
  var x = 10;
}
console.log(x); //10

if (true) {
  let y = 20;
}
console.log(y); //ReferenceError: y is not defined
</code></pre>

</details>

**Functions** are blocks of code used to _perform tasks when invoked:_

{% tabs %}
{% tab title="function()" %}
you can use the **function** keyword and a **reference** name to create a function:

```jsx
function sum(){
  return console.log(12 + 15)
}

//A function executes only when calling its reference with ()
sum()        //27
```

**return will stop execution** and return values, there are no _implicit_ returns in javascript.
{% endtab %}

{% tab title="Arrow function" %}
Instead, we can use the **const** keyword (for the _identifier_) and the fat arrow:

```jsx
const tut = () =>{
  var wel = 12;
  var tre = 10;
  return console.log(wel+tre)
}

//if the function just returns a value, you don't need {} or return
//the structure is always keyword/parameters/operations
tut();
//result 22
 
```

And to return an object we:

```
//with no return but also extra ()

const addUpTwoNumbers = (num1, num2) => ({ uno:num1, due:num2});

```
{% endtab %}
{% endtabs %}

In Javascript, all **functions are object methods**, be it from constructor or global object.

**Parameters** are values that are passed to the function and can be used locally in the function:

```jsx
//we can use multiple values for the function operations
function lot(num1, num2){
  var oltre = num1+num2;
  return console.log(oltre);
}

lot(5, 8);  //13
lot(1, 4);  //5
lot(4, 8);  //12

//parameters will be assigned based on their position, so num1=5 and num2=8
//parameters are LOCAL to the function, defined inside of it.
```

**Arguments** are the actual **values** passed when _invoking_ the function:

```jsx
//Invoking the lot(num1,num2) function 

lot(5, 8);    //assigning the num1, num2 Parameters the 5,8 Arguments
```

We can also implement **function composition**, using the return as an argument:

```jsx
//the function returns another function
function translate2d(dx, dy) {

  return function (x, y){
    return [dx + x, dy + y]  
  }
}

const move2x = translate2d(2, 0);    //move2x() is the returned function 
const result = move2x(4, 8);         //result is the returned operation result
console.log( result )                //[6, 8]
```

We can use the "parent" function to store **parameters** and **return** values of **nested functions**.

<details>

<summary>Nested functions with function arguments guide</summary>

We can use variables on the parent function to **store return values on multiple invoked functions.**

```jsx
//we declare the variables but set their value later
function memoize(f) {
  let preX, preY, preR
  
  return function (x, y) {
    if (preX === x && preY === y) {
      console.log("already casted")
      return preR
    }
    preX = x
    preY = y
    return preR = f(x, y)
  }
}
```

We pass a **function** (without () so it won't execute) argument as a **parameter**:

```jsx
//The memo1 stores the f(x,y) which is [5,5]
function addition(x,y){
  return [x,y]
}

const memo1 = memoize(adding );      
memo1(5, 5)                          
```

On repeated arguments we:

```jsx
memo1(1, 5)        //[1,5]
memo1(5, 5)        //[5,5]
memo1(5, 5)        //[5,5] "already casted"
```

</details>

We can also implement the _**spread operator**_ for function returns and have _multiple parameter functions._

<details>

<summary>Multi-parameter nested functions guide</summary>

We create **2 different parameter functions,** the f(x,y) array result will be **spread** and then used as a parameter again.

```jsx
function uni(x,y){
  return [x+3, y+1]
}

function dui(x,y){
  return [x*2, y*3]
}

function composeTransform(f, g) {
  return function (x, y) {
    return g(...f(x, y))
  }
}
```

The **order** of the parameters **functions** changes the result:

```jsx
let tent = composeTransform( uni, dui)  //[[2+3]*2 , [1+1]*3] 
tent(2,1)                               //[10,6] 

let tent1 = composeTransform( dui, uni)//[[2*2]+3, [1*3]+1 ] 
tent1(2,1)                             //[7,4]
```

</details>

We can implement **Variables** as functions when using **function combinations**:

```jsx
//We create 2 predicate functions 
function negative(x) {
    return x < 0;
}
function positive(y) {
    return y > 0;
}

//we use them as parameter functions
function oppure(p1, p2) {
    return function(x) {
        return p1(x) || p2(x);
    }
}

let nonzero = oppure(negative, positive);
nonzero(-5)         //(negative) true || (positive) false == true
```

We can **nest** more **predicate functions.**

<details>

<summary>Multiple nested predicate functions guide</summary>

We create **predicates** about string length, we include a **different parameter (y)** that won't be in the parameter function but in the _parameter of the variable that invokes the parameter functions_.

```jsx
function islonger(x){
    return function(y){
        return y.length > x.length
    }
}

function isshorter(x){
    return function(y){
        return y.length < x.length
    }
}

function not(p) {
    return function(x) {
        return !p(x);
    }
}

function mixend(x1, x2){
    return function(x){
        return x1(x) && x2(x)
    }
}

//while using them as parameters functions
function oppure(p1, p2) {
    return function(x) {
        return p1(x) || p2(x);
    }
}
```

We store the **parameters functions** in a **variable** and then add a parameter to invoke the functions.

```
//we make the first element the result of islonger() and the second
//the result of && between isshorter() and not(islonger())
let con= oppure(
    not(islonger("lungo")), 
    mixend( isshorter("hint"), not(islonger("allunga") ))
)

con("quantolungo") 
//islonger("lungo") == True ("quantolungo"(y)> lungo(x))
//not(islonger("lungo") )== False (re-does the function but ! the result)
//isshorter("hint")== False ("quantolungo"(y)< hint(x) )
//not(islonger("allunga"))== !True == False
//Mixend(...) == False && False == False
//oppure(...) == False || False == False

con("lil")
//islonger("lungo")== False
//not(islonger("lungo"))== !false == True
//isshorter("hint")== True
//not(islonger("allunga"))== !False == True
//Mixend(...)== True && True == true
//oppure(...)== True || True == True
```

</details>

### --------------Intermission-----------------

We **Interpolate** variables into HTML elements using _**Ternary Operators**_:

We use **template strings** (template iterals) to <mark style="background-color:blue;">**interpolate**</mark> variables and expressions into a string, including _**Ternary Operators**_

```jsx
//we can use `` or the +
let greetingStart = "Hallo "
let name = "dude"

const greeting = `${greetingStart}, my name is ${name}`;
let ecco = greetingStart + ", My name is " + name

console.log(greeting) / console.log(ecco)     //Hallo , My name is dude
//The backslash \ to escape special syntaxis
console.log(`${name} with the \{} and the \$`)  //"dude with the {} and the $"

//and for ternary logic we put the function between the ${}
const grade = 95;
`You have ${grade > 90 ? 'passed' : 'failed'} the exam.`;  //You have passed the exam
```

<details>

<summary>How to enable the backtick on the keyboard</summary>

Depending on your language your keyboard might not have the backtick (\` \`).

Download the [developer's keyboard](https://1drv.ms/u/s!ArqXUvKZFFNH70KlRzmg1fmF4e\_K?e=a3eWg9), and change the keyboard layout by pressing:                                **windowsIcon + spacebar**, use space to change the layout.

It will become avaiable using **Alt Gr + '**

</details>

**The console** is where we run scripts and commands.

### Math Operations, Booleans, and Arrays methods.

Most Math operations work the same in Javascript **+, -, \*,** and **/** following the **Pendas** priority system, we use **==** for comparisons (while = is to assign variables values).

```jsx
//We can also define pow with:
3 ** 2 == 9 == true
Math.pow(3,2) == 9

//and we can get the modulus (the rest after division)
10 % 3 == 1 ( 3*3 +1 )

//we can also use shorthand on operations
3 += 5 (result 8)
20 %= 3 (will be 2)
```

We can also perform **Math.\_\_\_()** operations, Math is a **Global Built-In** Object for Mathematics:

```jsx
//round() will round up the number based on the decimal
//ceil() and floor() round up and down without checking the decimal
Math.round(12.3) == 12 
Math.floor(12.3) == 12 
Math.ceil(12.3) == 13 

//It can returns the P greek
function degToRad(degrees) {
  return degrees * (Math.PI / 180);
};

function radToDeg(rad) {
  return rad / (Math.PI / 180);
};

//we can type long numbers using _
//1000000 = 1_000_000
```

The **NaN** (not a number) operations are as such:

```jsx
//we have the operator isNaN() in case, and NaN isn't === o !== to itself
NaN === NaN;        // false
Number.NaN === NaN; // false
isNaN(NaN);         // true
isNaN(Number.NaN);  // true
Number.isNaN(NaN);  // true

function valueIsNaN(v) { return v !== v; }
valueIsNaN(1);          // false
valueIsNaN(NaN);        // true
valueIsNaN(Number.NaN); // true
```

**Arrays** are objects variables that can hold more than one value and one _typeof()_ data:

```jsx
var list = [1, 2, 3]

//elements can be edited by their index(starting from 0)
list[1] = "new" //[1, "new", 3]
list.length == 2 

//strings are considered arrays of letters and so
"welcomed"[3] == "c" / "welcomed".length == 8
```

We can check its built-in _methods_ and _properties_ in **its \[\[prototype]] property,** like **concat()** or **.length:**

![](<../.gitbook/assets/ArrayObj (1).PNG>)

**Methods** on the other hand are **actions** on objects, and they need () because they are function:

<details>

<summary>Array push(), pop(), shift(), unshift(),fill() and splice() methods guide</summary>

We can use methods on **strings,** and we can **chain** them:

```jsx
//trim() removes start and end empty spaces
"  its m i n us ".toUpperCase().trim()    //"ITS M I N US"
```

For arrays, we can use **push(), pop(), shift()** and **unshift():**

```jsx
let row = [12]
//push adds at the end of the array, and returns array.length
row.push(13) //row== [12,13] // console.log(row.push(13))== 2

//pop will cut and return ONLY the last element
row.pop() // row == [12] // console.log(row.pop())== 13

//shift will cut and return the first element of the array
row.shift() // row== [13] // console.log(row.shift())== 12

//unshift() will ADD at the start of the array 
row.unshift(10, 11) //row== [10,11,12,13] //console.(row.unshift(10, 11))== 4
```

Similar to splice() the **fill()** method uses array indexes to overwrite the array.

```jsx
[1, 2, 3, 4].fill(0, 2, 4)  //[1, 2, 0, 0]
[1, 2, 3, 4].fill(5, 1) //[1, 5, 5, 5]
[1, 2, 3, 4].fill(6) //[6, 6, 6, 6]
```

We use **.splice( \[starting array index ], cut elements number, elements added )** to substitute **multiple** array elements, remember that the **starting index is included** in the splice.

```jsx
let longi = [1,2,3,4,5]
longi.splice(3, 1, "indeed") // longi== [1,2,3,"indeed",5]
//console.log(longi.splice(3, 1, "indeed"))== [4] //the cut elements

longi.splice(1, 2, "we change", "two") // longi== [1,2,"we change","two",5]

//we can add elements if we cut 0 elements
longi.splice(2, 0, "new") // longi==[1,2,"new",3,4,5]

//we can cut elements if we don't add any
longi.splice(3, 2) // longi== [1,2,3]

//OR we can include elements starting from an index using only 1 argument
longi.splice(1) // longi= [1]
console.log(longi.splice(1)) == [2,3,4,5]

//we can use it for Capital letters
let oltre = "minimal"
oltre[0].toUpperCase() + oltre.splice(1) == Minimal
```

Other methods like **.includes()** and **.startsWith()** will return _true/false_:

```jsx
//We can't use Regex expressions here (/guess/i)
longi.includes(1) == true
"siamo".startsWith("S") == False
```

We can use **typeof** to check data type.

```
typeof longi == arrays
```

</details>

**Pop()** and **Shift()** can both store values and return **methods:**

```jsx
//we are gonna change its 6,7 with 1,10, while also removing them
let deck= [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

deck.splice(deck.length/2, 0, deck.pop(), deck.shift());
deck == [2, 3, 4, 5, 6, 10, 1, 7, 8, 9] 

//we don't cut any elemennt with splice(),we use return .pop() and shift()
```

On arrays, we can **.sort()** and **.reverse()** the array elements order, and also use **.from()** to make **strings into arrays:**

<pre class="language-jsx"><code class="lang-jsx">//for strings the order is alphabetical
let ginn = ["ar","ac", "kr", "qu", "ab", "ri"]
ginn.sort()            //['ab', 'ac', 'ar', 'kr', 'qu', 'ri']
ginn.reverse()         //['ri', 'qu', 'kr', 'ar', 'ac', 'ab']

<strong>//numbers use typo code, an extra function needed
</strong><strong>let numeronia = [4,6,8,2,4,1]
</strong>numeronia.sort( (x,y)=> x - y)        //[ 1, 2, 4, 4, 6, 8 ]
numeronia.sort( (x,y)=> y - x)        //[ 8, 6, 4, 4, 2, 1 ]

//strings aren't arrays, so we use .from()
Array.from("welcomed")    //['w', 'e', 'l','c','o','m','e','d']
Array.from("welcomed".toUpperCase() ).map( (x)=> "this is the letter " + x )    
//['this the letter W',...]
</code></pre>

Any function _passed as an argument,_ inside a method, it's a **callback function**:

```jsx
//for example the anonymous functions inside .map()

const numbers = [1, 2, 3];
const numbersDoubled = numbers.map(function (number) {
  return number * 2;
});
```

**Booleans** are a type of data and we obtain it after **logical operators**:

```jsx
true && true == true  //logical AND returns true if BOTH
true || !true == true //logical OR, returns true if one of is true
2 !== 3    //is 2 NOT equal to 3? True
2 == "2"   // True, we check equality on value
2 === "2"  //False, it check strict equality on both value and typeof
"a" == "A" //False, equality checks caps
 
== being the equality operator
// && will be executed first
 
//and in functions
let studentCount = 12;
let mentorCount = 10;
var moreStudentsThanMentors = studentCount > mentorCount;
console.log("The awnser is", moreStudentsThanMentors);     //The awnser is true
 
//we can set boolean values without =="True"
if (htmlLevel > 5){
  cssAndHtmlAbove5 = true;
}
```

Booleans, Strings, and Numbers are the most known **primitives,** contrary to _objects_, which are aggregations of properties, **primitives are just values, they have no property:**

```jsx
//new String is a constructor, that's why its result is an object

typeof "abc";                 //"string"
typeof String("abc");         //"string"
typeof new String("abc");     //"object"
```

### Data conversion and type helpers

We can use the built-in helpers **Boolean()**, **String() Number()** to check/convert to respective data types:

```jsx
//Boolean will differentiate between truly/falsy values

Boolean("0")        //True, a 1-digit string is truly
Boolean(0)          //False, True for any other integer
Boolean("")         //False for any empty array
Boolean(" ")        //True, space digit is not empty arrays

//Number() will return an integer from a string when possible

Number("123.34")    //123.34, for Numbers in string use . for decimals
Number("")          //0, for empty arrays
Number(" 12.3  ")   //12.3 only the starters and ending spaces will be ignored
Number(null)        //0, is from null
Number(undefined)   //NaN

//String() will return the string result of its content 

String(1==1)        //"true" string will be returned
String(1!==1)       //"false" string will be returned
String(null)        //"null" string returned
String(undefined)   //"undefined" string returned

//String() on arrays will return a string of elements .join() by ,

String(["siamo", null, undefined, "audd"])    
//siamo,,,audd , the null and undefine will be empty
String({name: "uno"})                         
//[object, Object] will be returned
```

We can also have **data coercion** where values are converter automatically **:**

```jsx
//For example during the if statement

function errorMessage(input) {
  if (!input) {                //will automatically work as !Boolean(input)
    return 'Required field'
  }
}
```

### Js in HTML implementation

To link our **external .js file** to the HTML page we use:

```jsx
<body>
  <script src="./script.js">  
  </script>
</body>

//we can trigger functions on events
<a href="#" onClick="alert('text!');">Alert button</a> 

//or reference the function in the .js file
<input type="button" value="uno" onclick="add()"/> 

function add(){
  alert("welcomed")
}
```

We use **createElement(), createTextNode()** and **appendChild()** to potray _JS content into HTML_ :

```jsx
//we want to add additional <li> content on button click
<ul id="myList">
  <li>Coffee</li>
  <li>Tea</li>
</ul>

<button onclick="myFunction()">Append</button>

function myFunction() {
  oll = "terzo"
  const node = document.createElement("li");
  const textnode = document.createTextNode(oll);
  node.appendChild(textnode);
  document.getElementById("myList").appendChild(node);
}

//We create <li> HTML tag
//We insert the js variable as text with createTextNode(oll)
//We append the text to the <li> created element
//We use an id html element to append everything into
```

How to use **variables** and **arrays** in the DOM:

```jsx
<button onclick="myFunction()">Append</button>

var popp = "new element n."
var counter = 0

function myFunction() {
  counter++
  const node = document.createElement("li");
  const textnode = document.createTextNode(popp + counter);
  node.appendChild(textnode);
  document.getElementById("myList").appendChild(node);
}
```

### JS in DOM with arrays and event

Check **this exercise**:

{% embed url="https://codepen.io/misterlinux/pen/ZEaJdKg" %}
JS on DOM
{% endembed %}

We use Math.floor() to get **random 2-integers numbers**:

```jsx
//by multiplying the random (0 to 0.9) by 100 we get 2-digits numbers
var oul = Math.floor( Math.random()*100 ) ;
```

We use an external **array** to _store the random numbers_, while also resetting the **innerHTML**:

```jsx
//we use .length as an index to always update the array with the latest element
var filone = []

function rando() {
  filone[filone.length] = oul;
    
  var textnode = document.createTextNode(oul);
  document.getElementById("primo").appendChild(textnode);
}

var propt = document.getElementById("primo")
propt.innerHTML = ""
```

We also wanted the **numbers** and the **sum** to happen separately:

```jsx
//we sum the last random generated numbers, in case none we get NaN
function summin(){
  var erm = filone[filone.length-1] + filona[filona.length-1];

  const terzo = document.createTextNode(erm);
  document.getElementById("terzi").appendChild(terzo);
}
```

To show the arrays with the **stored values** we:

```jsx
var riga = document.createTextNode("[ " + filone + " ]");
document.getElementById("colla1").appendChild(riga);
```

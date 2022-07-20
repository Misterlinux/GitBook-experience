# JS 1

* [Math Operations, Booleans and Arrays methods](js-1.md#math-operations-booleans-and-arrays.)
* [Js in HTML implementation](js-1.md#js-in-html-implementation)
* [Js in Dom with array and events](js-1.md#undefined)

### Var, const, and functions.

&#x20;**Javascript** is an object-oriented and dynamic style.

**var, const and let** is how we store data in **keywords:**

{% tabs %}
{% tab title="var, let" %}
```
let uno = 'coming'
uno = 'The javascript is ' + uno

//we use = as an assign operator for the keyword uno and store the string "coming"
//we can then modify the var,let by repeatng the assign

console.log(uno)
/*result*/

"The javascript is coming"

//and we can declare multiple let
let primo= 1, secondo= 2, terzo= 3;

//ALSO, we can only use _ or - on let names
```
{% endtab %}

{% tab title="Const" %}
Data stored in **const** cannot be re-assigned with =;

```
const UNO = 20

//const UNO = UNO +3 will result in "constant value error"
//but you can still change it momentarely

console.log(UNO + 3)
//will return 23
```
{% endtab %}
{% endtabs %}

**Functions** are blocks of code used to  _perform tasks when invoked:_

{% tabs %}
{% tab title="function()" %}
you can use the **function** keyword to create a function:

```
function sum(){
    return console.log(12 + 15)
}

//after declaring the function with its keyword we Invoke it
sum()
// result 27
```

**return will stop execution** and return values, there are no _implicit_ returns in javascript
{% endtab %}

{% tab title="Arrow function" %}
Instead, we can use the **const** keyword and the arrow:

```
const tut = () =>{
  var wel = 12;
  var tre = 10;
  return console.log(wel+tre)
}

//the structure is always keyword/parameters/operations
tut();
//result 22
```
{% endtab %}
{% endtabs %}

**Parameters** are values that are passed to the function and can be used locally in the function:

```
//we can use multiple values for the function operations

function lot(num1, num2){
    var oltre = num1+num2;
    return console.log(oltre);
}

lot(5, 8);
lot(1, 4);
lot(4, 8);
//results 13, 5, 12

//parameters will be assigned based on their position, so num1=5 and num2=8
//parameters are LOCAL to the function, defined inside of it, including extra local variables-
```

**Arguments** are the actual **values** passed when _invoking_ the function:

```
//Invoking the lot(num1,num2) function 

lot(5, 8);    //assigning the num1, num2 Parameters the 5,8 Arguments

```

We can also implement **function composition**, using the return as an argument:

```
//the function returns another function

function translate2d(dx, dy) {

  return function (x, y){
    return [dx + x, dy + y]  
  }
}

const move2x = translate2d(2, 0);    //move2x is the returned function 
const result = move2x(4, 8);         //result is the returned operation result
console.log( result )                //[6, 8]

```

We can use the "parent" function to _store the_ **parameter**:

```
//we have a function as parameter and variables declared in the parent function

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

function addition(x,y){
  return [x,y]
}

const memo1 = memoize(adding );      //we pass the function, without the () so it doesnt return yet 
memo1(5, 5)                          //[5,5] and "already casted" for the first variables

//we execute the callback memoize function using the adding function as parameter
//memo1 has the returned function with the new arguments executing the parameter function
//we also store the LAST x, y and return in preX, preY, preR

memo1(1, 5)        //[1,5]
memo1(5, 5)        //[5,5]
memo1(5, 5)        //[5,5] "already casted"

//if the arguments are repeated we just re-use the saved result and add a string

```

We can also implement the _spread operator_ for function returns and have _multiple parameters function_:

```
//we first create the 2 parameter functions

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

//the spread operator will set the array of results as arguments
//we will execute FIRST the f(x,y) function THEN use the array results for the SECOND

let tent = composeTransform( uni, dui)
tent(2,1)                               //[10,6] 

let tent1 = composeTransform( dui, uni)
tent1(2,1)                             //[7,4]

//the order of the functions will apply different operations and results

//the passages for the spread operator are

  const first = f(x, y)
  const second= g(first[0], first[1])
  return second
  
//then we use the spread operator on the returned array

  const first = f(x, y)
  return g(...first)
  
//to end in 

  return g(...f(x, y))
  
```

We can also **Interpolate** variables into HTML elements, also using _Ternary Operators_:

```
//we can use `` or the +

let greetingStart = "Hallo "
let name = "dude"

const greeting = `${greetingStart}, my name is ${name}`;
let ecco = greetingStart + ", My name is " + name

console.log(greeting) / console.log(ecco)     //Hallo , My name is dude

//and for ternary logic we put the function between the ${}

const grade = 95;
`You have ${grade > 90 ? 'passed' : 'failed'} the exam.` ;

//You have passed the exam
```

**Console** is where we run scripts and commands.

### Math Operations, Booleans, and Arrays methods.

Most Math operations work the same in Javascript **+, -, \* and /** following the **Pendas** priority system.

```
//We can also define pow with:
3 ** 2 == 9 //will be true
Math.pow(3,2) == 9

// = is used for assigment while == for comparison
//and we can get the modulus (the rest after division)
10 % 3 == 1 ( 3*3 +1 )

//we can also use shorthand on operations
3 += 5 (result 8)
20 %= 3 (will be 2)
```

We can also perform **Math.()** operations, Math.() is a **Global Built-In** Object for Mathematics, for example **Math.round()**:

```
Math.round(12.3) == 12 
Math.floor(12.3) == 12 
Math.ceil(12.3) == 13 

//round() will round up the number based on the decimal
//ceil() and floor() round up and down without checking the decimal
//and even numbers like P greek
function degToRad(degrees) {
    return degrees * (Math.PI / 180);
};

function radToDeg(rad) {
  return rad / (Math.PI / 180);
};

//we can type long numbers using _
//1000000 = 1_000_000
```

And in the case of NaN (not a number) we also can have operations:

```
//we have the operator isNaN() in case, and NaN isnt === o !== to itself

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

**Arrays** are variables that can hold more than one value and one _typeof()_ data:

```
var list = [1, 2, 3]

//elements can be edited by their index(starting from 0)
list[1] = "new" //[1, "new", 3]

list.length == 2 
//strings are considered arrays of letters and so
"welcomed"[3] == "c" / "welcomed".length == 8

```

With **.length** we can check **Properties** of the array.

**Methods** on the other hand are **actions** on objects, and they need () because they are function:

```
"  its m i n us ".toUpperCase().trim()
//to uppercase and remove spaces at start,end
//"ITS M I N US"
//And on arrays we have:

let row = [12]
row.push(13) == [12,13] //.push() at the end of array
row.pop() == 13 //pop will cut and return only the last element but not the shortened array
row.shift() == 12 //will cut and return only the first element in the array
row.unshift(10, 11) == [10,11,12,13] //will ADD and return the .length of the arrau

//.splice allows us to precise cut or substitute array elements
let longi = [1,2,3,4,5]
longi.splice(3, 1, "indeed") == [1,2,3,"indeed",5]

//it goes .splice([index of where to start], number of elements cut, element added)
longi.splice(1, 2, "we change", "two") == [1,2,"we change","two",5]
//AND we can just cut content if we dont use the third paramether

//Others like .includes(), .startsWidth() will return if true/false on the array
//we can't use Regular expression Regex (/guess/i) here 
longi.includes(1) == true

let oltre = "minimal"
oltre[0].toUpperCase() + oltre.splice(1) == Minimal
//we can ALSO use the .splice(1) to get all the elements from index[1] included

//also we can use it with indexOf() and splice(, 1) to cut a specific element
carrello.comprati.indexOf( 'Star Ship' )        //["uno", "Star Ship", "tre"] [1]
carrello.comprati.splice( index, 1 )            //["uno", "tre"]

//also for last, arrays its a .typeof()
typeof longi == arrays
```

More **methods:**

```
//we can use .sort() to sort array elements alphabetically, and .reverse() for the opposite
let ginn = ["ar","ac", "kr", "qu", "ab", "ri"]
ginn.sort()            //['ab', 'ac', 'ar', 'kr', 'qu', 'ri']
ginn.reverse()         //['ri', 'qu', 'kr', 'ar', 'ac', 'ab']

//for numbers its a bit tricky, considering it takes the typo code, you will need a function
let numeronia = [4,6,8,2,4,1]
numeronia.sort( (x,y)=> x - y)        //[ 1, 2, 4, 4, 6, 8 ]
numeronia.sort( (x,y)=> y - x)        //[ 8, 6, 4, 4, 2, 1 ]

//strings aren't arrays but you return one with Array.from()
Array.from("welcomed")    //['w', 'e', 'l','c','o','m','e','d']
Array.from("welcomed".toUpperCase() ).map( (x)=> "this is the letter " + x )    //['this the letter W',...]

//we can't use Regular expression Regex (/guess/i) here 

```

Any function _passed as an argument,_ inside a method, it's a **callback function**:

```
//for example teh anonymous functions inside .map()

const numbers = [1, 2, 3];
const numbersDoubled = numbers.map(function (number) {
    return number * 2;
});

```

**Booleans** are a type of data and we obtain it after **logical operators**:

```
true && true == true //logical AND returns true if BOTH
true || !true == true //logical OR, returns true if one of is true
2 !== 3 //is 2 NOT equal to 3? True
2 == "2" // True, we check equality on value
2 === "2" //False, it check strict equality on both value and typeof
"a" == "A" //False, equality checks caps
 
 == being the equality operator
 // && will be executed first
 
//and in functions
 
let studentCount = 12;
let mentorCount = 10;
var moreStudentsThanMentors = studentCount > mentorCount;
console.log("The awnser is", moreStudentsThanMentors);     //The awnser is true
 
//we can set boolean values without ""
if (htmlLevel > 5){
    cssAndHtmlAbove5 = true;
}

```

### Data conversion and type helpers

We can use the built-in helpers **Boolean()**, **String() Number()** to check/convert to respective data types:

```
//Boolean will differentiate between truly/falsy values

Boolean("0")        //True, a 1 digit string is truly
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

String(["siamo", null, undefined, "audd"])    //siamo,,,audd , the null and undefine will be empty
String({name: "uno"})                         //[object, Object] will be returned

```

We can also have **data coercion** where values are converter automatically **:**

```
//For example during the if statement

function errorMessage(input) {
  if (!input) {                //will automatically work as !Boolean(input
    return 'Required field'
  }
}

```

### Js in HTML implementation

To link our external .js file to the html page we use:

```
<body>
    <script src="./script.js">  
    </script>
</body>

//this will allow us to call function on page interaction like

<a href="#" onClick="alert('text!');">Alert button</a> 
//here we call on the click event on a link for an alert directly on HTML

<input type="button" value="uno" onclick="add()"/> 
//so, here we reference the function add in the .js file onclickEvent

function add(){
  alert("welcomed")
}

```

To make the _content appear_ in the HTML :

```
//we want to add additional <li> content on button click

<ul id="myList">
    <li>Coffee</li>
    <li>Tea</li>
</ul>

<button onclick="myFunction()">Append</button>

//on the .js we have

function myFunction() {
  oll = "terzo"
  const node = document.createElement("li");
  const textnode = document.createTextNode(oll);
  node.appendChild(textnode);
  document.getElementById("myList").appendChild(node);
}

//so, we can create HTML tags elements with document.createElement()
//then we need to add text to the empty tag with document.createTextNode("a string can do)
//then we take the Tag and append to it the (content), Tag.appendChild(text)
//THEN to make it appear in the DOM html we need to get the ID of a tag and append the tag (now filled with content

```

How to use **var** and **arrays** in the Dom:

```
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

//we can use variables from the .js in the functions AND we can use counters and modify on the function
```

### JS in DOM with arrays and event

Check **this exercise**:

{% embed url="https://codepen.io/misterlinux/pen/ZEaJdKg" %}
JS on DOM
{% endembed %}

On the JS. part we have **Math.random()** and arrays on DOM:

```
//so, first to have the random numbers at 2 digits and floor them to the lower
//also, we get the ID 

  var oul = Math.floor( Math.random()*100 ) ;
  var pro = Math.floor( Math.random()*100 ) ;

```

As long as the page is not refreshed we can store **arrays** with the input:

```
//we keep the empty array outside the function and fill it by the click event
//using .length to place at the latest index when increasing
var filone = []

function rando() {
  filone[filone.length] = oul;
    
  var textnode = document.createTextNode(oul);
  document.getElementById("primo").appendChild(textnode);
}

//ALSO, to avoid the entire array being visible each click we "reset" the HTML content with

  var propt = document.getElementById("primo")
  propt.innerHTML = ""

```

We also wanted the **numbers** and the **sum** to happen separately:

```
function summin(){

  var erm = filone[filone.length-1] + filona[filona.length-1];

  const terzo = document.createTextNode(erm);
  document.getElementById("terzi").appendChild(terzo);
}

//with this we sum the last random generated numbers, in case none we have NaN

```

So, then to show the complete arrays we:

```
//can simply add it with the content:

  var riga = document.createTextNode("[ " + filone + " ]");
  document.getElementById("colla1").appendChild(riga);

```


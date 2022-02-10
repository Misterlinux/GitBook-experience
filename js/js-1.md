# JS 1

* [Math Operations, Booleans and Arrays](js-1.md#math-operations-and-arrays.)
* [Js in HTML implementation](js-1.md#undefined)

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

**return will stop execution** and return values.
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

function lol(num1, num2){
    var oltre = num1+num2;
    return console.log(oltre);
}

lol(5, 8);
lol(1, 4);
lol(4, 8);
//results 13, 5, 12

//parameters will be assigned based on their position, so num1=5 and num2=8
//parameters are LOCAL to the function, defined inside of it, including extra local variables-
```

### Math Operations, Booleans and Arrays.

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

**Methods** on the other hand are **actions** on objects:

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
longi.includes(1) == true

let oltre = "minimal"
oltre[0].toUpperCase() + oltre.splice(1) == Minimal
//we can ALSO use the .splice(1) to get all the elements from index[1] included

//also for last, arrays its a .typeof()
typeof longi == arrays
```

**Booleans** are a type of data and we obtain it after **logical operators**:

```
true && true == true //logical AND returns true if BOTH
true || !true == true //logical OR, returns true if one of is true
2 !== 3 //is 2 NOT equal to 3? true


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

To make the _content appear_ i the HTML :

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

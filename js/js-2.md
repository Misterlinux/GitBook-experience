# JS 2

* [Let, Var and differences](js-2.md#undefined)

### Methods on arrays and strings

Here are some more notes:

```
//we can put the array with index
[1, 1, "sting"][2] //sting
"string"[7]        //undefined

//But we can't use a regular expression[] to substitute
"string"[1] = "O"  //will remain "string"

//we can also use methods for position
"INDEED".charAt(5)        // D
"INDEED".charCodeAt(5)    // 68
//with charCodeAt we get the unique code of the digit pressed 
```

And we can use more same **methods** to modify strings:

```
//.slice(x, y) will include the x index but not the y
//and negative values will start at the end of the string/array
let str = "Apple, Banana, Kiwi";
str.slice(7 , str.length)    //"Banana, Kiwi"
str.slice(6, -2);            //" Banana, Ki"
str.slice(5)                 //", Banana, Kiwi"

//.slice() can work on arrays too, while .substring can't
//.subString(x, y) we can create a new string starting at x and being Y digits ong

str.substring(0, 5)          //"Apple"

//Having 2 string we can .concat() them instead of using the + 

let primo = "this is the one"
let secondo = "this is the two"

primo.concat(" AND", secondo)    //"this is the one ANDthis is the two
```

But this **doesn**'**t mean that array and string are the same type:**

```
typeof ["w","e","e","l"] //object
typeof "well"            //string

//and we can transform both in the other with
typeof "well".split("") == "w","e","e","l" //object
["w","e","e","l"].toString()               //"w,e,e,l"
["w","e","e","l"].join("")                 //"well"
//join will set the () between the spaces of array elements
//split will put the () between each array element

//or even you can
Array.isArray("indeed".split(" ") ) //true
Array.isArray("indeed" )            //false

//Typeof is an operator, not a method 
```

String data contains only 1 value and so is **primitive**, while Object contains more.

### If and Switch

We use the IF statement to execute a block of code if the **condition** is true:

```
//we have else for when the condition is false
//and use _else if_ in case we need more conditions 

if(true){
    return console.log("true")
}else if(){
}
else{
    return console.log("false")
}

```

For multiple **conditions** we can use **logical** operators:

```
if ((x === 5 || y > 3 || x <= 10) && (loggedIn || userName === 'Steve')) {
  // run the code
}

//remember to repeat the variable in case you use it multiple times
//for example in case of 3<x<10 separate the conditions 

if(x>3 && x<10){
  //code here
}

```

We can also use **switch** to use cases:

```
//switch uses cases for blocks and breaks to stop the code

switch (5) {
    case true:
	return console.log("number real")
	break;
    case false:
	return console.log("number not")
	break;
     default:
	return console.log("sdell")
}

//in case we need more cases for a single block
//we can use this method to get the month of the year
const month = new Date().getMonth();

//we can also use getDay() that starts in Sunday at 0 to 7
const day = new Date().getDay();

switch (month) {
	// January, February, March
	case 0:
	case 1:
	case 2:
		console.log("Winter");
		break;
	// April, May, June
	case 3:
	case 4:
	case 5:
		console.log("Spring");
		break;
	// July, August, September
	case 6:
	case 7:
	case 8:
		console.log("Summer");
		break;
	// October, November, December
	case 9:
	case 10:
	case 11:
		console.log("Autumn");
		break;
	default:
		console.log("Something went wrong.");
}
```

For both these statements the **falsy** values will be **false** in boolean:

```
//both strings, numbers and objects can be falsy 

- 0
- '': Empty string
- null
- undefined
- NaN

//and in case you are curious about the typeof , null being an object is a bug but still
typeof null + " " + typeof undefined    //object, undefined
```

**Ternary operator** is a simplified conditional operator:

```
//we don't need to have a constant linked to it
//usable for if/else

let greeting = ( condition) ? 
	'string returned if condition' 
	: 
	'string returned if false';

```

### Let, Var and differences

In 2015 the **let** keyword for variables was introduced:

```
//the main difference is on the scope of the variable

var z = 'hello';
var z = 'world';     //will allow to re-declare the variable
let j = 'hello';
let j = 'world';     //will give Syntax error on re-declaring teh same let to modify it

//being it that var will have a global scope while let will be limted to its block of code

let greeter = "hey hi";  
let times = 5;  
if (times > 3) {  
   var hello = "Say Hello JavaTpoint";   
   console.log(hello) 
}  
console.log(hello)    //should be hello not declared Error with let

//being var global scope it can be called out of his block, and this can cause Erros 
//that's why const is used most of the times, to avoid accidental change of values

```

and on functions it would be:

```
//here for example when calling the cb to see how the var=i changes in the for loop
//BUT being var global scoping it will change to 5 before the .push 

var callbacks = [];
(function() {
  var i;
  for (i = 0; i < 5; i++) {
    console.log("when does " + i + " change")
    callbacks.push( 
      function() { return i; } 
      );
  }
  console.log("is this " + i)
})();

console.log(callbacks.map( 
  function(cb) { return cb(); }  //resulting in [5, 5, 5, 5, 5]
  )
);

//if we had let the for loop would have increased and pushed the [1,2,3,4,5] instead

```

### Regex and string matching

We use **Regular Expression Search Methods** to search **patterns** in strings:

```
let pattern = /Lorem/i    
//we have the pattern in /slashes/ and letters for different modifyers, i(for case-insensitive) and g(to match with the whole content)

vipsum.search(pattern)    //will return the index of the first match 
vipsum.search(/loreM/i)   //we can also put directly
/lOrem/i.test("Lorem Ipsum")     //in other cases 
```

To search **more specific patterns** with regex we can use:

```
//we can search a range of specific letters 
vipsum.match(/[qvgk]/g)    //will return an array with all the matching single letters in []

//we can match the first of 2 values (if we use g then it's just as [])
vipsum.match( /[q|x]/ )    //will return the first of q|x matching

//we can also check for whitespaces/new lines 
vipsum.match( /\s/g )    //with \s we check both "" and \n in an array (with g)
vipsum.match( /\n/g )    //matches only the new lines

//we can also match strings that have at least 1
vipsum.match( /s+/g )    //will return an array of "s" or "ss+"

```

For the _methods,_ we have __ **.test()** and **.exec():**

```
//We .test the Pattern to the String returning true/false if present
let html = document.getElementById("lorem").innerText;
const primo= /lorem/i;
console.log( primo.test(html) );    //true

//.Exec() will return an array with index and input including the text
let yull = /ipsum/gi.exec(html)
console.log(yull)                  //ipsum or null in case not matching

//they have _____.text/exec(html) pattern first
```

Then we have the **.search()** and **.match() :**

```
//.search() will return the index of the match if present
let visit = "Plant a tree!";
let n = visit.search(/plants/i);
console.log(n)                    //if no match then -1 return

//.match will return an array like exec.() but can contain multiple matches
console.log( "best is best".match(/best/g) )    //[best, best]

//here we have html.search/match(___) when pattern is in ()
```

### HTML forms and JS validation

We use **forms** to collect user input:

```
//We use <form> as a container for the inputs and buttons, we will use its ID to also access the tags inside
//we also add to it a HTTP method "POST" or "GET" to add or retrieve content from the server
//the action Attribute can allow us to redirect the form after submission

<form id="formula" method="post" action="https://www.ilpost.it/">
	<label for="unon">First</label>
	<input type="text" id="uno" name="unon"> <br>

	<input type="radio" id="html" name="fav_language" value="HTML">
	<label for="html">HTML</label><br>
	<input type="radio" id="css" name="fav_language" value="CSS">
	<label for="css">CSS</label><br>

	<input type="checkbox" id="vehicle1" name="vehicle1" value="Bike">
	<label for="vehicle1"> I have a bike</label><br>
	<input type="checkbox" id="vehicle2" name="vehicle2" value="Car">
	<label for="vehicle2"> I have a car</label><br>

	<input type="submit" value="submit">
</form>

//type in <input> allow us to use text/checkboxes/radio and submit buttons in the form
//checkboxes allow us for multiple selections while radio button only one
//type="submit" appears as a button with content value

```

More about **label** for the inputs:

```
//label is useful to link together inputs by "for" = "id" = "name"
//we will need a name="" for the submit to happen
//while checkbox will be checked even if click on label text

<input type="radio" id="javascript" name="fav_language" value="JavaScript">
<label for="javascript">JavaScript</label>

```

To access the **form** we can use:

```
//we can use the ID to access name/value of inputs for example

<form id="krip">
    <input type="text" name="fname" value="Donald" id="uno">
    <input type="text" name="lname" value="Duck" id="due">
    <input type="submit" value="Submit">
</form> 
    
<p id="demoman">
    //Donald uno / Duck due / submit
</p>

//the <input> are treated as elements of an array so to access .value/.id in the tags

let testo = "";
const init = document.getElementById("krip")

for (let i = 0; i < isit.length; i++) {
  testo +=  init[i].value + " " + init[i].id  + "<br>" ;
}
document.getElementById("demoman").innerHTML = testo;

```

About **AppendChild()** and **innerText** difference in DOM:

```
//We can also use <button> instead of<input value="ADD" type="submit">

<input type="text" name="name" id="txt_name" > 
<button onclick="kek()">
    ADD
</button>

<p id="demo"></p>
<p id="demani"></p>

//in the first DOM html #demo, we createTextNode with the value in <input> 
//to then append in #demo, THIS will add a new Text for each click on the Append

function kek(){
  let inside = document.getElementById("txt_name").value
  
  let textnode = document.createTextNode(inside+ " ");
  document.getElementById("demo").appendChild(textnode);

  let text;
  if (isNaN(inside) || inside < 1 || inside > 10) {
    text = "Input not valid";
  } else {
    text = inside + " I guess";
  }
  document.getElementById("demani").innerHTML = text;
}

//in the innerHTML we can put a text in the #demani BUT only once no matter how many clicks

```

We can use **select** in the DOM for .value too:

```
//we can use label + select HTML

<label for="weather">Select the weather type today: </label>
<select id="weather">
    <option value="">--Make a choice--</option>
    <option value="black">Sunny</option>
    <option value="white">Ameno</option>
</select>

<p id="well"></p>

//Here we didn't just added a textContent to the #well

const select = document.querySelector('#weather');
const para = document.querySelector('#well');

//we can also put the function separate from eventlistener
select.addEventListener('change', setWeather);

function setWeather() {
  const choice = select.value;

  function update(bgColor, textColor) {
    para.style.backgroundColor = bgColor;
    para.style.color = textColor;
  }

  switch (choice) {
    case 'black':
      para.textContent = 'Text black on white space';
      update('white','black')
      break;
    case 'white':
      para.textContent = 'white text on black space';
      update('black','white')
      break;
    default:
      para.textContent = '';
  }
}

//Here we didn't just add a textContent to the #well but used a function update() to style its new CSS
//AND also the '' in case of default the space of the tag becomes null
```

For the **submit** button event to be listened we can various **options**:

```
<input type="text" id="scritto">
<button id="btn">javascript click</button>              //or we can also use

<input type="submit" value="javascript tick" id="btn">

//In javascript we can have a button with onClick event OR
const btn = document.querySelector('#btn');
btn.onclick = () => {
   let java = document.getElementById("scritto").value
   console.log(java)
};

//have a form that responds on submit, with the ID on form
<form action="" id="btn">
    <input type="text" id="scritto">
    <input type="submit" value="javascript tick" >
</form>

const btn = document.querySelector('#btn');
btn.addEventListener("submit", (event) =>{
  event.preventDefault();
  let java = document.getElementById("scritto").value
  console.log(java)
})

//here we have the form ID on .addEventListener("", () =>{})
//we need an (event) and .preventDefault() to avoid the submit after the event
//we can also use a "Click" EventListener for a button

<button id="btnado">Get Selected Colors</button>
bottone.addEventListener('click', (event) => {});   

//or have the Onlick() on the html to reference the javascript and then use the input etc...
<button onclick="clock()">
</button>

function clock(){
}
```

To select on **checkbox** and **radio** button form:

```
<form id="formula" method="post">
    <input type="radio" id="html" name="fav_language" value="HTML">
    <label for="html">HTML</label><br>
    
    <input type="checkbox" id="vehicle1" name="vehicle1" value="Bike">
    <label for="vehicle1"> I have a bike</label><br>

    <input type="submit" value="submit">
</form>

//we can get the .checked() or just .value() from the form

 const formato = document.getElementById('formula');

formato.addEventListener("submit", (event) => {
  event.preventDefault();
  let gippin = document.querySelector("#javascript")
  console.log( gippin.checked )                 //Both radio,checkbox can be true/false if checked
  let dess = document.querySelector("#vehicle2")
  console.log( dess.value)                      //while value will display if checked or ot
}) 

```

And to handle the **checkbox/radio** we can:

```
<form action="" id="parap">
	<label for="c1"> 
	    <input type="checkbox" name="color" value="red" id="c1">Red
	</label>
	<label for="c2">
	    <input type="checkbox" name="color" value="green" id="c2"> Green
	</label>
	<label for="c3">
	    <input type="checkbox" name="color" value="blue" id="c3">Blue
	</label>

	<input type="submit" value="selected" id="btnado">
</form> 

//with querySelectorAll we select all input with names that ends up pseudoclass checked
//we can have an array with the checkboxes AND THEN get the forEach() to get an array pushed elements

const btnadoo = document.querySelector('#parap');
btnadoo.addEventListener('submit', (event) => {
    event.preventDefault()
    let checkboxes = document.querySelectorAll('input[name="color"]:checked');
    let values = [];

    checkboxes.forEach((checkbox) => {
        values.push(checkbox.value);
    });
});   

```

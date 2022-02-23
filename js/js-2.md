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

so, lets check some content

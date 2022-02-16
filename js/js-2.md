# JS 2

* First element on

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

```

For both these statements the **falsy** values will be **false** in boolean:

```
//both strings, numbers and objects can be falsy 

- 0
- '': Empty string
- null
- undefined
- NaN

```


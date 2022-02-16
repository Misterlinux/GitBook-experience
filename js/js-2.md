# JS 2

* First element on

### Methods on arrays and strings

Here are some more notes:

```
//we can put the array with index
[1, 1, "sting"][2] //sting
"string"[7]        //undefined

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



```

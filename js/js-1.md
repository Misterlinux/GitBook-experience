# JS 1

* [Math Operations and Arrays](js-1.md#math-operations-and-arrays.)

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

**Parameters** are arguments that are passed to the function and can be used locally in the function:

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

### Math Operations and Arrays.

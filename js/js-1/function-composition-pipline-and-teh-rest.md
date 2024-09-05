# Function composition, pipline and teh rest

* list
* list
* lsit

We create a **higher-order** function that **returns a new function** while taking _two series_ of arguments, during its **first** and **second invocations.**

```jsx
//The arguments values are cached in the returned function
function translate2d(dx, dy) {

  return function (x, y){
    return [dx + x, dy + y]  
  }
}

const move2x = translate2d(2, 0);    //move2x() is the returned function 
const result = move2x(4, 8);         //result is the returned operation result
console.log( result )                //[6, 8]
```

This is called **function composition**, a process that combines functions to break down complex operations into smaller, more manageable pieces.&#x20;

```jsx
//Local variables can be set and used down the line
//Passed functions can be used with later invoked arguments
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

The returning functions can be invoked with new arguments, allowing for higher-order functions to be more modular across different invocations.

```jsx
//We set the argument function being used in the composition
//and its argument on the second invokation
function addition(x,y){
  return [x,y]
}

const memo1 = memoize( adding );      
memo1(1, 5)  //f(x) == [5, 5]
memo1(5, 5)  //[5,5]
memo1(5, 5)  //[5,5] "already casted"
```

<details>

<summary>Spread operator arguments and function composition order</summary>

We can use the **spread operator** on the array returned by a function, which allows us to use the **destructured** array elements as **arguments** of the composed function.

Both functions accept the same type of arguments and can perform different operations when their order is changed.

```jsx
//The g(x, y) first needs to deconstruct the returned [x, y]
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

let tent = composeTransform( uni, dui)    //[[2+3]*2 , [1+1]*3] 
tent(2,1)                                 //[10,6] 

let tent1 = composeTransform( dui, uni )  //[[2*2]+3, [1*3]+1 ] 
tent1(2, 1)                               //[7, 4]
```

</details>

A function composition determines its **evaluation order**, based on its function (**nesting**), with the innermost function being applied first.&#x20;

The **argument** passed to the composed function is used by its _first function_, and then each successive function receives the previous output as its argument, enabling **pipeline data processing.**

<pre class="language-jsx"><code class="lang-jsx">//sumNumbers for last because it doesn't return an array like the others
let filterOddNumbers = (arr) => arr.filter( x => x % 2 !== 0 )
let squareNumbers = (arr) => arr.map( x => x * x )
let addOneToEach = (arr) => arr.map( x => x + 1 )
let sumNumbers = (arr) => arr.reduce((acc, x) => acc + x, 0)

let duo = (f, g) => (array) => g( f(array))
let trio = (f, g, h) => (array1) => h( g( f( array1 )))

const pipeline = duo( 
  trio( filterOddNumbers, squareNumbers, addOneToEach ), 
<strong>  sumNumbers
</strong>)
//No even numbers, square each, + 1 each ==> sum
let data = [1, 2, 3, 4, 5, 6, 7, 8, 9]

const result = pipeline(data);  //[1, 3, 5, 7, 9], Math.pow(2), x+1, arr.reduce()
console.log(result);    // Output: 170
</code></pre>

### Functions execution order on reduce() function compositions.

The **reduce()** method accumulates values from an array, iterating between its elements.

```jsx
//initial value (v) + the rest (f) from left/right
console.log( [1, 2, 3].reduce((v, f) => v + f ) ) //6
```

We can combine multiple functions into a single [variadic ](https://www.geeksforgeeks.org/variadic-functions-in-javascript/)**function composition**, by _**destructuring**_ their arguments as an _**array**_.                                                                                                                                              The provided **argument** serves as the initial input, and each function's output becomes the input for the next one in the array.

```jsx
//x is extracted from each single function, x == v 
let compose1 = (...fns) => x => fns.reduceRight((v, f) => f(v), x);

//Each x is the returned f(x) of the previous array function ((x*2)+1)*x)
let double = x => x * 2;
let addOne = x => x + 1;
let square = x => x * x;

//We provide the x argument on the function composition invocation.
let doubleThenAddOneThenSquare = compose1(square, addOne, double);
console.log(doubleThenAddOneThenSquare(5)); // Output: 121 //52 if reverse order 
```

In this function **composition**, each _destructed_ function returns a new function rather than a value, returning a **new function composition**.                                                                                                                    The **order** of execution in the returned function composition will be inverted, from right-left (as in reduceRight) to top-bottom.                                                                                                                                     Each function has access to the function **input** from the **previous** function and the **arguments** passed when the composition is invoked.

{% tabs %}
{% tab title="Reference deconstruct" %}
We compose **reference** functions, that will trigger once they start being reduced.

```jsx
//Deconstructs functions from right to left
let compose = (...fns) => x => fns.reduceRight((v, f) => f(v), x);

//single deconstructed function made by 2 functions
let secondo = (fn) => (...args) => {
  console.log("secondo?")
  return fn(...args) + 1
}

let primo = (fn) => (...args) => {
  console.log("primo?")
  return fn(...args) + 1   
}

let final = (massa) => massa + 5

//Composed reference function being returned primo(secondo((final)))
let ricomposed = compose(
  primo,
  secondo
)(final)

let resulted = ricomposed(5)
console.log( resulted )  //primo?, secondo?, 7
```
{% endtab %}

{% tab title="invoked deconstruct" %}
We can compose using **invoked** functions, that will trigger before the reduce starts.

```jsx
let composejj = (...fns) => x => fns.reduceRight((v, f) => f(v), x);

//They require additional (arguments) and functions returns
let primo = (con) => {
  con()
  return (fn) => (...args) => {
    console.log("primo?")
    return fn(...args)
  } 
}

let secondo = () => (fn) => (...args) => {
  console.log("secondo?")
  return fn(...args) + 1
}

let final = (massa) => 

function squal(massa){
  return massa + 5
}

//The function passed won't execute, it will be passed
let ritornello = composejj(
  primo(()=> console.log("Beyond the deconstruct")),
  secondo()
)(squal)

let charla = ritornello(5)
console.log( charla )   //"Beyond the deconstruct", "primo?", "secondo?", 7
```
{% endtab %}
{% endtabs %}

<details>

<summary>Returned function composition execution order</summary>

The reduceRight() method **composes** functions from right to left, on an \[primo, secondo] array, but the function composition returns the primo() function first.

The secondo function is triggered first, but it returns a function (...args), which is then called on the second function composition call.

```jsx
//We add a console.log() to the first invocation on reduceRight()
function primo1(){
  return function (fn){
    console.log( "Actual Primo" )

    return function (...args){
      console.log( "Primo?" )
      return fn(...args) + 1
    }
  }
}

function secondo2(){
  return function (fn){
    console.log( "Actual Secondo" )

    return function (...args){
      console.log( "Secondo?" )
      return fn(...args) + 1
    }
  }
}

let final = (massa) => massa + 5

let compose = (...fns) => x => fns.reduceRight((v, f) => f(v), x);

let ricomposed = compose(
  primo1(),
  secondo2()
)(final)
//The reduceRight() is being respected
//"Actual Primo", "Actual Secondo"
//"Secondo?", "Primo?", 12
```

</details>

The function **composition** being returned from the reduceRight() is.

```jsx
//primo(secondo(final))
function ricomposed() {
  return function() {
    console.log("Primo?")
    return function(massa) {
      console.log("Secondo?");
      return final(massa) + 1 + 1;
    };
  };
}

console.log( ricomposed()()(5) )  //"Primo?", "secondo?", 7
```

### Function composition on Error Handling

1

1

1

1

1

1

1

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

const result = pipeline(data);
console.log(result);    // Output: 170
</code></pre>

1

1

1

1

1

1

1

1

1

1

1

1

# Function composition, pipline and teh rest

* list
* list
* lsit

1

1

1

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

Nested functions

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

maybe needed or not&#x20;

We can also implement the _**spread operator**_ for function returns and have _multiple parameter functions._

multi parameter

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

```jsx
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

1

1

1

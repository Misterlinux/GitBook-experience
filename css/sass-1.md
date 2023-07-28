# Sass 1

**Sass** (**S**yntactically **A**wesome **S**tyle**S**heet) is a _CSS_-compatible **pre-processor extension**, developed in 2006.

In React, we **npm install sass** to **transpile** a sass file _into CSS_, by default, the browser can't read sass.

A sass file (extension **.scss**) can **nest properties** part of a **DOM parent selector**.

```scss
//Sass variables can store strings/numbers/booleans/colors/lists
//Global and local variables are declared outside/inside selectors
//Any variables will affect only the selectors below it
$primo: red;

.coral{ color: $primo; }

//use !global to reset the global variable
.coral1{ $primo: green;  color: $primo ; }

.parent{
  color: white;
  background-color: black;
  padding: 3px;

  p{
    margin: 0;
    background-color: yellowgreen;
  }
  h6{
    background-color: $primo;
  }
}

//In the app.js file we import the sass file
import './App.scss';

<div>
  <p className='coral'> Global variable </p>

  <p className='coral1'> Local variable </p>

  <div className='parent'>
    <p>Nested element </p>
    <h6>Styled with nested sass </h6>
  </div>
<div>

```



1

1

1

1

1

1

1

1

1

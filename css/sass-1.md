# Sass 1

**Sass** (**S**yntactically **A**wesome **S**tyle**S**heet) is a _CSS_-compatible **pre-processor extension**, developed in 2006.

In React, we **npm install sass** to **transpile** a sass file _into CSS_, due to the browser being unable to read sass.

```scss
//Sass variables can store strings/numbers/booleans/colors/lists
//Global and local variables are declared outside/inside selectors
//Any variables will affect only the selectors below it
$primo: red;

.coral{ 
  color: $primo; 
}

.coral1{ 
  $primo: green;      //use !global to reset the global variable
  color: $primo ; 
}
```

A sass file (extension **.scss**) can **nest properties** part of a **DOM parent selector**.

```
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
  <div className='parent'>
    <p>Nested element </p>
    <h6>Styled with nested sass </h6>
  </div>
<div>
```

<figure><img src="../.gitbook/assets/sassStyling.png" alt=""><figcaption><p>sass style DOM element</p></figcaption></figure>

Sass can **nest properties names** if they start with the same word.

```
//for font-family, font-size and font-weight
.nested{

  font: {
    family: Helvetica, sans-serif;
    size: 18px;
    weight: bold;
  }
}
```

We can **@import** external **sass** files, usually to **separate** the **sass variables** file from the **sass selectors** file.

```
//We add a _partial to the file's name to keep it from transpiring
//@import on the top of the sass file for it to have global scope

_Global.scss
$bicolor: yellow;

App.scss
@import "Global";

.exported{
    background-color: $bicolor;
}
```

1

1

1

1

1

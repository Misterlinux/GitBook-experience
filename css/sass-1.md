# Sass 1

* 1
* 1
* 1
* 1
* 1

**Sass** (**S**yntactically **A**wesome **S**tyle**S**heet) is a _CSS_-compatible **pre-processor extension**, developed in 2006.

In React, we **npm install sass** to **transpile** a sass file _into CSS_, due to the browser being unable to read sass.

{% tabs %}
{% tab title="sass variables" %}
**Global** and **local variables** are declared outside/inside selectors.

```
//Sass variables can store strings/numbers/booleans/colors/lists
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
{% endtab %}

{% tab title="sass nested variables" %}
A sass file (extension **.scss**) can **nest properties** part of a **DOM parent selector**.

```sass
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
{% endtab %}
{% endtabs %}

<figure><img src="../.gitbook/assets/sassStyling.png" alt=""><figcaption><p>sass style DOM element</p></figcaption></figure>

Sass can **nest properties names** if they start with the same word.

```
//for font-family, font-size and font-weight
//remember the space before {}
.nested{

  font: {
    family: Helvetica, sans-serif;
    size: 18px;
    weight: bold;
  }
}
```

We can **@import** external **sass** files, usually to **separate** the **sass variables** file from the **sass selectors** file.

```sass
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

The **@mixin** and **@include** directives create blocks of sass properties to use on selectors.

{% tabs %}
{% tab title="Basic @mixin" %}
Both **-** and **\_** are considered the **same digit** on mixin names.

```sass
@mixin bordi-y{
    border-bottom: 2px solid blue;
    border-top: 2px solid red;
}

@mixin testo{
  font: {
    size: 25px;
    color: purple; 
  }
}

.paper{
    @include bordi-y;
    @include testo;
}
```

We can also use it to **shorten vendor prefixes**.

```
@mixin transform($property) {
  -webkit-transform: $property;
  -ms-transform: $property;
  transform: $property;
}

.myBox {
  @include transform(rotate(20deg));
}
```
{% endtab %}

{% tab title="@mixin() params and include() arguments" %}
Similar to functions, **@mixin parameters** receive their own values from the **@include() arguments.**

```sass
@mixin bordo($width, $color, $color1: orange ){
  border-bottom: $width solid $color;
  border-top: $width solid $color1;
}

.mix{
  @include bordo( 3px, lightblue)
}

.mix1{
  @include bordo( 6px, orange, $color1: lightblue )
}
```
{% endtab %}
{% endtabs %}

<figure><img src="../.gitbook/assets/MixinInclude.png" alt=""><figcaption><p>@mixin and @include sass</p></figcaption></figure>

The **@extend** directive passes a set of properties between selectors.

```
.primo{
    border-top: 3px solid green;
    border-bottom: 3px solid red;
}

//gets the .primo orders plus text
.secondo{
    @extend .primo;
    color: lightseagreen;
    font-size: 15px;
}
```

1

1

1

1

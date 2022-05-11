# PureCSS

* uno
* due
* tre

**PureCSS** is a set of responsive, small (3.7kb) and mobile-focused CSS modules.

To add it we use the **unpkg** **CDN** :

```
<head>
//these 2 first are for the PureCSS and responsive grid modules
    <link rel="stylesheet" href="https://unpkg.com/purecss@2.1.0/build/pure-min.css" integrity="sha384-yHIFVG6ClnONEA5yB5DJXfW2/KC173DIQrYoZMEtBvGzmf0PKiGyNEqe9N6BNDBH" crossorigin="anonymous">
    <link rel="stylesheet" href="https://unpkg.com/purecss@2.1.0/build/grids-responsive-min.css">
    
//we also need to include minimize.css and viewpoint for the zoom 
    <link rel="stylesheet" href="https://unpkg.com/purecss@1.0.1/build/base-min.css">
    <meta name="viewport" content="width=device-width, initial-scale=1">
</head>

```

We can integrate it into our project build with **npm:**

```
//in the terminal we
$ npm install purecss --save 

//to load the project
require('purecss')

```

_**The responsive grid:**_

```
//so, first we need a parent tag, pure-g(rid), which is gonna contain units, pure-u(nits)
<div class="pure-g">
    <div class="pure-u-1-2">
        this will be 1/2 of the screen and a 2-2 wont be the same as 1-1
    </div>
    <div class="pure-u-1-2">
         the other 1/2, and if exceeding it will create a new line
    </div>
</div>

//for responsive we add sm/md/lg/xl/xxl to the class, as breakpoint for the unit to work
<div class="pure-g">
    <div class="pure-u-1-2 pure-u-sm-1-3">
        from 568px it will be 1/3 of the screen
    </div>
    <div class="pure-u-1-2 pure-u-sm-2-3">
         will this will be 2/3 of the screen
    </div>
</div>

```

![Some grid](../.gitbook/assets/Cattura.PNG)

This works on **images** too:

```
//first we need pure-img on the <img> under a pure-g parent tag, so to have a responsive image to the screen
<div class="pure-g">
    <img class="pure-img pure-u-1-3 pure-u-sm-1-5" src="image.jpg" alt="">
    <img class="pure-img pure-u-1-3 pure-u-sm-1-5" src="image.jpg" alt="">
    <img class="pure-img pure-u-1-3 pure-u-sm-2-5" src="image.jpg" alt="">
</div>
//on bigger screens than (568px) we have 1/5 images and 2/3 image, they dont have to sum to 5/5
//on smaller screens they will be 1/3 of the page

```

Also, we can add the **Hidden attribute**:

```
//It won't show the h1 element, as display:none does
<h1 hidden>NOBODY WILL NOTICE THIS </h1>

```

### Menu and forms&#x20;

Check this **form**, with an **input** and **button:**

```
//we can use pure-u on each tag to custom form elements, doing it on button will work but will need css for the fonr-size
<div class="pure-g">
    <div class="pure-u-1-2"
        <form class="pure-form pure-u-2-3">
        //we use the pure-form for form tags, like input/button AND on input we can grid
            <input type="text" class="pure-input-2-3 pure-input-rounded" placeholder="search" >
            <button class="pure-button">try </button>
        </form>
    </div>
</div>

//we have 1/2 screen, a 2/3 of it for the form AND 2/3 of it for the input

```

![](../.gitbook/assets/Cattura11.PNG)

For **forms**, we start with a simpler one with no labels:

```
//we just add fieldset to group some inputs and use placeholder as labels
<form class="pure-form">
    <fieldset>
        <input class="pure-input-2-3 placeholder="username">
        <input class="pure-input-2-3 placeholder="password" readonly="">
    </fieldset>
    <fieldset>
        <textarea placeholder="text here.." rows="3" id="" class="pure-input-2-3"></textarea>
    </fieldset>
</form>

//we treat textarea as input, we use rows="3" for how many lines of space

```

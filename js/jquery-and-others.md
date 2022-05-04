# Jquery & others

* Jquery in Javascript

**Javascript** is a client-side programming language, initially for the browser, now designed for interactivity.

**Jquery** is a _library_ (a collection of code stored in a file, as pre-written code), to implement it we can:

```
//we can use the downloaded .js file library as SRC and then just add the script.js file

<head>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
    <script src="./script.js"></script>
</head>

```

Used back when Browsers had differences in using javascript, nowadays web-animations API and css can handle most of Jquery effects.

For the **D**ocument **O**bject **M**odel , the browser interpretation of the webpage.

```
//First we need to load the page
$(document).ready(function(){} )

//or
$(fucntion(){

})

//the syntax we can have 
$(selector).action()

//$ to access the jquery
//we QUERY(selct) with the () using CSS selectors
//then .action 

//for example we can select DOM elements variables
let sel = $(“h2”) 
let sel = document.querySelector("h2")

```

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
//First we need to load the page for most
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

Check this exercise:

{% embed url="https://codepen.io/misterlinux/pen/BaYoBgd" %}
Jquery and timer
{% endembed %}

When working with _audio files_ we can:

```
//first we can do like images, download or get src (which is really hard)
let sounded = [
  "https://samplelib.com/lib/preview/mp3/sample-3s.mp3 "
]

//for errors like this, we just need to put the audio inside Click
Uncaught (in promise) DOMException: play() failed because the user didn't interact with the document first.

```

And for the **.values** from the input:

```
var valuei = $("#usero").val(); 

//we first define value1 outside the setInterval, and random will go from 0 to 0.99, thats why .floor works

skip = setInterval(function(){
  if( valuei > 0){
    valuei -= 1
    let dentro = Math.floor( Math.random() * 5 )
    pass(valuei, dentro)
  }else{
    clearTimeout(skip) 
    skip = null; 
    audio.play();
  }
}, 500)

//at the end of the timer we get the audio

```

To have the button to stop/restart we added a counter to the existing code:

```
//on click we add clearInterval() or re-start the timer with the current secs
$(function(){
  let cic = 0;
  $("#sto").click(function(){
  cic+= 1;
  if(cic%2 == 1){

  }else{

  }
  });
})

```

For the **audio and image** on Interval, also used .replaceWith(''):

```
//we transfer timer and counter, each sound has an index from the random

function pass(ecco, sound){
  let audio = new Audio(sounded[sound])

  switch(sound){
    case 0:
      suono(audio)
      break;
    case 1:
      suono(audio)
      break; 
    case 2:
      suono(audio)
      break; 
    case 3:
      suono(audio)
      break; 
    case 4:
      suono(audio)
      break; 
  }
//we have a secondary counter for a sequential images
  film += 1
  if(film >= 5){
    film = 0 
  }

  $("#central").attr("src", pacco[film] )

  let mins = Math.floor(ecco/60)
  let sec = ecco%60 

  let temp = (sec<10) ? $("#time").text( "0"+ mins + ":0" + sec ) : $("#time").text( "0"+ mins + ":" + sec );
  $(".cambia").replaceWith("<div> replaced after start </div>");

  $("#sto").click(function(){
    audio.pause();
  })
}
//down we used a ternary operator with a variable that is not used and .replaceWith can change the HTML tags

```

to actually _**play**_** the audio** in Jquery we use:

```
//we use the .trigger("action")

function suono(aud){
    //audio.currentTime = 0;
    $(aud ).trigger('play');

    setTimeout(function(){
      aud.pause();
      //audio.currentTime = 0;
    }, 500);
    
}
//we need to put .pause() in a setTimeout() coz it can stop the audio immediatly without letting it start
//AND we can use .currentTime to set the audio time (at 0 the audio re-start)

```

More **Jquery**:

```
//On selectors and how to use THIS, .hide will remove the tag and text will add text to a tag

$(document).ready(function(){
  $("#capi").click(function(){
    $(this).hide()
    $(".capi").text("THIS (selected element) will be .hide()")
  })
})

```

We can also change the **att**ribute of HTML elements and _double click_:

```
//after we create an extra .css we add an extra HTML 

<button id="otranto" class="red">

$(document).ready(function(){
  $("#otranto").click(function(){
      $(this).attr("class", "red")
  })
})

```

And the hover:

```
//hover can have 2 functions, the first on hover while the second will change the previous text

$(function(){
  $("#hov").hover(function(){
      $(this).attr("class", "red")
      $(this).text("also by having 2 functions for the fired action")
  },
  function(){
      $(this).attr("class", "rid")
      $(this).text("mouse got off ")
  })
})

```

Also you can **chain** multiple actions with the mouse:

```
//we put the .on({}) and list actions, with 

$(function(){
  $("#onni").on({
      mouseenter: function(){
          $(this).css("background-color", "green")
      },
      mouseleave: function(){
          $(this).css("background-color", "yellow")
      },
      click: function(){
          $(this).css("background-color", "red")
      }

  })
})

//and we also have an event for click off
$(function(){
  $("#holdup").mouseup(function(){
      $(this).text("you didn't hold the mouse")
  })
})

```

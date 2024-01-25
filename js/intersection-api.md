# Intersection API

* This is a list
* siamo quasi&#x20;
* eccolo

### Intersection Observer API

The Intersection Observer is a **web platform API**, included in JS and without running in the main thread.

it _asynchronously <mark style="background-color:blue;">observe</mark> intersections_ between the **target** elements and the **user viewpoint root**:

```jsx
//viewpoint set in options and target set in observe()
//IntersectionObs config can't be changed once set but can observe multiple

let base = useRef()
let target = useRef()
  
useEffect(()=>{

  let options={
    root: base.current, //Document/Element bounding-box used as viewpoint
    rootmargin: "0px",  //offset margins applied to viewpoint intersections
    //interception area percentages that trigger the callback.
    threshold: [...Array(100).keys()].map(x => x / 100) 
  }
  
  //The argument being the observe() target elements
  //of which only some are entries[0].isIntersecting
  function entered(entries){
    console.log( entries[0] )
  }

  let interObs = new IntersectionObserver(entered, options)
  interObs.observe(target.current)
}, [])
```

1

1

1

1

1

1

Check this exercise:

{% embed url="https://codepen.io/misterlinux/pen/zYWxgdX?editors=1010" %}

The Intersection Observer is a **web platform API**, already included in JS without running in the main thread.

it _asynchronously observe intersections_ between the target elements and the **user viewpoint**:

```
//For the ScrollSPY we first need the paragraphs and the nav-items

let sidecont = document.querySelectorAll(".colonna section")
let sidenav = document.querySelectorAll(".flex-column li")

//we create a callback function for the constructor for EACH of the paragraph when intersected

function sided(sides){
  sides.map((side)=>{
  
//side being an instance of IntersectionObserverEntry object with boundingClientRect position, dimensions of target and intersection roots
  
    if(side.isIntersecting){
      //this will execute each time sidecont interfers
      ...
    }
  })
}

//then we can create an optional configuration for the IntersectionObserver 
//threshold is the portion to be intersected before calling the function, at 0 is always visible
//while rootMargin adds margin (- will reduce it) to the viewpoint, usefull for fixed headers

let come={
  threshold: 1,
  rootMargin: "-5px",
}

//the constructor with both callback function and optional
let sideview = new IntersectionObserver(sided, come)

sidecont.forEach((ciglia)=>{

//after creating the contructor(sideview) we call the method .observe() onEACH single NODE element

  sideview.observe(ciglia)
})

```

The code we execute after the Intersection will be:

```
//we will use classList for the bootstrap highlighted effect "active"

function sided(sides){
  sides.map((side)=>{
    if(side.isIntersecting){
    
//each NodeList element will get the current active removed
      sidenav.forEach(item=>item.classList.remove("active"))
      
//and, with the INTERSECTED element ID, we "active" the NAV element withthe same href=""
      let attiva1= document.querySelector(`.flex-column li[href*=${side.target.id}`).classList.add("active")
    }
  })
}

```

we can **remove any observation** with;

```
//inside the callback function

____.unobserve(entry.target); 

```

### Color randomizer & Smooth redirect

For the color we simply:

```
//We return a random string of 3 RGB 255 numbers

function colora(){
  let risulta = 0;
  
  for(let i= 0; i< 3; i++){
    risulta+= (Math.random()*255).toFixed() + ","
  }

//we slice the starter 0 and teh last (,)
  return `rgb(${risulta.slice(1,-1)})`
}

//then we color a node
boxe.forEach((x)=>{
  x.style.backgroundColor = colora();
})

```

For the Redirect smooth we get:

```
//For each nav link, 

document.querySelectorAll('.nav-items').forEach(link => {
  link.addEventListener('click', function(e) {
    e.preventDefault();

//first we get the attribute and cut the # using .substrng(1), then we get the corresponsive ID
    let href = this.getAttribute('href').substring(1);
    const scrollTarget = document.getElementById(href);

//we get the .getBoundingClientRect().top and we select the DOM element to scroll
    const elementPosition = scrollTarget.getBoundingClientRect().top;
    let colonna = document.querySelector(".colonna")

//when scrolling the selected we move the TOP in a smooth behavior
    colonna.scrollBy({
      top: elementPosition - 150,      //we remove the margin 150px we added with CSS
      behavior: 'smooth',
    });

  });
});

```

### Intersection CSS style animation

Check this webpage:

{% embed url="https://codepen.io/misterlinux/pen/yLKObpq" %}

About the **translateX and scale() transformation animation** on Intersected images:

```
//We select the image text sections, remember that all other elements 
//not intersected are under the ELSE styling

let image = document.querySelectorAll(".imagi")

let inter = new IntersectionObserver((entrie)=>{
    entrie.forEach((entry)=>{
        if(entry.isIntersecting){

//we get the fading effect with opacity from 0/1, while the scale() and translateX()
//we also used .classList.value to get the classes as an array, and with specific classes
//we get other effects on the ternary operator

            entry.target.style.opacity = 1
            entry.target.classList.value.includes("panini") ? entry.target.style.transform = "scale(1)" : entry.target.style.transform = "translateX(0rem)"
            entry.target.style.backgroundColor = entry.target.getAttribute("data-color");;
        }else{
            entry.target.style.opacity = 0
            entry.target.classList.value.includes("panini") ? entry.target.style.transform = "scale(0)" : entry.target.style.transform = "translateX(-15rem)"

        }
    })
},
{
    threshold: 0.3
})

```

We can **access attributes** from querySelectors:

```
//On the HTML we can have specific data attributes we can use in the script.js

let image = document.querySelectorAll(".imagi")
<div class="imagi pure-g" data-color="#f1bace">

entry.target.style.backgroundColor = entry.target.getAttribute("data-color");;

```

We still need to **add transition** in the CSS:

```
//For the .querySelector selector, we will have a transition delay
.imagi{
    transition: 0.5s;
}

//we can have the extra background effect
.capsule{
    background-color: aliceblue; 
}

```

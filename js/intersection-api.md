# Intersection API

* This is a list
* siamo quasi&#x20;
* eccolo

### Intersection Observer API

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

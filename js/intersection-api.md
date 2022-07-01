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



  //AFTER SETTNG UP THE INTERSECTIONOBSERVER we call the method observe to start observing 
  //we call the method observe after creating the contructor TO THE SINGLE NODE ELEMENT
 

sidecont.forEach((ciglia)=>{

//after creating the contructor(sideview) we call the method .observe() onEACH single NODE element

  sideview.observe(ciglia)
})

```

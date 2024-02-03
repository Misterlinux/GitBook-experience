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
  
let interObs;
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

  interObs = new IntersectionObserver(entered, options)
  interObs.observe(target.current)
}, [])
```

1

We need the IntersectionObserver() object and the target to **unobserve**() :

```jsx
//remember to declare both outside useEffect()
//If used twice it returns an error, and won't work in <strictMode/>
function annulla(){
  interObs.unobserve(target.current) 
}
```

{% embed url="https://codesandbox.io/p/sandbox/intersectionobserver-examples-v5y5mt?file=/src/App.js:146,1" %}

1

1

We _observe() loop_ when there are **multiple** intersection **targets**.

```jsx
let sections = document.querySelectorAll(".sticky-container")

sections.forEach((element)=> {
  observer.observe(element)
})
```

We can use **rootMargin** to create a 0px top intersect for the root.

```jsx
//used to intersect a sticky top element without scroll or height 
let options={
  root: contain.current,
  rootMargin: '0px 0px -100% 0px'
}
```

The _**entries**_ of the callback function will be the _current inView_ from the root, with true/false isIntersect based on the (options).

The **toggle()** method **force** argument only adds/removes on true/false.                                                      We can add a scroll() event with the callback function, but it won't return any target data.

```jsx
//We forEach() the entries to toggle() first
function passed(entries){
  root.current.addEventListener('scroll', inside)

  entries.forEach((element)=>{
    element.target.classList.toggle('active', element.isIntersecting)
  })
}

//To add a scroll-like intersection trigger we use an array of small thresholds
options:{
  threshold: [...Array(100).keys()].map((x)=> x/100)
}
```

<details>

<summary>IntersectionRatio, boundingClientRect and intersectionRect</summary>

The **intersectionRatio** entry prop is the target intersection percentage to the root.

The **boundingRect** prop is the target position relative to the entire root, its top becomes negative once it's scrolled over, with the formula bottom - top = height.

The **intercectRect** is the rectangle area where the target and **root viewpoint** overlay, it's 0 when no intersect, its bottom-top returns the **current intersect height** of the target, and the **top** prop stops updating once it has been intersected and no longer visible (unlike boundingRect)

```jsx
//ratio< 1 is equal to entry.isIntersecting, where we add/remove the scroll()
//On the X axis, boundingRect right-left = width
//We compare intersectRect to detect the target's position

let ratio = entry[0].intersectionRatio
const boundingRect = entry[0].boundingClientRect;
const intersectionRect = entry[0].intersectionRect;

if( ratio=== 0 ){
  contain.current.removeEventListener('scroll', vediamo);
}else if( ratio < 1 ){
  boundingRect.top < intersectionRect.top ? "on top":"on bottom" 
}else{
  stato.current.innerText = "inside"
}
```

</details>

We can modify both the root and target without needing to change the intersectObserver().

<figure><img src="../.gitbook/assets/Immagine 2024-02-03 150457.png" alt="" width="280"><figcaption></figcaption></figure>

{% embed url="https://codesandbox.io/p/sandbox/intersectionobserver-event-bounding-intersect-rect-qdg6s6" %}

1

1

1

1

1

1

### Color randomizer & Smooth redirect

For the color we simply:

```
//We return a random string of 3 RGB 255 numbers

function colora(){
  let risulta = 0;
  
  for(let i= 0; i< 3; i++){
    risulta+= (Math.random()*255).toFixed() + ","
  }

//we slice the starter 0 and the last (,)
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

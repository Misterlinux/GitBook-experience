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
  
let interObs = useRef();
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

  interObs.current = new IntersectionObserver(entered, options)
  interObs.current.observe(target.current)
}, [])
```

We need the IntersectionObserver() object and the target to **unobserve**() :

```jsx
//remember to declare both outside useEffect()
//If used twice it returns an error, and won't work in <strictMode/>
//We use useRef() to keep the intersectionObserver() value after animations
function annulla(){
  interObs.current.unobserve(target.current) 
}
```

{% embed url="https://codesandbox.io/p/sandbox/intersectionobserver-examples-v5y5mt?file=/src/App.js:146,1" %}

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

<figure><img src="../.gitbook/assets/Immagine 2024-02-03 150457.png" alt="" width="280"><figcaption><p>interceptionObserver() animated sections</p></figcaption></figure>

{% embed url="https://codesandbox.io/p/sandbox/intersectionobserver-event-bounding-intersect-rect-qdg6s6" %}

This is how we animate intersectionObserver() **nav-items**.

{% tabs %}
{% tab title="Nav-items html" %}
**Nav-items** need to share a class/id with the **section** elements in the root.

```jsx
//The alternative nav-item has no <a>

<nav className="d-flex">
  <div className="nav-item uno1">
    <a href="#uno1" className="nav-link">Uno </a>
  </div>
  <div className="due2 nav-item" onClick={(e)=> mosso(e)}>
    Secon
  </div>
  ..
</nav>

<section className="singolo" id="uno1">
  {Texto()}
</section>
```
{% endtab %}

{% tab title="js Intersect" %}
We use the **entry.isIntersecting** target to querySelect() the **nav-item**, and add() our CSS class.

```jsx
//We store a queryselector() value by setting it outside useEffect()
//we removeAll/add the current intersect element
//we can target the href document.querySelector(`.nav-item a[href*=${side.target.id}`)

let naviga;
useEffect(()=>{
  naviga = document.querySelectorAll("nav.d-flex .nav-item")
  
  ...
  if( entry.isIntersecting ){
    naviga.forEach(item=>item.classList.remove("active"))
    
    document.querySelector(`.nav-item.${entry.target.id }`).classList.add("active")
  }
}, [])

```
{% endtab %}
{% endtabs %}

Instead of the href/id scroll, we can manually scrollInView the section elements using **offsetTop**.

```tsx
//This won't move the window page scroll in any way
//The offsetTop requires position-absolute on the scroll to be precise 

function mosso(event){
  event.preventDefault()

  let container = event.target

  let adesso = document.querySelector(`section#${container.classList[0]}`);

  let moment = adesso.offsetTop + 10
  adesso.parentNode.scrollTop = moment
}
```

The **absolute** scroll area won't return any height to its **relative** parent container, so we use document.offsetHeight for design's sake.

```jsx
//Use margins on the relative to not modify the absolute offsetHeight
let sopra = useRef()
let sotto = useRef()

sopra.current.style.height = sotto.current.offsetHeight + "px"

<div ref={sopra} className="position-relative d-flex justify-content-center">

  <div ref={sotto} className="position-absolute row mx-0 col-8">
  </div>
</div>
```

{% embed url="https://codesandbox.io/p/sandbox/intersectionobserver-animated-navbar-h9vw3n?file=/src/App.js:149,17" %}

We **cache** IntersectionObserver ID **querySelect()** outside the intersect function for easier manipulation and access.

```jsx
//The forEach observer target 
let finalmente = document.querySelectorAll(".stratos")

//The cache outside the intersectionObserver function
let navElems = {}
finalmente.forEach((navId) => {
  navElems[navId.id] = document.querySelector(`.nav-item.${navId.id}`)
})

//So when it's time to edit the DOM element we 
elements[entry.target.id].classList.add("active")
//document.querySelector(`.nav-item.${entry.target.id }`).classList.add("active")
```

### Intersection CSS style animation

Check this webpage:

{% embed url="https://codepen.io/misterlinux/pen/yLKObpq" %}

{% embed url="https://codesandbox.io/p/sandbox/animated-intersectionobserver-sections-reactjs-vd6x8j?file=/src/App.js" %}
React js version&#x20;
{% endembed %}

We animate the sections with toggle() **css keyframes**.

```jsx
//Unlike useSpring() it won't animate on removal.
entry.target.classList.toggle("active", entry.isIntersecting)

.zone .row.active{
  animation: example 0.5s;
}

@keyframes example{
  0% {transform: scale(0);}
  100% {transform: scale(1);}
}
```

We can access tag **attributes** or modify the **entry.target.style** directly.

```jsx
<div class="imagi pure-g" data-color="#f1bace">
entry.target.style.backgroundColor = entry.target.getAttribute("data-color");;

//We check for a single className
if(entry.isIntersecting){
  entry.target.classList.value.includes("panini") ? 
    entry.target.style.opacity = 1 : entry.target.style.opacity = 0
}
```

1

We apply the Intersection Observer API on the **\<Parallax> root**, to limit the intersection to the scroll **window** of its layers, and not the entire page.

```jsx
//We can getElementById() it from a <ParallaxLayer> component.

useEffect(()=>{
  let window= document.getElementById("questo")

  let options= {
    root: window,
    rootMargin: "0px 0px -100% 0px",
    threshold: 0,
  }
}

<Parallax pages={3.3} className="meno" ref={ultimo} id="questo">
  <ParallaxLayer offset={0} style={{ backgroundColor: "lightskyblue" }}>
    <Secondo/>
  </ParallaxLayer>
  ...
</Parallax>
```

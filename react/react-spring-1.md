# React-Spring-1 useSpring(), config, To() interpolate render, useTransition(), React-use-measure.

* [To() interpolate render useSpring()](react-spring-1.md#to-interpolate-render-and-usestate-conditional-usespering-prop.)
* [The useTransition() animation styles and methods](react-spring-1.md#the-usetransition-animation-styles-and-methods)
* [React-use-measure on events and react-spring animations](react-spring-1.md#react-use-measure-on-events-and-react-spring-animations)

**React-spring** is a **library** to create **animated** UI components.

```jsx
npm i react-spring

App.js
import { animated, useSpring } from '@react-spring/web'
```

We _**import**_ the **animated** higher-order **component**, a function that takes a component and returns a new one.

{% tabs %}
{% tab title="Animation object" %}
We **useSpring({})** hook to set up the **animation object** properties **(**from, to**)**, then we deconstruct it on the **style** property to trigger it on mount.

```jsx
//we append the component on the animated high-order tag
function MyComponent() {

  const springs = useSpring({
    from: { x: 0 },
    to: { x: 100 },
  })

  return (
    <animated.div
      style={{
        width: 80, height: 80,
        background: '#ff6d6d',
        borderRadius: 8,
        ...springs
      }}
    />
  )
}

<div> <MyComponent /> </div>
```
{% endtab %}

{% tab title="Api animations methods" %}
We **deconstruct** the **api object** from the **useString({})** hook, and use its **methods** to control the **state** of the spring animation on _event listeners_.

```jsx
//Use className on the animated.div to style with CSS
//useString({}) needs to include all the animation properties used by the api
//The api methods control the state of the animation (start, stop set, etc)

.box{
    display: flex;
    width: 80px; height: 80px;
    background-color: brown;
    border: solid 5px yellow;
    border-radius: 10px;
}

function MyComponent() {

  const [springs, api] = useSpring(() => ({
    from: { x: 0, y: 0 },
  }))

  const handleClick = () => {
    api.start({
      from: {
        x: 0,
        y: 0,
      },
      to: {
        x: 150,
        y: 50,
      },
    })
  }

  return (
    <animated.div
      onClick={handleClick}
      className="box"
      style={{
        ...springs      
      }}
    />
  )
}
```
{% endtab %}
{% endtabs %}

<figure><img src="../.gitbook/assets/moved1.gif" alt="" width="293"><figcaption><p>Spring animation object + onClick api</p></figcaption></figure>

The <**animated**> **component** can be used directly on the **JSX** with a useString() as a style prop.

We can use the **useSpringRef()** hook to **reference** the imperative **API** on event handlers.                         &#x20;

Any **difference** between the useSpring() and the api.method **from:{}** will be **skipped.**&#x20;

```jsx
import { animated, useSpring, useSpringRef } from '@react-spring/web'
let refe = useSpringRef()

let init = useSpring({
  from: {x: 0, transform: "rotate(0deg)"},
  ref: refe
})

//from() isn't needed and to can be an array of [{props}]
function mosso(){
  refe.start({
    from: {x: 50, transform: "rotate(30deg)"},
    to: {x: 150, transform: "rotate(330deg)"},
    config: {duration: 5500},
    loop: {reverse: true}
  })
}

<animated.div
  onClick={mosso} className="boxo"
  style={{ ...init }}
>
</animated.div>
```

<figure><img src="../.gitbook/assets/rotated.png" alt="" width="416"><figcaption><p>useSpring(9 state and event handler function with useSringRef()</p></figcaption></figure>

We can _useSpringRef()_ to **pause**/**resume** and **stop** animations.

```jsx
//after stop() it won't resume()
init.pause()
init.resume()
init.stop()

//We can do the same on a useSpring() api.
```

The useSpring() **config property** contains **mass, tension, friction, easing**, and **duration** (used for its "timing function")

```jsx
//duration stops working when using any timing function prop
const basic = useSpring({
  from: {background: 'brown'},
  config:{
    tension: 280, friction: 120,
    easing: "easeOutQuart", duration: 3000,
  },
}

//We can edit the config on the api event handler function.
const handleClick = () => {
  basic.start({
    y: 20,
    config: {
      friction: 10,
    },
  })
}
```

<details>

<summary>List of useString() presets configs</summary>

We can use **both** a **preset** and a **custom** config property.

```jsx
const basic = useSpring({
  from: {background: 'brown'},
  config: config.wobbly,
  config:{
    duration: 3000,
  },
}
```

There are the presets configs.

```jsx
default – { tension: 170, friction: 26 }
gentle – { tension: 120, friction: 14 }
wobbly – { tension: 180, friction: 12 }
stiff – { tension: 210, friction: 20 }
slow – { tension: 280, friction: 60 }
molasses – { tension: 280, friction: 120 }
```

</details>

### To() interpolate render and useState() conditional useSpering() prop.

Using the <mark style="background-color:blue;">**to**</mark> _method_, we **interpolate** the **springValue** data on a different **style** property (we can animate both), and access its pure value (without the spring Object).

<pre class="language-jsx"><code class="lang-jsx">// 0/360 used for rotate and translateX if the useSpring is included

import { animated, useSpring , config, to } from '@react-spring/web'
const props = useSpring({
  from: { x: 0 },
  to: { x: 360 },
  config: {duration: 2500}
})
<strong>
</strong><strong>//we use x springValue both as rotate/x transform
</strong>&#x3C;animated.h2 style={{ 
  transform: props.x.to(value => `rotateX(${value}deg)`),
  ...props
}}>
  { props.x.to(value => value) }  //0-360
&#x3C;/animated.h2>
</code></pre>

We use the **springValue** as a **breakpoint** to animate a style property.

```jsx
const auto = useSpring({
  from: { x: 0 },
  to: { x: 1 },
  config: {duration: 3000}
})

style= {{
  transform: auto.x
    .to([0, 1], [0, 100])
    .to((x) => `translate(${x}px)`),
}}
```

&#x20;We **useState()** a **conditional springValue** and the <mark style="background-color:blue;">**to(**</mark><mark style="background-color:blue;">)</mark> method to animate style props.

**Range** are the animated _springValues_ breakpoints, **output** are the style property **values** on each **breakpoint,** and both only accept **integers** in their **array**.

```jsx
//We need {} on the springValue to access the to() method
//We don't need the from{} property

const [vedo, setVedo] = useState(false)
const {dice} = useSpring({
  dice: vedo ? 1 : 0,
  config: {duration: 5000},
})

//only color strings will work, and they will be only rendered as rgb()
<animated.div 
  onClick={ ()=> setVedo(!vedo) }
  className="boxo" 
  style={{
    scale: dice.to({ range: [0, 1], output: [0.5, 1.5] }),
    background: dice.to({ range: [0, 0.5, 1], output: ["red", "orange", "pink"] }),
    y: dice.to({ range: [0, 0.8, 1], output: [0, 50, 50] }),
    x: dice.to({ range: [0, 1], output: [0, 200] }),
  }}>
</animated.div>
```

<figure><img src="../.gitbook/assets/rangeOutput.png" alt="" width="293"><figcaption><p>Animated style properties onClick() </p></figcaption></figure>

We use an **array** of <mark style="background-color:blue;">**to**</mark> style **objects** for multiple animations, the array has to **include** the <mark style="background-color:blue;">**from**</mark> object **(**any object before it will be ignored**)**.

```jsx
//A loop: true would skip to the starter point, we need to add reverse.
//This will trigger the animation on mount
let auto = useSpring({
  from: { x: 0, y: 0, scale: 0.5, background: "red"},
  to: [
    { x: 0, y: 0, scale: 0.5, background: "red" },
    { x: 100, y: -50, scale: 1, background: "orange" },
    { x: 200, y: 50, scale: 1.5, background: "pink" },
  ],
  config:{ duration: 1000 },
  loop:{ reverse: true }
})

<animated.div className="boxo" style={auto}>
</animated.div>
```

We animate a **useSpring()** **background image** using only output (will work on its first 2 values).

<pre class="language-jsx"><code class="lang-jsx"><strong>//It can't even be set/triggered onClick()
</strong><strong>const {value} = useSpring({
</strong>  from: {value: 0}, to: {value: 1},
  loop: {reverse: true},
  config: {duration: 6000}
})

let x2= value.to({ output: ['0%', '20%'] })

&#x3C;div className="conta">
  &#x3C;animated.div className="suono" style= {{ x: x2 }} >
  &#x3C;/animated.div>
&#x3C;/div>
</code></pre>

When animating a CSS background image, we use [**inset** ](#user-content-fn-1)[^1]to **cover** the X/Y empty margins.

```css
//A bigger background avoids empty border spaces during the animation
.conta{
  position: relative;
  width: 85vw;
  height: 30vh;
  overflow: hidden;
  background-color: burlywood;
}

/*use inset: -200%; width: 400%; height: 400%; for bigger animations*/
.treno{
  inset: 0% -25%;
  width: 125%; height: 100%;
  position: absolute;
  opacity: 0.3;
  background: url('https://media.timeout.com/images/105782103/image.jpg') center;
  background-size: cover;
}
```

<figure><img src="../.gitbook/assets/backgroundInset1.gif" alt="" width="375"><figcaption><p>Animated useSpring() background image</p></figcaption></figure>

We can directly **destruct** a **useSpring()** object and re-assign **property** names.

```jsx
//The destructured property then the assigned name
let {opacity, transform: tran} = useSpring({
  opacity: 0.5,
  transform: rotateX(180deg)
})

<animated.div style={{ opacity }}></animated.div>	
<animated.div style={{ transform: tran }} > </animated.div>
```

<details>

<summary>Destruct useSpring() rotateX property on 2 display absolute images</summary>

The 2 **absolute** images are **overlayed** in the same container.

<pre class="language-jsx"><code class="lang-jsx"><strong>&#x3C;div className="carta" onMouseEnter={ronda} onMouseLeave={ronda}>
</strong>  &#x3C;animated.div className="carta davanti">
  &#x3C;/animated.div>

  &#x3C;animated.div className="carta dietro">
  &#x3C;/animated.div>
&#x3C;/div>

//The event handlers are on the container coz the images will be rotated
.carta{
  position: absolute;
  width: 200px;
  height: 120px;
}

.davanti,
.dietro{
  background-size: cover;
}

.davanti{
  background-image: url(https://images.neon.com);
}

.dietro{
  background-image: url(https://images.sea.com);
}
</code></pre>

We **useState()** the _useSpring()_ destructured style **properties**, and use them on the **animated** components.

```jsx
//We use perspective() for a better rotateX effect, or add rotateY()
const [conta, setConta] = useState(false)

let {opacity: opa, transform: tran } = useSpring({
  opacity: conta ? 1 : 0,
  transform: `perspective(600px) rotateX(${conta ? 180 : 0}deg) `,
  config: {duration: 1000}
})

const ronda = () => { setConta(!conta) }
```

Both **images share** the useSpring() style **properties**, the **front** image **opacity** needs to be **opposite** from the back one, we edit it using the <mark style="background-color:blue;">to</mark> method.

```jsx
//we add an opposite rotateX to the back image to avoid it being upside down.
<div className="carta" onMouseEnter={ronda} onMouseLeave={ronda}>

  <animated.div 
    className="carta davanti"
    style={{ 
      opacity: opa.to(x=> 1- x),
      transform: tran
    }}
  >
  </animated.div>

  <animated.div 
    className="carta dietro"
    style={{ 
      opacity: opa, transform: tran,
      rotateX: "-180deg"
    }}
  >
  </animated.div>
</div>
```

</details>

<figure><img src="../.gitbook/assets/rotating.gif" alt=""><figcaption><p>rotateX and opacity useString() on 2 absolute images</p></figcaption></figure>

### The useTransition() animation styles and methods

The **useTransition(**array, config**)** hook **sequentially** _animates_ datasets of elements on the DOM.

```jsx
//enter{} is the current style, from/leave are before/after the transition
//exitbefore will repeat the transition to remove the current element before the next

const transitions1 = useTransition([content], {
  from: { opacity: 0 },
  enter: { opacity: 1 },
  leave: { opacity: 0 },
  exitbefore: true,
  config: { duration: 5000 },
})

//useTransition() can start() with useSpringRef().
```

useTransition() triggers automatically, we useEffect() to bind it to events.

{% tabs %}
{% tab title="onRest() auto transitions" %}
We create a useState() **index** and an **array** of components to render with the useTransition()&#x20;

```jsx
import { useTransition, animated, useSpringRef } from '@react-spring/web'
const [indice, setIndice] = useState(0)

//Each component will have the useTransition style prop as an argument.
//useful for when cycling different types of components
const pages = [
  ({ style }) => (
    <animated.div style={{ ...style, background: "pink"}}>A</animated.div>
  ),
  ...
]
```

The useTransition() **onRest method** triggers each time an animated **transition** is **completed**.

The useTransition() renders one element at a time and the **onRest() method** updates its index to re-trigger the transition.

<pre class="language-jsx"><code class="lang-jsx">//useTransition() triggers each time its array changes.
//onRest(animationResult-object, spring controller,current index)
<strong>const transitions1 = useTransition(indice, {
</strong>  from: { opacity: 0, y: "50%"},
  enter: { opacity: 1, y: "0%"},
  leave: { opacity: 0, y: "0%"},
  config: { duration: 2000 },
  onRest: (string, crtl, item)=> {
    if (indice === item) {
      setIndice(x => (x+ 1) % 3 )
    }
  }
})
</code></pre>

The useTransition() hook returns the style object and its array content (an useState() index)

```jsx
<div>
  {transitions1((style, content) => {
    const Page = pages[content]
    return <Page style={style} />
  })}
</div>
```

<figure><img src="../.gitbook/assets/transition1.gif" alt=""><figcaption><p>automatic useTransition() updated onRest()</p></figcaption></figure>
{% endtab %}

{% tab title="onClick() index transition" %}
We **useEffect()** the useTransition() **index** as a \[dependency] to have the _initial animation_ and start() it onClick().

```jsx
//to keep the index from 0 to 2 (images indexes) we use % 3
const IMAGES = [
  'https://images/art-piece-for-you.png',
  'https://images/generative-waves.jpg',
  'https://images/generative-art.jpg',
]

const [activeIndex, setActiveIndex] = useState(0)
function clicka(){
  setActiveIndex(x => (x+ 1) % 3 )
}

useEffect(() => {
  springApi.start()
}, [activeIndex])

const springApi = useSpringRef()
```

We animate the clipPath height from 0-100-0 while keeping the width at 100.

```jsx
const transitions = useTransition(activeIndex, {
  from: {  clipPath: 'polygon(0% 0%, 0% 100%, 0% 100%, 0% 0%)' },
  enter: { clipPath: 'polygon(0% 0%, 0% 100%, 100% 100%, 100% 0%)' },
  leave: { clipPath: 'polygon(100% 0%, 100% 100%, 100% 100%, 100% 0%)' },
  exitBeforeEnter: true,
  config: {
    duration: 3000,
  },
  delay: 1000,
  ref: springApi,
})

<div>
  {transitions((springs, item) => (
    <animated.div className="img__container" style={springs} onClick={clicka}>
      <img src={IMAGES[item]} />
    </animated.div>
  ))}
</div>
```

<figure><img src="../.gitbook/assets/clicktrans1.gif" alt=""><figcaption><p>exitbeforeenter useTransition() animation onClick()</p></figcaption></figure>
{% endtab %}
{% endtabs %}

{% embed url="https://codesandbox.io/p/devbox/usetransition-onrest-components-l3k3z8?file=/src/App.css:18,1" %}
useTransition() index useState() to render different components
{% endembed %}

To render **multiple \<animated>** elements we put an **array** as a **useTransition**() argument.

```jsx
//The elements will overlay if position: absolute
let lista = [{ numba: 1,chiave: "unn"}, ... ]

//The map() is not needed, it is to contrast with the next example
let multipli = useTransition(
  lista.map((x)=> ({...x})),
  {
    key: (item)=> (item.chiave),
    from: {opacity: 0},
    enter: { opacity: 1, background: "red", x: 20, margin: 10 },
    leave: {opacity: 0}
  }
)

//we can use the array properties with item.chiave
<div>
  {multipli( (style, item, t, index)=>(
    <animated.div className="boxo text-center position-absolute" style={{...style}}>
      <p className='text-white'>Index {index}</p>
    </animated.div>
    ))
  }
</div>
```

<figure><img src="../.gitbook/assets/firstoTrans.png" alt="" width="299"><figcaption><p>useTransition() multiple elements</p></figcaption></figure>

Changing the order of its useState() array _doesn't trigger_ the useTransition() **animation** by itself.

We need to **dynamically create** style properties, from _external variables_, during the array methods, which we extract in the <mark style="background-color:blue;">enter</mark> object.

The **update()** property **animates** its extracted style **properties** each time the array **argument changes**, it also requires the property value to be set using an **external counter.**

```jsx
//Update reacts on property changes, contrary to add/remove, shuffle doesnt trigger
//Each update will translate3D() the previous x properties values.
let y= 0;

let colore = useTransition( 
  lista1.map((x)=> ({ ...x, x: y+= x.numba*25}) ), 
  {
    key: (item)=> (item.chiave ),
    from: { opacity: 0 },
    update: ({x}) => ({x}),
    enter: ({x}) => ({ opacity: 1, background: "red", x}),
    leave: { opacity: 0 },
  }
)

const rein = () => setLista1(shuffle)
```

<details>

<summary>useTransition() to set and update style properties with imported objects.</summary>

We npm install lodash (javascript library) and import the shuffle method.

```jsx
import shuffle from 'lodash.shuffle'
shuffle([1,2,3,4,5])	//[3, 5, 4, 2, 1]
```

We **useState()** an array of **style objects** to render and animate with **useTransition()**.                                                                                                                         We _useEffect()_ setInterval() to trigger the **shuffle method.**

```jsx
//we need to cleanInterval() it before re-starting

export default [
  {
    name: 'Rare Wind',
    description: '#a8edea → #fed6e3',
    css: 'linear-gradient(135deg, #a8edea 0%, #fed6e3 100%)',
    width: 120,
  },
  ...
]

import data1 from './Data1'
const [lista, setLista] = useState(data1)

useEffect(() => {
  const t = setInterval(() => setLista(shuffle), 2000)
  return () => clearInterval(t)
}, [])
```

We update the **counter** on the **array**.**method** argument and to set the **style property (x)**.

```jsx
//We update the counter and set spring objects with the imported object
let spazio = 0

const transitions = useTransition(
  lista.map(data => ({ ...data, x: (spazio += data.width) - data.width })),
  {
    key: (item) => item.name,
    from: { width: 0, opacity: 0 },
    update: ({ x }) => ({ x }),
    enter: ({ x, width }) => ({ x, width, opacity: 1, height: 80 }),
    leave: { width: 0, opacity: 0 },
  }
)
```

We use the **counter** to style the **container**, the **spring** object to style the <mark style="background-color:blue;">\<animated></mark>, and the imported **object properties** to style the single-_child components_.

<pre class="language-jsx"><code class="lang-jsx"><strong>&#x3C;div className="list1" style={{ width: spazio }} onClick={mischia} >
</strong>
  {transitions( (style, item, t, index) => (
    &#x3C;animated.div className="card1" style={{ ...style }}>

      &#x3C;div className="details1" style={{ backgroundImage: item.css }} />

    &#x3C;/animated.div>
  ))}
&#x3C;/div>
</code></pre>

The components share the same space (width) with position: absolute.

```css
.card1 {
  position: absolute;
  height: inherit;
  padding: 15px;
}

.details1 {
  height: 100%;
}
```

</details>

<figure><img src="../.gitbook/assets/useTransition1.gif" alt=""><figcaption><p>useTransition() on {x} updated style property</p></figcaption></figure>

### React-use-measure on events and react-spring animations

The React-use-measure tool references DOM elements to measure their boundaries.

```jsx
//The bounds objects contain { width, height, top, right, bottom, left, x, y}
npm i react-use-measure 

import useMeasure from 'react-use-measure'
const [ref, bounds] = useMeasure()


function sopra({ clientX, clientY }){
  setXY([clientX, clientY])
}

<div className="inside" ref={ref} onMouseMove={sopra}>
  <div className="box"></div>
</div>

<div>
  <p> Mouse x {Math.round(xy[0] - bounds.left)}px </p>
  <p> Mouse y {Math.round(xy[1] - bounds.top)}px </p>
</div>
```

<figure><img src="../.gitbook/assets/reactMeasure.png" alt="" width="294"><figcaption><p>Relative Cursor position on measured DOM element</p></figcaption></figure>

We access the from/to useSpring() properties with:

```jsx
props.width.animation.to             //the to:{} width value
props.width.to(x => x.toFixed(0))    //Dom rendering inside an <animated />
```

<details>

<summary>Animating measures with useSpring() conditional values.</summary>

We **animate** a react-use-measure **width** with a **useState() trigger**, using a t**ernary operator** to change the **useSpring()** target **value** (to).

The relative>absolute **child** component is **animated** using its **parent** measured **width**.

```jsx
//we need the animated component to access the useSpring() prop

const [open, toggle] = useState(false)
const [ref, { width }] = useMeasure()
const props = useSpring({ width: open ? width : 0})

<div ref={ref} className="main" onClick={()=> toggle(!open) } >
  <animated.div className="fill" style={props} />

  <animated.div className="content">
    {props.width.to(x => x.toFixed(0))}
  </animated.div>
</div>
```

The children **CSS** properties set its **starter animation condition** (from).

```css
//the .fill element starts from the left and its height covers the parent
.main {
  position: relative;
  width: 200px;
  height: 50px;
  cursor: pointer;
  border-radius: 5px;
  border: 2px solid #272727;
  overflow: hidden;
}

.fill {
  position: absolute;
  left: 0;
  height: 100%;
  background: hotpink;
}

.content {
  position: absolute;
  display: flex;
  color: #272727;
  width: 100%; height: 100%;
  align-items: center; justify-content: center;
}
```

The useString() **api** for the event works too, but we **can't reset** the (from) **property** yet.

```sass
const [propa, api] = useSpring(()=>({
  from: {width: 0 }
}))

function checkin(){
  api.start({
    from: {
      width: 0,
    },
    to: {
      width: width,
    },
  })
}

<div ref={ref} className="main" onClick={checkin} >
  <animated.div className="fill" style={propa} />

  <animated.div className="content">
    {propa.width.to(x => x.toFixed(0))}
  </animated.div>
</div>
```

</details>

<figure><img src="../.gitbook/assets/fillAnimation.png" alt="" width="335"><figcaption><p>Animated width property on useState()</p></figcaption></figure>

The **matchMedia()** method returns _true_ if the document **matches** (or is higher) than its _media-query string_.

```jsx
//The matching media query index on the value array returns the column's integer
//If width > 1000 then matchMedia("(min-width:1000px)").matches == true
//We add/remove an eventListener ("resize") to update the useState() match value
function useMedia(queries, values, defaultValue) {

  function match(){      
    return values[queries.findIndex(q => matchMedia(q).matches)] || defaultValue
  }
  const [value, set] = useState(match)

  useEffect(() => {
    const handler = () => set(match)
    window.addEventListener("resize", handler)
    return () => window.removeEventListener("resize", handler)
  }, [])

  return value
}

const colonne = useMedia(
  ['(min-width: 1100px)', '(min-width: 900px)', '(min-width: 600px)'], [5, 4, 3], 2)

console.log(colone)  //3
```

We **useMeasure()** to dynamically **set** the column's **width** on **media queries**.

```jsx
//On resize we can get the current width and useMedia() columns
const [ref, { width }] = useMeasure()

let wide = width / colonne

<div ref={ref} className="text-center">
  <p> Each of the {colonne} columns will be {wide} px</p>
</div>
```

<figure><img src="../.gitbook/assets/columnsWidth.png" alt="" width="428"><figcaption><p>columns and with on resize</p></figcaption></figure>

We **import** an **array** of image objects as a **useState**(), to use in the useTransition().

```jsx
export default [
  { css: 'https://images.pexels.com/.../pexels-photo-416430.jpeg', height: 150 },
  ...
]

import Data from './Data';
const [items, set] = useState(Data)
```

{% tabs %}
{% tab title="Render and shuffle images" %}
We import the **lodash.shuffle** method to useEffect() **shuffle** the _useState(Data)_.

```jsx
//npm install lodash is a javascript utility library
//or npm install lodash.shuffle if it doesn't work
import shuffle from 'lodash.shuffle'
console.log( shuffle([1,2,3,4] )   //[3,2,4,1]

useEffect(() => {
  const t = setInterval(() => set(shuffle), 5000)
  return () => clearInterval(t)
}, [])
```

We set the (container) **height** and the useTransition() **images objects array** on a **useMemo**().

```jsx
//its useMedia() dependencies change on resize
//The heights array length and values get updated on items.map()

const [heights, gridItems] = useMemo(() => {
  let heights = new Array(columns).fill(0) 

  let gridItems = items.map((child, i) => {
    const column = heights.indexOf(Math.min(...heights)) 

    const x = (width / columns) * column 
    const y = (heights[column] += child.height / 2) - child.height / 2 

    return { ...child, x, y, width: width/ columns, height: child.height / 2 }
  })

  return [heights, gridItems]
}, [columns, items, width])
```

On shuffle the images **translate-X/Y**, change **column** array **position**, and the (max) height container is updated.

```jsx
//trail is the delay of images overlapping during the transition
//we deconstruct the useTransition() animated argument properties 
const transitions = useTransition(gridItems, {
  key: item => item.css,
  from: ({ x, y, width, height }) => ({ x, y, width, height, opacity: 0 }),
  enter: ({ x, y, width, height }) => ({ x, y, width, height, opacity: 1 }),
  update: ({ x, y, width, height }) => ({ x, y, width, height }),
  leave: { height: 0, opacity: 0 },
  config: { mass: 5, tension: 500, friction: 100 },
  trail: 5,
})

<div ref={ref} className="list" style={{ height: Math.max(...heights) }}>
  {transitions((style, item) => (
    <animated.div style={style}>
      <div style={
       { backgroundImage: `url(${item.css}?auto=compress&dpr=2&h=500&w=500)`}
      } />
    </animated.div>
  ))}
</div>
```

<figure><img src="../.gitbook/assets/shuffleColumn.png" alt="" width="563"><figcaption><p>useTransition() image grid</p></figcaption></figure>
{% endtab %}

{% tab title="Add images onClick()" %}
We **useState()** the current images **objects** and **indexes**.

```jsx
let [images, setImages] = useState([ ])
let [adesso, setAdesso] = useState(0)
```

On click, we add the Data(index) object to the useState(), useMemo() remains the same. &#x20;

```jsx
//useState() delays by one, but it works as an index coz we use < and not <=

function add(){
  setAdesso((x)=> x + 1)

  if( adesso < Data.length ){
    setImages((x)=> ( [...x, Data[adesso] ] ))
  }else{
    console.log("all images added")
  }
} 
      
<button className="btn btn-secondary" onClick={()=> add()}>
  Add
</button>
```
{% endtab %}

{% tab title="setInterval() load images" %}
We update the useState() with **useRef()**.

```jsx
//works with useEffect(), doesn't work with onClick() 
let [images, setImages] = useState([ ])

let vol = useRef(0)
let volta = vol.current

//don't useRef() inside a useState() setter function
function add(){
  volta += 1

  if( volta < Data.length ){
    let nuovo = Data[volta]
    setImages((x)=> ( [...x, nuovo ] ))
  }else{
    console.log("all images added")
  }
}
```

On load, we **useEffect()** _setInterval()_ the add() function until all image objects are added.

<pre class="language-jsx"><code class="lang-jsx">//The starting add() will render only once on load
//We need to use clearInterval() twice
<strong>let inter;
</strong>
useEffect(()=>{
  add()
  
  inter = setInterval(()=>{
    add()

    volta == Data.length &#x26;&#x26; clearInterval(inter)
  }, 1000)

  return () =>{
    clearInterval(inter)
  }
}, [volta])
</code></pre>

The useMemo() remains the same.
{% endtab %}

{% tab title="CSS images" %}
In both examples, the images need position absolute to share the same line.

```
.list {
  position: relative;
  width: 100%;
  height: 100%;
}

.list > div {
  position: absolute;
  padding: 15px;
}

.list > div > div {
  background-size: cover;
  background-position: center center;
  width: 100%;
  height: 100%;
}
```
{% endtab %}
{% endtabs %}

{% embed url="https://codesandbox.io/p/github/Misterlinux/useTransition-image-grid/main?file=/src/components/Second.js&workspaceId=010fde04-8e9a-4164-9de5-448b0152b2f1" %}
codeSandbox of the github example
{% endembed %}

[^1]: Negative internal margins,

    same css syntax.                &#x20;

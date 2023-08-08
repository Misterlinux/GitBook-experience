# React-Spring 1

* 1
* 1
* 1
* 1

**React-spring** is a **library** to create **animated** UI components.

```
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

Any **difference** between the useSpring() and the api.method **from:{}** will be **skipped**, to be animated style properties need to be both on <mark style="background-color:blue;">from</mark> and <mark style="background-color:blue;">to</mark>.

```
import { animated, useSpring, useSpringRef } from '@react-spring/web'
let refe = useSpringRef()

let init = useSpring({
  from: {x: 0, transform: "rotate(0deg)"},
  ref: refe
})

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

```
//after stop() it won't resume()
init.pause()
init.resume()
init.stop()

//We can do the same on a useSpring() api.
```

The useSpring() **config property** contains **mass, tension, friction, easing**, and **duration** (used for its "timing function")

```
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

```
const basic = useSpring({
  from: {background: 'brown'},
  config: config.wobbly,
  config:{
    duration: 3000,
  },
}
```

There are the presets configs.

```
default – { tension: 170, friction: 26 }
gentle – { tension: 120, friction: 14 }
wobbly – { tension: 180, friction: 12 }
stiff – { tension: 210, friction: 20 }
slow – { tension: 280, friction: 60 }
molasses – { tension: 280, friction: 120 }
```

</details>

Using the <mark style="background-color:blue;">**to**</mark> _method_, we **interpolate** the **springValue** data on a different **style** property (we can animate both).

```
// 0/360 used for rotate and translateX if the useSpring is included

import { animated, useSpring , config, to } from '@react-spring/web'
const props = useSpring({
  from: { x: 0 },
  to: { x: 360 },
  config: {duration: 2500}
})

style={{ 
  transform: smal.x.to(value => `rotateZ(${value}deg)`),
  ...props
}}
```

We use the **springValue** as a **breakpoint** to animate a style property.

```
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

We use a **conditional springValue** and the <mark style="background-color:blue;">**to**</mark> method to **animate** the **style** properties.                             **Range** are the animated _springValues_ breakpoints, **output** are the style property **values** on each **breakpoint**.

```
//We need {} on the springValue to access the to method
//The useSpring() breakpoint needs to share the name with springValue
//We don't need the from{} property

const [vedo, setVedo] = useState(false)
const {dice} = useSpring({
  dice: vedo ? 1 : 0,
  config: {duration: 5000},
})

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

```
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

### The useTransition() animation styles and methods

The **useTransition(**array, config**)** hook **sequentially** _animates_ datasets of elements on the DOM.

```jsx
//enter{} is the current style, from/leave are before/after the transition
//exitbefore will repeat the transition to remove the current element before the next

const transitions1 = useTransition(indice, {
  from: { opacity: 0 },
  enter: { opacity: 1 },
  leave: { opacity: 0 },
  exitbefore: true,
  config: { duration: 5000 },
})
```

{% tabs %}
{% tab title="onRest() auto transitions" %}
its array argument can also be an index while its config is different from useSpring()

```jsx
//We create an array of animated components to use the index in
import { useTransition, animated, useSpringRef } from '@react-spring/web'
const [indice, setIndice] = useState(0)
const refe = useSpringRef()

const pages = [
  ({ style }) => (
    <animated.div style={{ ...style, background: "pink"}}>A</animated.div>
  ),
  ({ style }) => (
    <animated.div style={{ ...style, background: "blue"}}>B</animated.div>
  ),
  ({ style }) => (
    <animated.div style={{ ...style, background: "green"}}>C</animated.div>
  )
]
```

The useTransition() onRest method triggers each time an animated transition is completed.

```jsx
//Its arguments are: animationResult (enter style object and the cancelled/finished properties)
//the spring controller and the useTransition index

const transitions1 = useTransition(indice, {
  from: { opacity: 0, transform: 'translate3d(0,50%,0)' },
  enter: { opacity: 1, transform: 'translate3d(0%,0,0)' },
  leave: { opacity: 0, transform: 'translate3d(0,-50%,0)' },
  config: { duration: 2000 },
  ref: refe,
  onRest: (string, crtl, item)=> {
    if (indice === item) {
      setIndice(x => (x+ 1) % 3 )
    }
  }
})
```

The updated useState() index triggers the useEffect() which re-starts the transition.

```jsx
useEffect(() => {
  refe.start()
}, [indice])
```

The useTransition() hook returns the style objects and the index for the animated component to render.

```jsx
<div>
  {transitions1((style, i) => {
    const Page = pages[i]
    return <Page style={style} />
  })}
</div>
```

<figure><img src="../.gitbook/assets/transition1.gif" alt=""><figcaption><p>automatic useTransition() updated onRest()</p></figcaption></figure>
{% endtab %}

{% tab title="Second Tab" %}
1

1

1

1

1

1

1

1
{% endtab %}
{% endtabs %}

1

1

1

1

1

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

We access th from/to useSpring() properties with:

```
props.width.animation.to             //the to:{} width value
props.width.to(x => x.toFixed(0))    //Dom rendering inside an <animated />
```

<details>

<summary>Animating measures with useSpring() conditional values.</summary>

We **animate** a react-use-measure **width** with a **useState() trigger**, using a t**ernary operator** to change the **useSpring()** target **value** (to).

The relative>absolute **child** component is **animated** using its **parent** measured **width**.

```
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

1

1

1

1

1

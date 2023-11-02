# React-Spring 5

* elements
* element
* elle

The **useResize()**, a useSpring() abstraction, returns the **useRef()** container width/height props.     Its onChange() will trigger on **window resize**.

```jsx
//width returned in px
let quad = useRef(null)

const { width, height } = useResize({
  container: quad,
  onChange: ({value: {width} })=> {
    console.log( width )
  }
})

<div>
  <div style={{ width: "50vw", height: "30vh", background: "yellow" }} ref={quad}>
  </div>
</div>
```



### Animated useSpring() with useInView() hook&#x20;

The **useInView()** hook tracks an element's **visibility** relative to the **viewpoint**.                                      It will return the **useRef()** for the target element and the **boolean** result of the _Intersection Observer API_

```jsx
import { animated, useSpring, useInView} from '@react-spring/web'

let [ref, inview] = useInView({
  //Element's visibility percentage that will trigger the useInView() callback
  threshold: 0.5,         //independently of scroll direction
  rootMargin: '-20% 0%',  //margins around the element
  once: true,             //will trigger the API once
})

let springs = useSpring({
  opacity: inview ? 1 : 0,
  y: inview ? 0 : 100,
  backgroundColor: inview ? "red" : "pink",
})

<div ref={ref}>
  <animated.div style={springs}>
    {inview ? "Visible" : "inVisible" }
  </animated.div>
</div>

```

1

<details>

<summary>Multi directional section with useInView()</summary>

When having **multiple** useInView() elements keep the **threshold below 0.5**, to _not overlay_ and _repeat_ the _useSpring()._

```jsx
//We can add a delay to the useInView
//Instead of absolute/width animation we used margin/opacity
let [primo, visto1] = useInView({
  delay: 500,
  threshold: 0.2
})
let seePrimo = useSpring({
  opacity: visto1 ? 1 : 0,
  marginLeft: visto1 ? 0 : 500,
})

let [secon, visto2] = useInView({
  delay: 500,
  threshold: 0.2
})
let seeSeco = useSpring({
  opacity: visto2 ? 1 : 0,
  marginRight: visto2 ? 0 : 500,
})

let [tri, visto3] = useInView({
  delay: 500
})
let seeTri = useSpring({
  opacity: visto3 ? 1 : 0,
  marginTop: visto3 ? 0 : 200,
})

<div>
  <animated.div ref={primo} className="bg-warning" 
    style={{ height: "100vh", ...seePrimo }}>
  </animated.div>

  <animated.div ref={secon} className="bg-primary" 
    style={{height: "100vh" , ...seeSeco }}>
  </animated.div>

  <animated.div ref={tri} className="bg-danger" 
    style={{width: "100%", ...seeTri}}>
    <div style={{height: "100vh"}}></div>
  </animated.div>
</div>
```

</details>

1

### Rendering useSprings() with index-based style properties

The **useSprings**() hook renders a **number** of indexed springs with _shared props_, their **index**es can set their individual properties and be used to animate specific useSpring().

```jsx
//We render 3 useSprings() each with x based on their array index
//The index conditional is needed in api.start() to not animate every element
let [lista, listaapi] = useSprings(3, i => ({
  from: {x: 0},
  to: {x: i * 100}
})

listaapi.start(i =>{
  if (i !== 2) return

  return {x: 400}
})
```

<details>

<summary>Overlayed useDrag() swipe effect and function from/to useSpring() props</summary>

We use the useSprings() **index** as **arguments** for the **from/to functions**.

The starting animation is a translateY(-1000), we distance (4px) and rotate each card, and the **delay** adds the _stack effect_.

```jsx
import { animated, useSprings, to as interpolate} from '@react-spring/web'
import { useDrag } from '@use-gesture/react'

const cards = [
  'https://RWS_Tarot_08_Strength.jpg',
  'https://RWS_Tarot_16_Tower.jpg',
  'https://RWS_Tarot_07_Chariot.jpg',
  'https://RWS_Tarot_06_Lovers.jpg',
  'https://RWS_Tarot_02_High_Priestess.jpg',
  'https://RWS_Tarot_01_Magician.jpg',
]

const from = (_i) => ({ x: 0, rot: 0, scale: 1.5, y: -1000 })

const to = (i) => ({
  x: 0,
  y: i * -4,
  scale: 1,
  rot: -10 + Math.random() * 20,
})

const [props, api] = useSprings(cards.length, i => ({
  from: from(i),
  to: to(i),
  config: { duration: 2000 },
  delay: i * 100,
})) 

//We can add perspective(1500px) rotateX(30deg) for 3D effect
const trans = (r, s) =>
  `rotateY(${r / 10}deg) rotateZ(${r}deg) scale(${s})`
```

We create a useState() **set** to store the _swiped cards_ (like an array but with no repeated elements and has() method)

We **useDrag**() the current passed **arg**ument **index** element, on **mx** only movement, we **add()** it to the **set** if it exceeds the _minimal velocity_ after it _stops being dragged_.

We api.start() only the useDrag() index useSpring(), onDrag() its **scale** gets bigger, its position and **rot**ation depend on the mx drag distance if it's in the **set()** then we **swipe** it out of viewpoint.

When drag is finished and all cards are **set()** we **clean()** it and **api.start()** the last card keyframe (**to**)

```jsx
//on '@use-gesture/react' velocity is an array, sow e take [1]
//The config tension gets changed onDrag()
const [gone] = useState(() => new Set())

const bind = useDrag(({ args: [index], down, movement: [mx], velocity }) => {

  const trigger = velocity[1] > 0.15 
  const dir = mx < 0 ? -1 : 1 

  if (!down && trigger) gone.add(index) 

  api.start(i => {
    if (index !== i) return

    const isGone = gone.has(index)

    const x = isGone ? (200 + window.innerWidth) * dir : down ? mx : 0 
    const rot = mx / 100 + (isGone ? dir * 10 * velocity[1] : 0) 
    const scale = down ? 1.1 : 1 

    return {
      x, rot, scale,
      config: { friction: 150, tension: down ? 800 : isGone ? 200 : 500 },
    }
  })

  if (!down && gone.size === cards.length)
    setTimeout(() => {
      gone.clear()
      api.start(i => to(i))
    }, 600)
})
```

We loop **useSprings**() into 2 components, with its **index** in the **useDrag**() as **arg**ument.                                                                                                                                                                 A **relative** container for the overlayed _absolute cards_, with **overflow** to not expand the width on swipe.

```jsx
//We use interpolate to set the useSpring() props in the trans function
//We need touchAction and axis for the useDrag()
import {to as interpolate} from '@react-spring/web'

<div className="d-block position-relative" 
     style={{width: "100%", height: "60vh", overflow: "hidden"}}>
     
  {props.map(({ x, y, rot, scale }, i) => (
    <animated.div className="deck1" key={i} style={{ x, y }}>

    <animated.div
      {...bind(i)}
      style={{
        transform: interpolate([rot, scale], trans),
        backgroundImage: `url(${cards[i]})`,
        touchAction: 'pan-x',
        axis: 'x'
      }}
    />
    </animated.div>
  ))}

</div>
```

We add a **custom** circle **cursor** to the absolute cards, and set the background properties.

```css
.deck1{
  position: absolute;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  touch-action: none;
}

.deck1 > div{
  cursor: url('
    https://uploads.codesandbox.io/uploads/user/b3e56831-8b98-4fee-b941-0e27f39883ab/Ad1_-cursor.png') 
    39 39, auto;

  background-color: white;
  background-size: auto 85%;
  background-repeat: no-repeat;
  background-position: center center;
  width: 45vh;
  height: 85vh;
  box-shadow: 0 12.5px 100px -10px rgba(50, 50, 73, 0.4);
}

```

</details>

1

1

1

1

1

1

1

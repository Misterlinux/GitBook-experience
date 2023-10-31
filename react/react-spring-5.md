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

1

1

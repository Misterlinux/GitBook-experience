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

1

1

1

1

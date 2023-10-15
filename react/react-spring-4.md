# React-Spring 4

* 1
* 1
* 1
* 1

**useSpring**() animations **run** even without rendering, and _api.start() can't change keyframes_ during a loop. We create a new useSpring() hook to change the style keyframe.

<details>

<summary>Changing useSpring() on the same loop element</summary>

The new useSpring() _won't start_ **from:{}** but from its already running **keyframe**.

```jsx
//It will render depending on the number of keyframes and duration
//It renders on teh DOM so we useState()

let [spin, api] = useSpring(()=>({
  from: {x: 0, y: 0, background: "lightblue"},
  to: [
    {x: 100, background: "lightblue"},
    {y: 80, background: "blue"},
    {x: 0, background: "lightgreen"},
    {y: 0, background: "green"},
  ],
  loop: true,
  config: config.wobbly
}))

let [spin1, api1] = useSpring(()=>({
  from: {x: 0, y: 0, background: "pink"},
  to: [
    {x: 100, background: "pink"},
    {y: 80, background: "orange"},
    {x: 0, background: "red"},
    {y: 0, background: "orange"},
  ],
  loop: true,
  config:{ duration: 1000 }
}))

let [stile, setStile] = useState(spin)
let trigger2 = useRef(false)

function cambio1(){

  trigger2.current ? setStile(spin) : setStile(spin1)
  trigger2.current = !trigger2.current
}

<div className="d-block">

  <animated.div className="boxo" style={stile}>
  </animated.div>

  <div className="text-center">
    <button className="btn btn-primary" onClick={cambio1}>Change</button>
  </div>

</div>
```

</details>

We can pause() and resume() to **keep** the useSpring() **keyframe** when changed.

<details>

<summary>Changing useSpring() while pause() resume() keyframes</summary>

We **useEffect**() to keep the second useSpring() paused before resuming and rendering it.&#x20;

```jsx

let [spin2, api2] = useSpring(()=>({
  from: {x: 0, y: 0, background: "lightblue"},
  to: [
    {x: 100, background: "lightblue"},
    {y: 80, background: "blue"},
    {x: 0, background: "lightgreen"},
    {y: 0, background: "green"},
  ],
  loop: true,
  config: config.wobbly
}))

let [spin3, api3] = useSpring(()=>({
  from: {x: 0, y: 0, background: "pink"},
  to: [
    {x: 100, background: "pink"},
    {y: 80, background: "orange"},
    {x: 0, background: "red"},
    {y: 0, background: "orange"},
  ],
  loop: true,
  config:{ duration: 1000 }
}))

let [stile1, setStile1] = useState(spin2)
let trigger3 = useRef(false)

let fermo1 = true

useEffect(()=>{
  api3.pause()
}, [fermo1])

function cambio2(){

  if(trigger3.current){
    setStile1(spin2)
    api2.resume()
    api3.pause()      
  }else{
    setStile1(spin3)
    api3.resume()
    api2.pause()
  }

  trigger3.current = !trigger3.current
}


<div className="d-block">

  <animated.div className="boxo" style={stile1}>
  </animated.div>

  <button className="btn btn-primary" onClick={cambio2}>
    Resume
  </button>

</div>
```

</details>

1

1

1

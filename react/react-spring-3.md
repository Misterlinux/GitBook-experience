# React-Spring 3

* 1
* 1
* 1
* 1

The **useSpringValue**() hook sets **singular** style properties, it accesses useSpring() **methods** imperatively.

```jsx
//It doesn't require the API call
//it won't trigger on the updated component, we must use its methods.

let valore = useSpringValue(0, {
  config: config.wobbly,
})

let valore1 = useSpringValue(0, {
  config:{ mass: 20, friction: 3, tension: 10 },
})

function starto(){
  valore.start(300)
  valore1.start(300)
}

<div>
  <animated.div className="barra1" style={{ width: valore }} >
  </animated.div>

  <animated.div className="barra2" style={{ width: valore1 }} >
  </animated.div>
</div>
```

<figure><img src="../.gitbook/assets/useSpringValue.png" alt="" width="375"><figcaption><p>useSpringValue() on animation-timing function</p></figcaption></figure>

### Animation sequences with useChain()

The **useChain**( \[**useSpringRef**() array], \[timesets], timeframe ) hook _links multiple animations_ in a specified **order**, controlling their **timing**.

We useState() as a trigger for the **useChain()**, which will **revert** the animation's **order** and delays.

```
//Their timing delays will be timeset-array-value * timeframe (1000ms if not set)
const [open, set] = useState(false)

useChain( 
  open ? [colonna, refe] : [refe, colonna]  , 
  [0, open ? 1.5 : 3] 
)
```

The _useSpring()_ values and the _useTransition()_ element depend on the useState(), to not trigger before the useChain() timing.

```
//Without a useChain() useSpringRef() would block the animation

const colonna = useSpringRef()
const { largo } = useSpring({
  ref: colonna,
  from: { largo: "0%" },
  to: { largo: open ? "100%" : "0%" },
  config: { duration: 1500 },
});

let lego = ["red", "blue", "green", "yellow" ]

let refe = useSpringRef();
let muro = useTransition( open ? lego : [], {
  ref: refe,
  trail: 2000 / lego.length,
  from: {opacity: 0, height: 0 },
  enter: {opacity: 1, height: 120 },
  leave: {opacity: 0, height: 0},
  config: {duration: 3000}
})
```

1

1

1

1

1

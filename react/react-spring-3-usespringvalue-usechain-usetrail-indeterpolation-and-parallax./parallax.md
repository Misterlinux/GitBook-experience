# Parallax

* 1
* 1
* 1

A single **useEffect()** can log DOM **userRef**()/querySelector() elements.&#x20;

The **ParallaxLayer** children components need an **useState**() dependency, that triggers only after the page is useEffect() <mark style="background-color:blue;">**mounted**</mark>, to be logged.

```jsx
//Remember React.strictmode is active when testing

useEffect(() => {
  setParallaxLayerMounted(true); 
}, []);

const stratosRefs = useRef([]);

useEffect(()=> {
  console.log( stratosRefs )
}, [parallaxLayerMounted])
```

A Parallax component won't respond to a scroll event, only to a **wheel**.

<details>

<summary>Wheel event on Parallax</summary>

On useRef() \<Parallax/>

```jsx
//We 100vh Parallax in case the Parallax doesn't adapt to the screen
const parallaxRef = useRef(); 

useEffect(()=> {
  window.addEventListener('wheel', handleWheelEvent);
}, [])

const handleWheelEvent = (index) => {
  const {container, current} = parallaxRef.current;
  const scrollpercent = 
    current / (container.current.scrollHeight - window.innerHeight)
  console.log(scrollpercent);
};

<div className="App">
  <Parallax pages={2} ref={parallaxRef} style={{ height: "100vh" }}>
    <ParallaxLayer offset={0}>
      <div ref={(ref) => (stratosRefs.current[0] = ref)}>
        <h1>This is the moment he became</h1>
      </div>
    </ParallaxLayer>
    ...
  </Parallax>
</div>
```

1

1

1

</details>

1

1

1

1

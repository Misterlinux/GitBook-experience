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

The wheel event is attached to the useRef() of the \<Parallax> container.

```jsx
const parallaxRef = useRef(); 

useEffect(()=> {
  window.addEventListener('wheel', handleWheelEvent);
  
  return () => {
    if (parallaxRef.current) {
      parallaxRef.current.removeEventListener('wheel', handleWheelEvent);
    }
  };
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
      <h1>This is the moment he became</h1>
    </ParallaxLayer>
    ...
  </Parallax>
</div>
```

</details>

&#x20;We **useRef(\[])** \<ParallaxLayer/> child elements, check useRef() callback function in [React4](../../js/js-4-spread-and-rest-operators-error-constructor-and-object-try-catch-and-finally.md).

```jsx
//We 100vh Parallax in case the Parallax doesn't adapt to the screen
const stratosRefs = useRef([]);

//We loop the DOM reference objects using its id, className, and style properties
useEffect(()=> {
  console.log( stratosRefs )
  //IntersectObserver callback function
  ...
  entries.forEach((entry)=> {
    if(entry.isIntersecting){

      stratosRefs.current.forEach((ref, index)=> {
        if( ref.id == entry.target.id ){
          console.log( ref.className.includes("bg-warning") )
          ref.style.color = "pink"
        }
      })
    }
  })
  ...
}, [parallaxLayerMounted])

<div className="App">
  <Parallax pages={2} style={{ height: "100vh" }}>

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

1

1

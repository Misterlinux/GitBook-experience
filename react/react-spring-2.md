# React-Spring 2

1

* 1
* 1

The **useTrasition()** **style** props can be **rendered** only in an **animated.div** component.

The **Enter** and **Leave** prop **animate** the useTransition() elements being **added/removed** to the DOM, _both will be rendered_ when the element is [<mark style="background-color:blue;">updated</mark>](#user-content-fn-1)[^1].

When the animation is _re-triggered_ the **first** and **final frame** of [<mark style="background-color:blue;">**Enter**</mark> ](#user-content-fn-2)[^2]will be **animated**, to avoid rendering it (and "reset" the transition) _we add a starter frame_ with different property values.

```jsx
//We update the useTransition() by changing the useState()
//opacity is not on the last/first "frame" of Enter, it "reset" the style props
//leave removes the old element while from/enter renders the new
const [mio, setMio] = useState(["another"])

const estilo = useTransition(mio, {
  from: {
    centra: "40%",
    back: "transparent", long: "0%", lefto: "0%",
    display: "block", color: "black", opacity: 0, width: "100%",
  },
  enter: [
    { opacity: 0.8 }, 
    { back: "pink", color: "green", long: "90%", lefto: "10%", opacity: 1 },
    { back: "orange", color: "blue", long: "50%", lefto: "50%" },
    { back: "red", color: "red", long: "0%", lefto: "100%"}
  ],
  leave: [
    { centra: "0%", display: "none" }
  ],
  update: { color: "purple" },
  config:{ duration: 1000 }
})

let plus = useRef(0)

function changed(){
  plus.current += 1;
  ( plus.current % 2 ) ? setMio( ["nuovo"] ) : setMio( ["another"] )
}

//We deconstruct the custom-style properties for the containers
//and use the ...rest-operator for the child component
<div className="d-flex justify-content-center mt-5 mb-2">
  {estilo(( {centra, back, long, lefto, ...resto} , item) => (
  
    <animated.div className="centra" style={{ width: centra }} >
      <animated.div 
        className="sfondo"
        style={{backgroundColor: back, width: long, left: lefto}}
      >
      </animated.div>

      <animated.div className="testo" style={resto}>
        {item}
      </animated.div>
    </animated.div>
  ))}
</div>

```

<figure><img src="../.gitbook/assets/RepAnimation3.gif" alt="" width="297"><figcaption><p>useTransition() on updated() element update</p></figcaption></figure>

1

1

1

1

1

1

1

[^1]: when the useTransition() element is changed.

[^2]: useTransition() enter{} prop

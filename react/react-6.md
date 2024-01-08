# REACT 6

* 1
* 1
* 1
* 1

We use **objects** to dynamically **style** _components_, using JSX brackets.

```jsx
//remember that style needs double {}
const picture={
  url: "https://.../635258.jpg",
  width: 100,
  height: 100
}

function Imma({pic}){
  return (
    <img 
      className= {cubo}
      src= {pic.url}
      style={{
        height: pic.height + "px",
        width: pic.width + 30 + "px"
      }}
    />
  )
}
```

On the DOM we pass the **style object prop** to the **component**.

```jsx
<div className="row col-10 justify-content-center">
  <Imma pic={picture} />
  <div>
    <button className="btn btn-primary" onClick={rotate}>
      Rotate
    </button>
  </div>
</div>
```

We install the **classnames** utility to join JSX classes conditionally.

```jsx
//First the classname then (:) the condition, we can include default classNames 
//we useState() to render the conditions

const [roll, setRoll] = useState(false)

let cubo= cn("p-0", {
  "rotola": roll
})

function rotate(){
  setRoll((x)=> (!x))
}

//and use the className to toggle the CSS animation 
.rotola{
  animation: roll 4s;
  animation-iteration-count: infinite;
}

@keyframes roll {
  100%{
    transform: rotate(360deg);
  }
}
```

<figure><img src="../.gitbook/assets/rotateClassname.png" alt="" width="507"><figcaption><p>DOM element animated with a classname object</p></figcaption></figure>

### React-Bootstrap Navbar and Carousel

We need to use **flexGrow:0** for the nav elements justify and a custom-fill **SVG** for the React-bootstrap collapse icon.

{% embed url="https://codesandbox.io/p/devbox/react-bootstrap-navbar-collapse-637pfp?file=/src/App.js:14,22" %}
Navbar and Navbar collapse
{% endembed %}

<figure><img src="../.gitbook/assets/navbarreactBoot.png" alt="" width="375"><figcaption></figcaption></figure>

The react-bootstrap **Carousel** component includes the **.carousel-indicators** and **.carousel-control-prev-icon** (SVG), we edit them to customize the carousel areas.

{% embed url="https://codesandbox.io/p/devbox/custom-react-bootstrap-cj7mgt?file=/src/App.js:24,1" %}
React-Bootstrap Carousel with custom SVG arrows
{% endembed %}

<figure><img src="../.gitbook/assets/CarouselReactBoot.png" alt="" width="330"><figcaption></figcaption></figure>

1

1

1

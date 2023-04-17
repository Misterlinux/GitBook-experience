# REACT 1

* 1
* 1
* 1
* 1

**Node.js** is a _javascript environment_, we use **npm** (Node Package Manager) to install **modules** and use a **packages.json** file to track them.

To create a **React** application folder we run:

```
npx create-react-app (name of the app)

//its /public folder contains the app root
//its /src folder has the different page components
```

**React** is a _javascript library_ used for user interface, based on single-role **components**.

We **render DOM** elements in the **React components** by **abstracting** HTML tags using **jsx**.

<details>

<summary>Render React DOM elements guide</summary>

With Javascript, we needed to createElement, content, a selector, and then **render**:

```
<script type="text/javascript">
  var divNode = document.createElement('div');
  divNode.innerText = 'Hello World';

  var rootElement = document.querySelector('#root');
  rootElement.appendChild(divNode);
</script>

```

On **React** we can:

```
const element = React.createElement("div", {
  children: "Hello World",
});

const rootElement = document.querySelector("#root");
ReactDOM.render(element, rootElement);

```

**JSX** is syntax sugar used to shortcut the createElement:

```
const element = <div>Hello World</div>;

const rootElement = document.querySelector("#root");
ReactDOM.render(element, rootElement);
```

Then we implement it on **components**:

```
import React from "react";
import ReactDOM from "react-dom";

function HelloWorld() {
  return <div>Hello World</div>;
}

export default Base

//we will use the root in App.js
const root = ReactDOM.createRoot(document.getElementById('root'));
```

</details>

A **component** needs to **import** React and can reference multiple components, called child components, but can export only one.

```
//components are shortened versions of <Primo> </Primo> to put code in-between
//we can use Arrow functions for the components
import React from 'react';

const Primo =() =>{
  return(
    <div>
      <h1> first component </h1>
    </div>
  )
}

const Secondo =() =>{
  return(
    <div>
      <h2>Second component</h2>
    </div>
  )
}

const App =() =>{
  return(
    <div>
      <Primo />
      <Secondo />
    </div>
  )
}

export default App;
```

We use **CamelCase** for **component names** to differentiate from default HTML tags.

On **JSX** we can use _javascript expressions_ using **{}**:

<details>

<summary>React javascript expression guide</summary>

We put **javascript** before the **return** of the component.

```
//we can work with arrays, methods and objects
const Second =() =>{
  const linea= ["uno", "molti", "terzi"]

  const oggetto= {
    soggetto: "nome",
    predicato: 12_000
  }
  
  return(
     <div>
       This is a sum: {1 +2 +3}        //6
       molti: {linea.join(" / ") }     //uno / molti / terzi
       two object {oggetto.soggetto} and {oggetto.predicato} //nome and 12000
     </div>
  )
}
```

We can invoke **functions and render loops**:

```
const Primo =() ={

  function object(zona){
    return zona.capital + " and " + zona.province
  }
  const area={
    capital: "lichsteiner",
    province: "vicini"
  }
  const serie= ["meant", 23, "to be"]

  return(
    <div>
      We invoke the function: {object(area)}  //lichsteiner and vicini
      <ul>
        {
          serie.map((x) =>{
            return(                          
              <li>The element is: {x} </li>  //The element is: meant 
            )
          })
        }
      </ul>
    </div>
  )
}
```

</details>

### React Props, event handlers and useState

**Props** are **arguments** passed into React **components,** they are **read-only** and can't be **updated.**

```
//we use it when importing/exporting components
//we declare props argument key and THEN we add properties

const Basic= (props) =>{
  return(
    <div>
        We use the props here: {props.name}
    </div>
  )
}

export default Basic

//The property values change depending on the component which is used
import Base from './components/Basso'

const App= () =>{
  return(
    <Base name="Mozafar" />    //We use the props here: Mozafar
  )
}

export default App;
```

<details>

<summary>Props used with javascript expression</summary>

We can edit the **props** property using **javascript expressions**:

```
//we export the props component with expressions
const Base= (props) =>{
  console.log(props)

  return(
    <div id={ "numero-" + props.numba } >
      The name is {props.name} and number {props.numba}
      <div>nuovo valore: {props.numba + 12} </div>
    </div>
  )
}

export default Base
```

to then assign the **props values** on the rendering components:

```
//and properties values can be expressions themselves
import Base1 from './components/Basso'

let anni= Math.floor( Math.random()*100 )
const App= () =>{
                                           //console.log(
  return(                                  //{name: "Mozafar", numba: 5} )
    <div>                                  //<div id="numero-5">
      <Base1 name="Mozafar" numba={anni}/> //The name is Mozafar and number 5
    </div>                                 //nuovo valore: 17
  )
}

export default App;
```

</details>

We can add **Event Handlers** to React components, like **onClick()**, **onChange()** or **onSubmit()**:

```
//We put the callback function in {} as an expression
const Ano= () =>{
  let add= 0;
  function more(){  
    add += 1
    console.log("Added " + add)    //added 2/3/... 
  }

  return(
    <div>
      <button className='btn btn-warning' onClick={more}>Clikka</button>
    </div>
  )
}
```

We use the React Hook **useState** to track the **state** of a component and **update** it:

<details>

<summary>updating Reactjs  useState() </summary>

We use **destructuring** on the imported **useState object**, into a current state **variable,** and a set **function** to **update** it.

```
//We use .concat() instead of .push() to create a new state array that will 
//get re-rendered on React (instead of just changing current with .push())

import React , { useState } from 'react';

const Ray= () =>{
  let [rar, setRar] = useState([])
  let [uno, setUno] = useState( 21 )

  function innio(){
    setRar( rar.concat("immensi") )
    setUno( 33 )
  }

  return(
    <div>
      <div>Updated array on click {rar}</div>
      <div>Number state changes {uno}</div>
      <button className='btn btn-outline-primary' onClick={innio}>More</button> 
    </div>
  )
}
```

</details>

<figure><img src="../.gitbook/assets/usestato.png" alt=""><figcaption><p>useState() after onClick() </p></figcaption></figure>

### React useEffect with setInterval()

We use the **useEffect(function, dependency)** **hook** to **synchronize** a component with an external **variable.**

```
//useEffect triggers on render, with the setState() change being a render 
//the setTimeout continues, we use empty [] dependency to render it once

let [esempio, setEsempio]= useState(0)

useEffect(() => {
  setTimeout(() => {
    setEsempio((esempio) => esempio + 1);
  }, 1000);
}, [] );

<div>aumentiamo di uno qui {esempio}</div>
```

When a **dependency state** changes the **useEffect** renders:

<details>

<summary>useEffect with dependency useState</summary>

We need 2 **states**, one for the **dependency** change and the other for the **javascript expression.**

```
//When the count1 state changes onClick() the useEffect updates the 
//calculation1 state and we render the javascript expressions

const [count1, setCount1] = useState(0);
const [calculation1, setCalculation1] = useState(0);

useEffect( () =>{
  setCalculation1(()=> Math.pow(count1, 2) - count1*2 )
}, [count1])

<button className="btn btn-outline-success" 
        onClick={()=> setCount1((c)=> c + 1)} >
  aggiungi
</button>
<p>
  We did {Math.pow(count1, 2)} subtracting {count1*2} and got {calculation1}
</p>
```

</details>

<figure><img src="../.gitbook/assets/useeffect.gif" alt=""><figcaption><p>useEffect function rendering after onClick state</p></figcaption></figure>

We can implement **javascript expression** using ternary-operators for DOM content:

```
//we need to use {` ${``} `} for strings

<button className={`btn btn-${ lancetta ? `danger`: `success` }`}
        onClick={()=> setLancetta((w)=> !w ) }
>
  {lancetta ? "pause" : "start" }
</button>

//for variables we don't need ""
const [texto, setTexto] = useState("start")

<button>{lancetta ? texto : "no button" }</button>

```

We use 2 useState() **dependencies** to **start/pause the setInterval()**.

```
//We need the return clearTimeout() to limit the setInterval() to once at the time
//We use the lancetta to true/false the useEffect setInterval() 
//and we use the other dependency useState() to stop it with clearInterval()

const [tempato, setTempato] = useState(0)
const [lancetta, setLancetta] = useState(false)
let interval;

useEffect( ()=>{

  if(lancetta){    
    interval= setInterval( ()=>{
      setTempato((temp)=> temp + 1)
    }, 500)
  }

  if( tempato == 10 ){
    setLancetta( false )
    clearInterval(interval)
  }

  return () =>{
    clearInterval(interval)
  }
}, [lancetta, tempato] )

```

<figure><img src="../.gitbook/assets/intervaleffect2.png" alt=""><figcaption><p>useEffect dependency used with setInterval()</p></figcaption></figure>

We can also trigger **setInterval()** and **clearInterval()** using the same **dependency**:

<details>

<summary>Reset/Restart single button with useEffect</summary>

The **stop/pause button** will reset the current seconds but **not the setInterval()**, for that we use the **reset** button.

```
//We use the true/false switch and javascript expressions

<div className="text-center fs-2 font-weight-bold">
  {seconds}s
</div>

<div className="text-center">
  <button className={`btn btn-${isActive ? `danger`:`success`}` }
          onClick={ ()=> setIsActive((x)=> !x) }>
    {isActive ? 'Pause' : 'Start'}
  </button>
  <button className="btn btn-secondary" onClick={reset}>
    Reset
  </button>
</div>
```

We use useEffect() **conditionals** to trigger the **setInterval()**.

We reset(seconds), and we **clearInterval()** but we **also re-start**, by using setIsActive(!isActive) to **trigger** the useEffect() conditional **again** while keeping the gradi counter.

We stop seconds and active using **reset()**.

```
//We need to include both triggers and state counters in the dependencies

const [seconds, setSeconds] = useState(0);
const [gradi, setGradi] = useState(0)
const [isActive, setIsActive] = useState(false);

function reset() {
  setSeconds(0);
  setIsActive(false);
}

useEffect(() => {
  let interval = null;
  if (isActive) {

    interval = setInterval(() => {
      setSeconds(seconds => seconds + 1);
      setGradi(gradi => gradi + 1)
    }, 500);

  } else if (!isActive && seconds !== 0) {
    clearInterval(interval);
    setSeconds(0)
    setIsActive(!isActive);
  }
  
  return () => {
    clearInterval(interval);
  }
}, [isActive, seconds, gradi]);
```

</details>

<figure><img src="../.gitbook/assets/resetinterval.gif" alt=""><figcaption><p>start/reset/re-start on single button</p></figcaption></figure>

1

1

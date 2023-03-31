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

```
//We use destructuring on the useState object, into a current value variable
//and the set___ function to update it

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
      You want to see il raggi? {rar}
      We are doblgadig the numba {uno}
      <button className='btn btn-outline-primary' onClick={innio}>More</button>
    </div>
  )
}

//for arrays we use .concat() instead of .push()
//instead of changing the current array state, it creates a new state array and 
///gets re-rendered on React
```

1

1

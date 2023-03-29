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
//we can use Arrow functions for the componeets
import React from 'react';

const Primo =() =>{
  return(
    <div>
      <h1> first component
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

1

1

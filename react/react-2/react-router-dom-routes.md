# React-Router-Dom Routes

* 1
* 1
* 1
* 1

We use the [React-Router](https://reactrouter.com/en/main/route/route) library to enable **client-side routing**, allowing the browser to manage page navigation without requesting new HTML documents from the server. &#x20;

Unlike server-side routing, which involves full page reloads, React Router **updates** components and URLs on the **client-side** based on route configurations.&#x20;

<pre class="language-jsx"><code class="lang-jsx">//Not included in React we install its components
npm i react-router-dom

//We change the name of Browser
<strong>import {
</strong>  BrowserRouter as Router,
  Link,
  Route,
  useParams,
  Routes,
  Outlet,
  useRouteError
} from "react-router-dom";
</code></pre>

We configure the \<Route/> **URL path** and **element** component in **Router>Routes**, any component containing React-Router components has to be inside of \<Router/>.

{% tabs %}
{% tab title="Router>Routes>Route" %}
The component outside the \<Router> will remain during all route URL updates.

```jsx
//The "/" component will be the initial render of the page
import Primo from './components/Primo';
import Secondo from './components/Secondo';
import Home from './components/Home';

return (
  <div>
    <Title />

    <Router>
      <Navi />
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path=":primo" element={<Primo />} />
        <Route path=':primo/fisso1' element={<Secondo />} />
        <Route path=':primo/:secondo' element={<Secondo />} />
      </Routes>
    </Router>
  </div>
);
```

<figure><img src="../../.gitbook/assets/VariableRoutes.png" alt=""><figcaption><p>Fixed and Variable routes</p></figcaption></figure>
{% endtab %}

{% tab title="<Link> and components" %}
The \<Link> component renders an **a**nchor tag, on click, it updates the URL and route based on the <mark style="background-color:blue;">**to**</mark> prop, handling the process **declaratively** (i.e., specifying what to do, not how to do it) and avoiding a full page reload.

The route path can be either **fixed** and explicit or **dynamic**, where certain parts of the URL are replaced with :variables that are later defined by the values provided in the \<Link> **to** prop.

```jsx
//The : is in the Route path, not the to prop
function Title(){
  return <h1>Fixed, No router component</h1>
}

function Navi(){
  return(
    <div>
      <p> <Link to="/"> Home </Link> </p>
      <p> <Link to="variabile"> variable </Link> </p>
      <p> <Link to="variabile/fisso1"> variable+fixed </Link> </p>
      <p> <Link to="vari1/vari2"> variable+variable </Link> </p>
    </div>
  )
}
```
{% endtab %}
{% endtabs %}

1

```jsx
// Some code
function Title(){
  return <h1>Fixed, No router component</h1>
}

function Navi(){
  return(
    <div>
      <p> <Link to="/"> Home </Link> </p>
      <p> <Link to="variabile"> variable </Link> </p>
      <p> <Link to="variabile/fisso1"> variable+fixed </Link> </p>
      <p> <Link to="vari1/vari2"> variable+variable </Link> </p>
    </div>
  )
}

return (
  <div>
    <Title />

    <Router>
      <Navi />
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path=":primo" element={<Primo />} />
        <Route path=':primo/fisso1' element={<Secondo />} />
        <Route path=':primo/:secondo' element={<Secondo />} />
      </Routes>
    </Router>
  </div>
);
```

1

1

1

1

1

1

{% embed url="https://codesandbox.io/p/sandbox/react-dom-router-variables-9zhwzh?file=/src/App.js&from-embed=" %}
Variable \<Route/> path with \<Outlet/> and absolute paths
{% endembed %}

1

### Using \<Outlet /> with React Routes&#x20;

**Route elements** **replace** their _previous Route elements_ **unless** they are **fixed components outside Router** (like navbars and other UI components).

**\<Outlet />** can **render** parent routes with their **children's routes** element by **nesting** their **\<Route>**

1

{% tabs %}
{% tab title="Outlet route" %}
Nested \<Route/> elements need \<Outlet/> to be rendered with their parent's element.

<pre class="language-jsx"><code class="lang-jsx"><strong>//Without &#x3C;Outlet/> nested &#x3C;Route/> elements don't even render.
</strong><strong>&#x3C;Router>
</strong>  &#x3C;Routes>
    &#x3C;Route path="/" element={&#x3C;Home />} /> 
    
    &#x3C;Route path="/primo" element={&#x3C;Primo/>}>
      &#x3C;Route path="/primo/secondo" element={&#x3C;Secondo/>} />
    &#x3C;/Route>
  
    &#x3C;Route path="terzo" element={&#x3C;Third/>} />
  &#x3C;/Routes>
&#x3C;/Router>
</code></pre>
{% endtab %}

{% tab title="Primo/secondo <Outlet/>" %}
**Outlet** allows the parent \<Primo/> element will **render with** its **nested** \<Secondo/> element.&#x20;

```jsx
function Primo(){

  return(
    <div className="text-center">
      <h1>Homepage</h1>
      
      <Link to="secondo">  
        <p>Outlet component</p>
      </Link>
      
      <Outlet/>
    </div>
  )
}

function Secondo(){
  
  return(
    <div className="text-center">
      <p>Outlet component</p>
    </div>
  )
}
```

Any **link path** outside the nested \<Route/> will be rendered alone.
{% endtab %}

{% tab title="Third <Route/>" %}
This is an out-of-nested \<Route>, but we can render the _nested element_ with \<Link>

```jsx
//To do so we need the absolute <Link/> path 
function Secondo(){

  return(
    <div className="text-center">
      <p>Replaced Route component</p>
        
       <Link to="/primo/secondo">
         <h3>To the nested</h3>
       </Link>
    </div>
  )
}
```
{% endtab %}
{% endtabs %}

1

1

<details>

<summary>Render Route element components with useParams() variables</summary>

We create a **router** with the homepage and the **variable route** for the link **pat**hs.

```jsx
<Router>
  <Routes>
    <Route path="/" element={<House />} />
    <Route path=":variable/*" element={<Topic />} />
  </Routes>
</Router>
```

On the homepage, we _loop_ and _render_ the **object properties** as **paths.**&#x20;

```jsx
//The imported getSites() returns the array of objects to loop
import { getSites } from "../Content";
import Final from "./Final";

let topico = getSites();

<div>
  <ul>
    {topico.map(({ id, name }) => (
      <li key={id}>
        <Link to={id}> {name} </Link>
      </li>
    ))}
  </ul>
  
</div>

```

On the variable path, we extract the **current route** with **useParams()** and _render_ its corresponding _component_.

```jsx
//while also passing the :variable path as a prop.
import Primo from "./Primo";
import Secondo from "./Secondo";
import Terzo from "./Terzo";

function Topic() {
  const { variable } = useParams();

  const modules = {
    Primo,
    Secondo,
    Terzo
  };

  const Module = modules[variable];

  return (
    <div className="d-flex justify-content-center">
      <div className="d-block">
        <h1 className="text-center"> {variable} </h1>

        <Module fonte={variable} />
      </div>
    </div>
  );
}
```

On each of the variable route **components**, we _loop_ and _render_ their **resources** property.

We set the **Final** _Route element_ in the variable path to render it with the previous elements.

```jsx
//The Final component will render the resource id/name as :articolo path
import { getResor } from "../Content";
import Final from "./Final";

function Primo(prop) {
  let risorsa = getResor(prop.fonte);

  return(
    <div>
      <ul>
        {risorsa.resources.map((id) => (
          <li key={id.id}>
            <Link to={id.id}>{id.name}</Link>
          </li>
        ))}
      </ul>

      <Routes>
        <Route path=":articolo" element={<Final />} />
      </Routes>
    </div>
  )
}
```

The Final component renders the name and id props of the resource array elements.

```jsx
//We need both the path variables to use the imported getDesc()
import { getDesc } from "../Content";

function Final() {
  const { variable, articolo } = useParams();
  let { name, description } = getDesc({ variable, articolo });

  return (
    <div>
      <h4> {name} </h4>
      <p> {description} </p>
    </div>
  );
}

```

We **export** the **array** of objects and the **function** to filter its properties.

```jsx
//We render and loop the id/name for the route paths

const topico = [
  {
    name: "This is the Primo window",
    id: "Primo",
    resources: [
      {
        name: "Why React Hooks?",
        id: "why-react-hooks",
        description: `In this post you'll ...`
      },
      ...
    ]
  },
  ...
]

export function getSites() {
  return topico;
}

export function getResor(fonte) {
  return topico.find(({ id }) => id == fonte);
}

export function getDesc({ variable, articolo }) {
  return topico
    .find(({ id }) => id == variable)
    .resources.find(({ id }) => id == articolo);
}
```

</details>

11



{% embed url="https://codesandbox.io/p/sandbox/react-dom-router-with-object-routes-qf2366?from-embed=" %}
Router variable Routes with variable components
{% endembed %}

1

1

### The useLocation() hook and properties

The **\<Router>** can contain any **DOM** tag (while \<Routes> can only contain \<Route/>).

```jsx
//Link tags and components work only if rendered inside Router
<Router>
  ...  //Any DOM element here will be fixed in any route
  <Routes>
    <Route path="/primo" element={ <Primo/> } >
      <Route path="primo1" element={ <Secondo/> } />
    </Route>
    <Route path="/primo/secondo/:edit/*" element={ <Terzo/> } />
  </Routes>
</Router>
```

Instead of the deprecated useHistory(), we import the **useLocation()** hook to access the current _location objec_t. It returns the URL <mark style="background-color:blue;">pathname</mark>, a unique route <mark style="background-color:blue;">key</mark>, an indiritation <mark style="background-color:blue;">state</mark>, and both the <mark style="background-color:blue;">search</mark>(?=) and the <mark style="background-color:blue;">hash</mark>(#) in the **URL**.

The **useLocation()** hook updates only if a **router-dom component** is triggered.                                                The _browser_ navigation buttons update the URL using the **history API** and the \<Router/> component tree, but it's not a router event.

```jsx
import {
  useLocation 
} from "react-router-dom";

const location = useLocation();
console.log( location.pathname ) 	//  /primo
```

{% tabs %}
{% tab title="Search prop" %}
The search property returns the **search queryString** from the **URL**.

```jsx
//useLocation().pathname won't return the search query
<Link to="secondo/fine?place=anderville&sort=asc">
  Imperative Route 
</Link>

let posto = useLocation()
console.log( posto.search )  //search: "?place=anderville&sort=asc"
```

The **URLSearchParams** is a built-in javascript **interface** for the query strings in the **URL**, It allows us to access and edit the returned key/value pairs.

```jsx
const queryParams = new URLSearchParams(posto.search);
const someParam = queryParams.get('place');

const [via, setVia] = useState( someParam || 0 )	//andreville
```
{% endtab %}

{% tab title="Hash prop" %}
The **hash value** is separated from the URL by a (#) **fragment identifier**.&#x20;

It updates the **URL without** sending an **HTTP** request to the server nor reloading the browser, used for single-page navigation.

We can use it to log events based on the **anchor ID** the user has been redirected to.

```jsx
//Any new hash replaces the old one in the URL.
//The /primo#parte updated URL will trigger a scroll in the browser
useEffect(()=>{
  console.log("The user has moved")
}, [location.hash])

<div>
  <a href="#parte"> To Section </a>
</div>

<div id="parte"> Scrolled here </div>
```

Hash **sections url** are also used for SEO google search optimitation.

The **\<Link>** won't trigger an ID hash scroll because it doesn't update the URL with a hash, but it replaces the current URL with another one that already includes a hash.

```jsx
//This also applies to external route <Link> with hash.
<Link to="#parte">
  To section
</Link>

<Link to={{ pathname: "secondo/finale", hash: "#parte" }}>
  Hash on imperative navigation
</Link>

//We can use the location.hash to scrollIntoView the ref anchor.
let barra = useRef()

useEffect(() => {
  const hash = posto.hash;

  if (hash && carra.current ) {
    barra.current.scrollIntoView({ behavior: 'smooth' });
  }
}, [posto]);
```
{% endtab %}

{% tab title="state prop" %}
The **state** prop **sends data** between \<Link> routes, It won't persist on reload or route change.         It's easily accessible so it's better to use _localStorage_ and _sessionStorage_ with encryption for user data.

```jsx
//High data might affect performance 
<Link to="secondo/cartoon" state={{valore: "dicinnove", value: 19}}>
  Declarative with state
</Link>

let posto = useLocation()
console.log( posto.state )	//{valore: "dicinnove", value: 19}
```
{% endtab %}

{% tab title="key prop" %}
When a component is mounted by the **\<Router>**, a unique read-only **key** identifier is created based on the current **URL**.

```jsx
//On browser navigation buttons the old key won't be updated
let place = useLocation()
console.log( place.key )
```
{% endtab %}
{% endtabs %}

Unlike **useParams()**, the useLocation() **pathname** includes both the static and dynamic routes.

```jsx
//From, <Route path="/minni/pop/:fnaf/*" element={ <Terzo/> } />

const { pathname } = useLocation();  // /minni/pop/variegato
const variable = useParams();        //{fnaf: 'variegato', *: ''}
```

### The useNavigation() sibling \<Route> and localStorage

The \<Link> handles **imperative redirecting**, while the **useNavigate()** is for **programatical redirecting**.

The **useNavigate()** hook returns a function that redirects the user in response to a variety of events, not just clicking a link, its 2 arguments are the **route path** and the **options objec**t.

The **replace** property resets the URL to the current path and disables the previous route, while **state** is passed and accessed with useLocation().

```jsx
//We can reset the scroll position with <ScrollRestoration> on dataBrowser
//replace is false by default
function giro(){
  viaggio("primo3", {state: {primo: "here"}, replace: true} )
}

<button className="btn btn-primary" onClick={()=> giro()}>
  Programatically redirect
</button>

//on primo3
console.log( useLocation().state )	//{primo: 'here'}
```

A useNavigate() **relative path** will use **history API** to update the URL without updating the useLocation(), similarly to the browser navigation buttons.

```jsx
//Both will work only if the route has been already rendered

let via = useNavigate()
onClick={()=>( via(1) )}
onClick={()=>( via(-1) )}
```

We can also use the relative path as a shortcut between the **sibling \<Route>**.

```jsx
//from the primo/primo1 sibling route, without re-declaring the route
function test(){
  via("../primo2");
}

//Remember that <Outlet/> goes on the parent Route to make the children visible
<Routes>  
  <Route path="/primo" element={ <Primo/> } >
    <Route path="primo1" element={ <Secondo/> } />
    <Route path="primo2" element={ <Quarto/> } />
    <Route path='primo3' element={ <Terzo/> } />
  </Route>
</Routes>
```

The **localStorage** is a **browser-provided** web storage object, accessible by the _client_ and the _server_, persistent across browser sessions and \<Router> routes.

```jsx
//After being set It can be accessed by any route
let forma={ first: 12, second: "badge" }
localStorage.setItem('stato', JSON.stringify(forma));

const storedFormState = localStorage.getItem('stato');
const parsedFormState = storedFormState ? JSON.parse(storedFormState) : null;

//We useState() to modify it more easily
const [formState, setFormState] = useState(parsedFormState || {});

useEffect(() => {
  localStorage.setItem('stato', JSON.stringify(formState));
}, [formState]);

//How we use input to edit the useState() bound to the localStorage()
<input id="cash" type="text" className="form-control" value={formState.first} 
  onChange={(e)=> setFormState((stat)=> ({...stat, first: e.target.value}) )}/>
  
localStorage.removeItem("stato");
```

1

1

1

1

1

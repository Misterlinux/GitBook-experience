# React-Router-Dom Routes

* 1
* 1
* 1
* 1

1

1

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

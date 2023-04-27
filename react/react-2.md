# REACT 2

* 1
* 1
* 1
* 1

We use **Client-side routing** to render **\<Routes> components** without requesting **external documents**.

Routing is not included in react, we need to in import Components from **react-router-dom**.

```
npm i react-router-dom

//We change the name of Browser
import {
  BrowserRouter as Router,
  Link,
  Route,
  useParams,
  Routes,
  Outlet,
  useRouteError
} from "react-router-dom";

```

{% tabs %}
{% tab title="Home route" %}
We render **Router>Routes>Route** in the return, the **element component** is linked to the **URL path**.

We use /\* for the **nested Routes** that will inherit the **parent URL**

```
return(
  <div>
    <Router>

      <Routes>
        <Route path="/" element={<Binge />} errorElement={<Sbaglia />} />
        <Route path="/venere/*" element={<Venere />} />
      </Routes>

    </Router>
  </div>
)

```

The **/** Route is the "**Home**" URL, we use **\<Link>** to **navigate** to the URL PATH,&#x20;

```
//we only need the /(path name) for the TO link.

const Binge = () =>{
  return(
    <div>
      <ul>
        <li> <Link to="/">Home</Link> </li>
        <li> <Link to="/venere">Bergamo</Link> </li>
      </ul>
    </div>
  )
}

```

<figure><img src="../.gitbook/assets/URL.PNG" alt=""><figcaption><p>Home / route</p></figcaption></figure>
{% endtab %}

{% tab title="Nested route" %}
On the route element component, we use a **variable route path**.

The URL PATH **depends on the \<Link>** and inherits the parent URL.

```
//The path will change depending on the Link, and we can link 2 to one route

return(
  <div>
    <Link to="primem" >Click to nuovo or primo? </Link>
    <Link to="secondum" >Click to nuovo or secondum? </Link>

    <Routes>
      <Route path=":nuovo/*" element={<Primo />} />
    </Routes>
  </div>
)

```

<figure><img src="../.gitbook/assets/nested.png" alt=""><figcaption><p>URL path on variables </p></figcaption></figure>

1

1

1
{% endtab %}

{% tab title="Variable route" %}
We cut the /\* from the path, any extra Route won't inherit the PATH URL.

```
//this is done for the ending routes

return(
  <div>
    <Link to="secondo"> To secondo </Link>

    <Routes>
        <Route path=":meaning" element={<Secondo />} />
    </Routes>
  </div>
)

```

<figure><img src="../.gitbook/assets/URL3.PNG" alt=""><figcaption><p>variable URL Path</p></figcaption></figure>
{% endtab %}
{% endtabs %}

1

1

1

1

1

1

1

1

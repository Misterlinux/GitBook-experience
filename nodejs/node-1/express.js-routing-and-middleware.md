# Express.js Routing and Middleware

* 1
* 1
* 1
* 1

A middleware is an **express function** called during route requests, it has access to the _request_ and _response_ **objects**, its **next()** function passes control to the next stack/route handler.

<pre class="language-jsx"><code class="lang-jsx">//It can be included directly in the Route request
function steps(req, res, next){
  console.log("first step")
  next()
}

app.get("/first", <a data-footnote-ref href="#user-content-fn-1">steps</a> ,(req, res)=>{
  res.send("end stack")
})
</code></pre>

The express method **app.use()** mounts the middlewares on the route paths.

{% tabs %}
{% tab title="app.use("/", ())" %}
If no route is specified then it's applied to **every** route, their **order** depends on when were they declared.

```jsx
//Imported middleware will be invoked last
app.use(steps)

//It has to share the same route of the route it is included in
app.use("/means", (req, res, next)=>{
  console.log("This is the second")
  next()
})

app.get("/means", preso, (req, res)=>{
  console.log("This is the return")
  res.send("how does a middleware work")
})
```
{% endtab %}

{% tab title="Array [middleware]" %}
We can include an **array** of middlewares to a single route path.

```jsx
//Invoking them in the array order
function primabase(req, res, next){
  next()
}

function secondabase(req, res, next){
  console.log("Per primo " + req.params.secondo + req.params.primo)
  next()
}

let basi = [secondabase, primabase]
app.get("/linea/:primo/:secondo", basi, (req, res, next)=>{
  console.log("This is the last part")

  res.send("end of the wave")
})
```
{% endtab %}
{% endtabs %}

A middleware can end a route path with **res.send()** and **res.redirect()**.

```jsx
function check(req, res, next){
  req.params.part.length > 5 ? next() : res.redirect("/means")
}

app.get("/take/:part", (req, res)=>{
  res.send("The params is higher than 5")
})
```

Middlewares can update **req.body** and pass **error** parameters to the route handler.

<details>

<summary>Error handling middleware with next(err)</summary>

The **middleware** error handler requires 4 parameters, and will inherit the error object.

```jsx
//even if you don't use next() you need to include it.
app.use((err, req, res, next) => {
  console.error('Error caught by error middleware:', err);
  res.status(500).send('Internal Server Error');
});

function fetchDataFromDatabase() {
  if (Math.random() < 0.5) {
    throw new Error('Database connection failed');
  }
  return [{ id: 1, name: 'John' }, { id: 2, name: 'Jane' }];
}

app.use('/erro', (req, res, next) => {
  try {
    const data = fetchDataFromDatabase();
    req.body = data
    next()
  } catch (err) {
    next(err); // pass the error to the error middleware
  }
});

//It access the updated req.body
app.get("/erro", (req, res, next)=>{
  res.send( req.body )
})
```

This is useful to handle errors before they reach the route handler function.

</details>

We can **module.export** middlewares to the router.

```jsx
//middle.js
function mezzo(req, res, next){
  console.log("middle function")
  next()
}

module.exports = mezzo;

//server.js
let preso = require("./middle")
app.use( preso1 )
```

### Independent routing with express.router()

The **express.router()** class can create independent **routing instances**, that need to be **app.use()** mounted to a **path** of the routing system.

it inherits methods and properties from the express framework (like **middlewares**).

```jsx
//Router.js 
const router = express.Router();

function solo(req, res, next){
  console.log( "added middle on router" )
  next()
}

//path localhost3030/first/ahead
router1.get("/ahead", solo, (req, res)=>{
  console.log( "router handler" )

  res.send("sent")
})

module.exports = router1;

//server.js
let rotta = require("./router")
app.use("/first", rotta)
```

We implement **route chaining** using the **router.route()** method.

```jsx
//Same HTTP route but different methods verbs
app.route('/users')
  .get((req, res) => {
    console.log("We got the get way")
    res.send("Io volevo il film")
  })
  .post((req, res) => {
    res.send("Lo stanno facendo")
  });
```

1

1

1

1

1

1

[^1]: We can also:

    (req, res, next)=>{

    &#x20;  next()

    }

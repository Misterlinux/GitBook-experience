# Express.js Routing and Middleware

* [Independent routing with express.router()](express.js-routing-and-middleware.md#independent-routing-with-express.router)
* [Hashing passwords with bycryptjs](express.js-routing-and-middleware.md#password-hashing-with-bycryptjs)

A middleware is an **express function** called during route requests, it has access to the _request_ and _response_ **objects**, its **next()** function passes control to the next stack/route handler.

<pre class="language-jsx"><code class="lang-jsx">//It can be included directly in the Route request
function steps(req, res, next){
  console.log("first step")
  next()
}

app.get("/first", <a data-footnote-ref href="#user-content-fn-1">steps</a> ,(req, res)=>{
  res.send("end stack")  //first step, end stack
})
</code></pre>

The express method **app.use()** mounts the middlewares on the route paths.

{% tabs %}
{% tab title="app.use("/", ())" %}
If no route is specified then it's applied to **every** route, their **order** depends on when were they declared.

```jsx
//Aplied to all requests routes
app.use(steps)

app.get("/ruota", (req, res)=>{
  console.log("This is the return")  //first step, this is the return
  res.send("how does a middleware work")
})

//This middleware will be ignored.
app.use("/ruota", (req, res, next)=>{
  console.log("This is the second")

  next()
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
//If > 5 res.send, if not it redirects
function check(req, res, next){
  req.params.part.length > 5 ? next() : res.redirect("/means")
}

app.get("/take/:part", check, (req, res)=>{
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

We can **module.export** middlewares to use() in the router.

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

The **express.router()** class create independent routing instances, which need to be mounted to a specific path using **app.use()**, effectively **prefixing** all routes defined within the **router**.

it inherits methods and properties from the express framework (like **middlewares**).

```jsx
//Router stored in routes/router.js 
const router = express.Router();

function solo(req, res, next){
  console.log( "added middle on router" )
  next()
}

//path /localhost3030/first/ahead
router1.get("/ahead", solo, (req, res)=>{
  console.log( req.body )
  res.send("sent")
})

module.exports = router1;

//All previously defined middlewares will be aplied to the mounted router.
app.use(express.json())  //Will parse the router req.body

let rotta = require("./routes/router")
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

### Password hashing with bycryptjs

The **bcrypt** module is a wrapper library for the C-based bcrypt **password hashing algorithm**.                                 It provides a javascript **API** to hash passwords, due to the C compilation, extra dependencies may be required depending on the platform.

The **npm i bcryptjs** module is a pure _JavaScript implementation_ of the bcrypt algorithm, it doesn't require external code and runs directly in Node.js.

Its **getSalt(**&#x72;ound&#x73;**)** method generates a random string used for the **hash()** of the password.

```jsx
//bcryptjs is less performant than bcrypt but they share functions
//Being a drop-in replacement it inherits all the previous methods
//A higher rounds value for a more complex but slower hash.
const bcryptjs = require('bcryptjs');
const bcrypt = require("bcrypt");

const sale = await bcryptjs.genSalt(10)
const cryptopass = await bcryptjs.hash(password, sale)
```

Bcryptjs provides **synchronous** versions of its _promise-based methods,_ which block the execution of the program until they complete and return a value.

<pre class="language-jsx"><code class="lang-jsx"><strong>console.log( bcrypt.genSalt(10))          //Promise { &#x3C;pending> }
</strong>console.log( bcryptjs.genSaltSync(10))    //$2a$...
console.log( await bcrypt.genSalt(10))    //$2a$...
</code></pre>

The **compare()** method compares a _plaintext_ and a _hashed password,_ using its salt, to return a boolean value.

```jsx
//Ther is also compareSync
const newtrue = await bcryptjs.compare( password, hashpassword ) //true/false

//The salt value is visible in the hashed password
$2b$10$q.KYbb.xmNQ0KEikk5EfWenciYtBMJBsfhmPqnf7HTWeTcHnDAI5q

$2b$ is the bcrypt algorithm identifier.
<cost> is the round count, represented as a base-10 integer.
<salt> is the salt value, represented as a base-64 encoded string.
<hash> is the hashed password, represented as a base-64 encoded string.
```

The **getRounds()** method extracts the number of rounds used in the salt process.

```jsx
//the same async and sync methods as for the compare.
bcryptjs.getRounds("$2b$....")
```

We manage its error handling with a **try/catch** block:

<pre class="language-jsx"><code class="lang-jsx">//An 401 error for compare() returning false
//An 500 error for errors in the compare() methods (like invalid arg, etc)
<strong>router.post("/sign-in", async (req, res) => {
</strong>  try{
    const isValidPassword = await bcrypt.compare( password, hashpassword );
    
    if (!isValidPassword) {
      return res.status(401).json({
        error: "Invalid Credential", 
        isAuthenticated: false}
      );
    }
    
  }catch (error) {
    console.error(error.message + " non validated");
    res.status(500).send({error: error.message});
  }
})
</code></pre>

[^1]: We can also:

    (req, res, next)=>{

    &#x20;  next()

    }

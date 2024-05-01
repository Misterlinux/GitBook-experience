# NodeJs, Server request/responses with Postman, CORS, and JWT authentification

* [Modify a local JSON file with Postman POST data](node-1.md#modify-a-json-file-with-postman-post-data)
* [CORS and JWT autentification](node-1.md#cors-and-jwt-autentification)

**NodeJs** is a server-side javascript environment for app development.

We use **ExpressJs**, an open-source NodeJs framework, to handle HTTPS requests with routing support.

The **REST** (Representational State Transfer) **API** exchanges data between applications through **HTTPS methods** like _GET, PUT, POST_, and _DELETE_. **(**Client-> API -> Database -> Database data**).**

To start a **NodeJs server** we import/require **express methods,** each route will include an **endpoint** and its **handler function**:

```jsx
- npm Init         //We start up a package.json file

//The endpoint sets where the request and response is gonna take place
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send("Hello World!");
});

//listen() sets the localhost: endpoint 
app.listen(3000, () => console.log("Server is up and running"))

//node server.js to start the server
```

The **endpoint** is the part of the _URL_ that comes after **/**.

Contrary to _React_, the server needs to re-start to update, to avoid that we **npm i nodemon**.

```jsx
//In the package.json we create a custom script 

"scripts": {
  "start": "nodemon server.js"      //now is npm start
}
```

**Postman** is a scalable testing tool, it can retrieve information sent by the **server routes**.

**Queries** are the url part that comes after a **?...=**, it is used to _pass information_ from the endpoint to the **server routes**.

```jsx
//We request them with req.query.___, and add them to the endpoint
//we add a query with ?(name set in route)=(query value) in the URL

app.get('/', (req, res) => {
  let cava = req.query.v
  res.send("Hello World! , we have the " + cava );
});
```

<figure><img src="../.gitbook/assets/postmanQuery1.png" alt="" width="314"><figcaption><p>Postaman Get method for a URL with query</p></figcaption></figure>

<details>

<summary>Multiple queries for Math operations routes</summary>

To add **multiple queries** to the url we use **&**:

```
http://localhost:3000/add?value1=12&value2=21
```

**Queries values** are strings by default, we convert them for math functions.

```jsx
//We first add the route endpoint and then the query values

app.get("/add", function (req, res) {
    let sum1 = Number( req.query.value1 )
    let sum2 = Number( req.query.value2 )   
    res.send("The result is " + (sum1 + sum2 ));
});
```

</details>

**Parameters** are _properties_ attached to the **URL**, prefixed with **(:)** on the **endpoint**, and requested with **req.params.\_\_\_**.

```jsx
//http://localhost:3000/add1/12/74

//we pass its multiple values in the Endpoint
app.get("/add1/:primo/:second", function (req, res) {
    let sum1 = Number( req.params.primo )
    let sum2 = Number( req.params.second )
    res.send("The result is " + (sum1 + sum2 ));
});
```

<details>

<summary>Middleware use() and redirect() route</summary>

**Middleware** are **functions** called during route calls, any of their request data is included in their route (like an authentification use check at each route).

```jsx
//next() is used to advance in the middleware chain
//depending of the Date() object it will res.send() or redirect() the route

function requestTime(req, res, next){
  req.requestTime = Date.now() 

  const caso = (req.requestTime % 2 == 0) ? next() : res.redirect("/monos")
}
```

We implement it with **use()** or call it in the middle of the route.

```jsx
//If we use() it, it will be included in each route
//or we can call it in specific routes

app.use(requestTime)

app.get("/tempo", requestTime, function (req, res) {
  res.send("The time is " + req.requestTime )
})

app.get("/monos", function(req, res){
  res.send("possiamo vederci altri?")
})
```

</details>

### Modify a JSON file with Postman POST data

To _body-parse request body_ elements we **use()** the **express.json()** built-in _middleware_.                      We install file-system **(fs)** which will allow us to **update server files**.

```jsx
//Unlike normal middleware, it doesn't need to be included in routes
app.use(express.json())

npm i file-system
const fs = require("fs")
```

We **req**uest the **body** from _Postman_ Post and update the imported **JSON** array using **fs**.

```jsx
//we set the new object ID to be the last of the JSON.
const quotes = require("./quotes.json");  //[{}, {}, {}, ...]

app.post("/quotes", function(req, res){
  const corpo = req.body
  corpo.id = quotes.length

  quotes.push( corpo )
  fs.writeFileSync("./quotes.json", JSON.stringify(quotes));

  res.send( "Pushed one" )
})
```

<figure><img src="../.gitbook/assets/Postmanpostsync.png" alt="" width="317"><figcaption><p>Postman POST and updated JSON (with changed ID)</p></figcaption></figure>

Both **Post** and **Put** Postman **methods** can _update and create_ elements, **Put** is _**idempotent**_, its results remain the same not matter how many times it's repeated.

<details>

<summary>PUT and DELETE method update on JSON file </summary>

To **update** the JSON file with the Postman POST body, we **filter** both its **ID** and its **parameter** so it keeps the updated object ID.

```jsx
//Splice() uses the index so we need to lower it

app.put("/quotes/:id", function(req, res){

  const messo= req.body
  let index= Number( req.params.id )

  if( index > quotes.length-1 || messo.id > quotes.length-1 ){
    res.send("object to update not present")
  }else{

    quotes.splice( index-1 , 1, messo )

    fs.writeFileSync("./quotes.json", JSON.stringify(quotes));
    res.send("We updated the Json array")
  }
})
```

To **DELETE** a JSON element by its **ID** parameter, we **filter()** it and use the matching element index to **splice()**.

```jsx
//album returns an array, we [0] to extract its index

app.delete("/quotes/:id", function(req, res){
  let canc = Number( req.params.id )

  let album = quotes.filter((el) => {
    return el.id == canc
  });

  const index = quotes.indexOf(album[0]);
  quotes.splice( index, 1 )
  
  fs.writeFileSync("./quotes.json", JSON.stringify(quotes));
  res.status(200).json({ succes: true });
})
```

</details>

### CORS and JWT autentification

**CORS**, _Cross-Origin Resource Sharing_, allows servers to set specific **origins** for their **request**.           Origins are defined by their **protocol HTTP** and **port** number.

HTTP is a _stateless protocol_, it won't record any request data, so to **authenticate** the user and to share data between the browser and server we used **sessions**.

**Session objects** track users by comparing their **cookies** (string files downloaded on website access) to the ID session. When **users** make a **request** _(log in)_ it checks if they have an already open session and what permits they have.

**Sessions** need storage space and extra security when sent to the server, they make the app harder to scale, and it's hard to implement on apps that contain many back-end micro-services or _don't use the browser for their cookies_.

<figure><img src="../.gitbook/assets/sessiontoken.png" alt=""><figcaption><p>Sessions cookies and session ID </p></figcaption></figure>

The **JSONWebToken** (JWT) _registers_ the user directly **to the app** without any sessions.                       **JSON** stands for _Javascript Object Notation_, a text-based data format transferable between all languages and standard syntax for APIs.

The **JWT** is made of **clains** (string sections) separated by a comma, clains are encoded in <mark style="background-color:blue;">code-64</mark>.

The **first** _header_ clain contains the **hashing algorithm** and the token **type.**                                                   The **second** contains the JSON object sent to the user, visible to anyone.                                                      The **third** is a **secret hash**, kept by the **server** and it resets if the original request changes.

<figure><img src="../.gitbook/assets/JWT.png" alt=""><figcaption><p>JWT token</p></figcaption></figure>

### Implementing JWT registration to the server

On the **server.js** we implement **npm install cors** by setting the allowed **Port** origin.

```jsx
const express = require("express");
const app = express();
app.use(express.json());
const cors = require("cors");

const corsOptions = {
  origin: "http://localhost:3000"    
};
app.use(cors(corsOptions));        //We use() the CORS middleware
```

We _import_ the **routes.js** as _middleware_ for all **/user** endpoint routes.

```jsx
const user = require("./routes/user.js");
app.use("/user", user);
```

In the **routes/user.js** we **npm install bcrypt fs** to _hash the password_ and save it to the _JSON database_.

```jsx
const express = require("express");
const bcrypt = require("bcrypt");   
const fs = require("fs"); 

//The user router comes from the expressJs built-in method
const router = express.Router();   
```

We import both the JSON **database** to update and the JWT **generator**, then we deconstruct the **req**uest **body** for the **/user/sign-up** route (**router** is the middleware of /user endpoint).

If the **Post** **email** is already being in the database we return an error **(400) response**.                             We **bcrypt** the **password** body property and add a random **salt() hash** to it.                                              The _response.send()_ **JWT** is created after the user is registered.

<pre class="language-jsx"><code class="lang-jsx"><strong>const usersDb = require("../database/db.json");
</strong>const generateJWT = require("../utils/generateJWT");

router.post("/sign-up", async (req, res) => {
  const {  name, email, password } = req.body;

  try {
    const user = await usersDb.filter(user => user.email === email);
    if (user.length > 0) {
      return res.status(400).json({error: "User already exist!"});
    }

    const salt = await bcrypt.genSalt(10);
    const bcryptPassword = await bcrypt.hash(password, salt);
    let newUser = {
      id: usersDb.length,
      name: name,
      email: email,
      password: bcryptPassword
    }

    usersDb.push(newUser);
    await fs.writeFileSync('./database/db.json', JSON.stringify(usersDb));
     
    const jwtToken = generateJWT(newUser.id);
    return res.status(201).send({ jwtToken: jwtToken, isAuthenticated: true});

  } catch (error) {
    console.error(error.message);
    res.status(500).send({error: error.message});
  }
});

module.exports = router;    //the router is then exported
</code></pre>

On the **utils/generateJWT.js** we **npm install jsonwebtoken dotenv**.                                                              We access the secret **env file** to generate JWT tokens, for the **payload** object we use public user data, the token is then **sign()** and given an **expiration** date.

```jsx
const jwt = require("jsonwebtoken");
require("dotenv").config();

function generateJWT(user_id) {
  const payload = {
    user: {
      id: user_id
    }
  };

  return jwt.sign(payload, process.env.jwtSecret, { expiresIn: "1h" });
}

module.exports = generateJWT;    //the generate function is then exported
```

<figure><img src="../.gitbook/assets/JWTserver.png" alt=""><figcaption><p>Created Post user in teh JSON database and JWT user response.send()</p></figcaption></figure>

The **env** and **database/db.json** files are:

```jsx
//The external .env file value is used to set variables 
//It's secret so keep it from git commits using .gitignore
jwtSecret = "migracodeAuthJan2021"

//The JSON database is an array for objects elements
[]
```

<details>

<summary>Sign-in endpoint with bcrypt.compare()</summary>

In the **user/sign-in** endpoint, we **bcrypt.compare()** the JSON database password with the **req.post** password.

```jsx
//To sign-in we check if any user has the req.post password
//The JSON database passwords are encrypted, so to compare we de-crypt them.
//It returns a JSW using the matched user ID.

router.post("/sign-in", async (req, res) => {
  const { email, password } = req.body;

  try {
    const user = await usersDb.filter(user => user.email === email);
    if (user.length === 0) {
      return res.status(401).json({
        error: "Invalid Credential", 
        isAuthenticated: false}
      );
    }

    const isValidPassword = await bcrypt.compare(
      password,
      user[0].password
    );
    if (!isValidPassword) {
      return res.status(401).json({
        error: "Invalid Credential", 
        isAuthenticated: false}
      );
    }

    const jwtToken = generateJWT(user[0].id);
    return res.status(200).send({ jwtToken, isAuthenticated: true });

  } catch (error) {
    res.status(500).send({error: error.message});
  }
});
```

</details>

We implement the **authentification** _middleware_ in the **user/auth** endpoint.

```jsx
//if authenticated it res.send() the success code 200
const authenticate = require("../middleware/authentificate.js");

router.post("/auth", authenticate, (req, res) => {
  try {
    res.status(200).send({isAuthenticated: true});
  } catch (error) {
    res.status(500).send({error: error.message, isAuthenticated: false});
  }
});
```

In the **auth** middleware we use the **env** file and the **JWT** token (received during sign-up/sign-in), as an _authorization/bearer_ in the **req.header("authorization")**.&#x20;

We **JWT.verify()** the bearer JWT with the _env.JSWsecret_ string, to find the user assigned to the token.

```jsx
//we require the jwt and dotenv modules
const jwt = require("jsonwebtoken");
require("dotenv").config();

function authenticate (req, res, next) {

  let token = req.header("authorization");

  if (!token) {
    return res.status(403).send({ 
      message: "Authorization denied", 
      isAuthenticated: false }
    );
  }
  token = token.split(" ")[1];

  try {
    const verify = jwt.verify(token, process.env.jwtSecret);
    req.user = verify.user;

    next();
    
  } catch (err) {
    res.status(401).send({ message: "Token is not valid", isAuthenticated: false });
  }
};

module.exports = authenticate; 
```

<figure><img src="../.gitbook/assets/authMiddleware.png" alt="" width="563"><figcaption><p>Sign-in JWT and JWTBearer on Postman with code 200 res.send()</p></figcaption></figure>

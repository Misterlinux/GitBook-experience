# NODE 1

* 1
* 1
* 1
* 1

**NodeJs** is a server-side javascript environment for app development.

We use **ExpressJs**, an open-source NodeJs framework, to handle HTTPS requests with routing support.

The **REST** (Representational State Transfer) **API** exchanges data between applications through **HTTPS methods** like _GET, PUT, POST_, and _DELETE_. **(**Client-> API -> Database -> Database data**).**

To start a **NodeJs server** we import/require **express methods,** each route will include an **endpoint** and its **handler function**:

```
- new Init         //We start up a package.json file

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

Contrary to _React_, the server needs to re-start to update, to avoid that we **npm install nodemon**.

```
//In the package.json we create a custom script 

"scripts": {
  "start": "nodemon server.js"      //now is npm start
}

```

**Postman** is a scalable testing tool, it can retrieve information sent by the **server routes**.

**Queries** are the url part that comes after a **?...=**, it is used to _pass information_ from the endpoint to the **server routes**.

```
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

```
//We first add the route endpoint and then the query values

app.get("/add", function (req, res) {
    let sum1 = Number( req.query.value1 )
    let sum2 = Number( req.query.value2 )   
    res.send("The result is " + (sum1 + sum2 ));
});

```

</details>

**Parameters** are _properties_ attached to the **URL**, prefixed with **(:)** on the **endpoint**, and requested with **req.params.\_\_\_**.

```
http://localhost:3000/add1/12/74

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

```
//next() is used to advance in the middleware chain
//depending of the Date() object it will res.send() or redirect() the route

function requestTime(req, res, next){
  req.requestTime = Date.now() 

  const caso = (req.requestTime % 2 == 0) ? next() : res.redirect("/monos")
}
```

We implement it with **use()** or call it in the middle of the route.

```
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

```
//Unlike normal middleware, it doesn't need to be included in routes
app.use(express.json())

npm i file-system
const fs = require("fs")
```

We **req**uest the **body** from _Postman_ Post and update the imported **JSON** array using **fs**.

```
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

```
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

```
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

1

1

1

1

1

1

1

1

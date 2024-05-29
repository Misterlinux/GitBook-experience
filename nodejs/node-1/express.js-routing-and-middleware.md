# Express.js Routing and Middleware

* 1
* 1
* 1
* 1

1

1

```jsx
// Some code

Siamo parti


```

1

1

1

1

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

1

1

1

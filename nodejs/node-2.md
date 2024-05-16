# NODE 2

* 1
* 1
* 1

The **SSL** (secure socket layer) is a security protocol that establishes an encrypted link between the server and the browser, to keep the data transfered secure.

The nodeJs v17 fixes a security hole of SSL, it requires to update react-scripts on old create-react-app apps.

```jsx
//For this error
"error:0308010C:digital envelope routines::unsupported"

//Update it to the latest
npm i react-scripts@latest
```

The **cross-origin fetch requests**, sent from the **ReactJs** client to the **NodeJs** server.js, may be blocked due to browser security restrictions.&#x20;

We add a **middleware** to allow the **content-type** methods.

```jsx
app.all('*', function(req, res, next) {
  res.header('Access-Control-Allow-Origin','URLs to trust of allow');
  res.header('Access-Control-Allow-Methods','GET, POST, OPTIONS, PUT, PATCH,DELETE');
  res.header('Access-Control-Allow-Headers','Content-Type');
  if ('OPTIONS' == req.method) {
      res.sendStatus(200);
  } else {
    next();
  }
});
```

On the **ReactJs** client we set the **fetch** configuration object for our own **server.js** route. Check [React-3](../react/react-3.md) for more API fetch().

```jsx
//The body has to be a string or a formData, so we JSON.stringify()
let [valore, setValore] = useState({
  nomen: "", cognomen: "", pass: "", logged: false,
})

async function mandato(e){
  e.preventDefault()

  let richiesta = {
    method: "POST",    //For the app.post() route in server.js
    body: JSON.stringify(valore),
    headers: {
      'Content-Type': 'application/json',
      'Accept': 'application/json'
    }
  }
  
  //The client-side awaits the server to respond, then we parse the results as JSON.
  let response = await fetch("http://localhost:3000/aggiungi", richiesta)
  let json = await response.json()
  console.log( json )
}
```

The **server.js** receives and decostructs the **request.body**, we update the postSQL table and we **response.send()** the result.

{% tabs %}
{% tab title="then() pool.query" %}
The response.send() data will be treated as an object, so do not return strings.

<pre class="language-jsx"><code class="lang-jsx"><strong>//check Database-1 for how to connect a postSQL table
</strong><strong>app.post('/aggiungi', (req, res) => {
</strong>  let {nomen, cognomen, pass, logged} = req.body
  let query = `INSERT INTO users (username, email, password, loggedin) 
    VALUES ($1, $2, $3, $4)`
    
  pool
    .query( query, [nomen, cognomen, pass, logged] )
    .then((resultado) => {

      if(resultado){
        res.status(202).send(resultado)
       }else{
        res.status(404).send({error: "re-try"})
      }
    })
    .catch (error) {
      console.error(error)
      res.status(500).send("Internal Server Error")
    }
});
</code></pre>
{% endtab %}

{% tab title="Await pool.query" %}
There is no difference between the 2 methods

```jsx
app.post('/aggiungi', async (req, res) => {
  let {nomen, cognomen, pass, logged} = req.body
  let query = `INSERT INTO users (username, email, password, loggedin) 
    VALUES ($1, $2, $3, $4)`

  try {
    const resultado = await pool.query(query, [nomen, cognomen, pass, logged])

    if (resultado) {
      res.status(202).send(resultado)
    } else {
      res.status(404).send(resultado)
    }
  } catch (error) {
    console.error(error)
    res.status(500).send("Internal Server Error")
  }
});
```
{% endtab %}
{% endtabs %}

On ReactJs we check the response object by using the <mark style="background-color:blue;">status</mark>, <mark style="background-color:blue;">statusText</mark>, and <mark style="background-color:blue;">ok</mark> properties.

```jsx
//Once the promise is furfilled we get the response object
//It can read the res.status() we set in the server
let response = await fetch("http://localhost:3000/aggiungi", richiesta)
response.ok ? console.log( response.status ) : console.log(response.statusText)

//The same for the results
let json = response.json()
json.length ? console.log(json) : console.log("Emptyness status")
```

1

1

1

1

1

1

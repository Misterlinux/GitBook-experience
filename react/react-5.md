# REACT 5

* 1
* 1
* 1

**React events** (onClick) and their **Event handlers** (onClick(()=>())) have access to the **React Event Object**. We use it to access the (event) **type**, DOM **target**, and **clientX, pageX, and screenX** positions.

<details>

<summary>event.key/event.code on React Event Object</summary>

**event.crtlKey** checks if the crtl button was being held during onClick().                                  Different React events will give access to different **event object properties**, like **event.key/event.code** on _onKeyDown() input_ events.                    &#x20;

```
function controlla(e){
  console.log( e )

  console.log( "Client X/Y: " + e.clientX + "/" + e.clientY)
  console.log( "Page X/Y: " + e.pageX + "/"  + e.pageY )
  console.log( "Screen X/Y: " + e.screenX + "/" + e.screenY )
  console.log( "Trigger event: " + e.type )
  console.log( "Was the crtl key used? " + e.ctrlKey )
  console.log( e.target )
  console.log( e.target.className )

  let pressed = e.target.className

  if( pressed.includes("danger") ){
    console.log("danger button")
  }else if( pressed.includes("warning") ){
    console.log("Warning button")
  }else{
    console.log("Success button")
  }
}
```

On DOM we:

```
<div className="text-end my-4">
  <button onClick={controlla} className="btn btn-success">Uno</button>
  <button onClick={controlla} className="btn btn-warning">Due</button>
  <button onClick={controlla} className="btn btn-danger">Tre</button>
</div>
```

</details>

<figure><img src="../.gitbook/assets/eventObject.png" alt="" width="538"><figcaption></figcaption></figure>

1

### Instance methods on useRef() DOM elements.

We **useRef()** an input **current.value** to **append()** it in a JSX tag.

{% tabs %}
{% tab title="useRef().current.value" %}
We need 2 **useRef()**, for the input **ref** and the **append()** DOM target.

```
//We can't append() a JSX object so we need to document.createElement()

const sce= useRef(null)
const paper= useRef(null)

function entra(e){
  e.preventDefault()
  let plot= document.createElement("div")
  plot.innerText= sce.current.value

  paper.current.append(plot)
  sce.current.value= ""
}

```

On the **DOM** the **ref** will store and update the input.value (we **append()** to render it):

```
<form className="row col-6 mb-3" onSubmit={entra}>
  <div className="col-auto">
  	<input ref={sce} type="text" className="form-control"/>
  </div>
  <div className="col-auto">
  	<button className="btn btn-primary">submit</button>
  </div>
</form>

<div className="paper col-6">
  <div ref={paper} className="pt-3 ps-4">

  </div>
</div>
```
{% endtab %}

{% tab title="useState().target.value" %}
**useState() can {**render**}** the **input.target value** without a useRef(), but we need to _value/onChange()_ the input.

```
const [run, setRun] = useState("")
let con= useRef( null )

function late(e){
  e.preventDefault()
  let yuu= document.createElement("p")
  yuu.innerText= run

  con.current.append(yuu)
  setRun("")
}
```

On the **DOM** we:

```
<form className="row col-6 mb-3" onSubmit={late}>
  <div className="col-auto">
      <input type="text" className="form-control"
             value={run} onChange={(e)=>( setRun(e.target.value) )}/>
  </div>
  <div className="col-auto">
      <button className="btn btn-primary">submit</button>
  </div>
</form>

<div className="paper col-6">
  <div ref={con} className="pt-3 ps-4">

  </div>
</div>
```
{% endtab %}
{% endtabs %}

<figure><img src="../.gitbook/assets/refAppend.png" alt="" width="563"><figcaption><p>useRef() append() DOM elements</p></figcaption></figure>

1

1

1

1

1

1

1

1

1


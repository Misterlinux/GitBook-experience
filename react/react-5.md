# REACT 5

* 1
* 1
* 1

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


# React-hook-form

* 1
* 1
* 1

The **React-Hook-Form** is a _form management library_ for React. It minimizes re-renders by leveraging the native **form validation API** and easily integrates with other UI libraries.                                                   It features a simple API structure through the **useForm() hook**, which offers methods for handling forms, it supports both synchronous and asynchronous validation, along with built-in error handling.

The **register()** method from the useForm() hook tracks each **input** name and value in the form state. On submit the **handleSubmit()** function will make the **form data** available to its callback function argument.

We manage multiple independent forms by creating different **instances** of the useForm() methods.

```jsx
//The inputs maintain their built-in attributes
const { register, handleSubmit } = useForm()
const { register: register1, handleSubmit: handleSubmit1} = useForm();

return(
  <div className="m-5">
    <form onSubmit={handleSubmit( ((data)=> console.log(data)) )}>
      <input {...register("prima")} />
      <input type="submit" />
    </form>

    <form onClick={handleSubmit1( ((data)=> console.log(data)) )}>
      <input {...register1("prima")} />
      <input type="submit" />
    </form>
  </div>
)
```

The **useForm() hook** configures the form behavior through its options object properties.

<details>

<summary>useForm() properties</summary>

<mark style="color:orange;">**mode**</mark>: it sets the event that triggers each input validation progress, it influences other properties that depend on validation, such as isValid.

<mark style="color:orange;">**reValidateMode**</mark>: It alters the validation triggers following a submission with errors, it specifies which events prompt the re-evaluation of input values and their associated errors.

<mark style="color:orange;">defaultValues</mark>: Its sets the initial values for each input field. if set to undefined it will conflict with the form state.

<mark style="color:orange;">values</mark>: Similar to defaultValues, it sets the initial values after the defaultValues and allows us to edit the field values outside the input change.

<mark style="color:orange;">errors</mark>: Similar to values, the errors prop allows us to set an initial error object, which can be updated independently of the input validation rules.

<mark style="color:orange;">resetOptions</mark>: It seems to only work on teh reset() option.

<mark style="color:orange;">criteriaMode</mark>: It determines how validation errors are handled; by default, only the first error is recorded, but we can configure it to include all returned errors.

<mark style="color:orange;">shouldFocusError</mark>: By default it will focus on the input field that has an error. If set to false, it will not trigger any focus on errors. This feature works with inputs that have linked refs.

<mark style="color:orange;">delayError</mark>: It delays the rendering of errors returned by the inputs based on the validation trigger event

<mark style="color:orange;">shouldUnregister</mark>: It unregisters any unmounted input from the form state and overrides any similar configurations at the child level.

<mark style="color:orange;">shouldUseNativeValidation</mark>: Set to false by default, this option enables the browser's native validation and allows the use of the _:valid_ and _:invalid_ CSS pseudo-selectors.

<mark style="color:orange;">disable</mark>: It disables all input fields in the form and can be used to prevent user interaction during an asynchronous event.

<mark style="color:orange;">resolver</mark>: This function allows you to integrate any validation library with the useForm() validation process.

</details>

```jsx
//External values can be used on the useForm() props values
let [valori, setValori] = useState({})
let [sbagli, setSbagli] = useState({})

const {
  register, handleSubmit, formState: { errors, isValid },
} = useForm({
  mode: "onSubmit"            //onChange | onBlur | onTouched | all 
  reValidateMode: "onChange"  //onBlur | onSubmit = 'onChange'
  defaultValues: async () => {
    let prezzo = await fetch("https://api.coindesk.com/v1/bpi/currentprice.json")
    let risultato = await prezzo.json()

    return {
      primo: "unico",
      secondo: risultato.chartName
    }
  },
  criteriaMode. "firstError"	// "all"
  values: valori,
  errors: sbagli,
  criteriaMode: "firstError"	//"all"
  shouldFocusError: true,
  delayError: 0,
  shouldUnregister: true,
  disable: false
})
```

We edit useForm() properties using their assigned state values.

```jsx
//Destruct the actual errors object and not the returned state
<button onClick={()=> setValori(()=> ({primo: "new", secondo: "values"}) )}>
  Set input values
</button>

<button onClick={()=> setSbagli((erro)=> ({...errors, secondo: "new error"}))>
  Add error
</button>
```

The **register()** function, returned by **useForm()**, registers a field into the form state.                                   It returns an object that, when destructured within an \<input> element, provides all the necessary **props** and **event handlers** from the useForm() hook.                                                                                            The input's **name** is used as an argument for the **register** function to link the input to the form state. We can also include a configuration **options objec**t as an argument to the register function.

The **register()** function can be destructured outside of the input tag.                                                              Any function returned by the register event handler needs to be called on each native input event to ensure that the updated values are available in the form state.

```jsx
//The input needs the same name and ref as the destructed register 
const { register, handleSubmit } = useForm()
const {onChange: cambio, ref: ref2} = register("secondo")

function doppio(event){
  //custom and original onChange
  console.log(event.target.value) 
  cambio(event)
}

const valori = (data) => {console.log(data)}

<form onSubmit={handleSubmit(valori)}>
  <input {...register("primo", {onChange: ()=>{console.log("pressed")} })}/>
  <input name="secondo" ref={ref2} onChange={doppio}/>
  <input type="submit" />
</form> 
```

By using specific naming conventions, we can create **object structures** based on the input **names**.

```jsx
//If a number is used, it will be treated as an element of an array.
<form>
  <input {...register("primo.uno")}/>
  <input {...register("secondo.2")}/>
<form/>

{ primo: {uno: "valore"}, secondo: [, , "valore"] }
```

The register options object includes all **built-in** validation rules.                                                                             The **onBlur()** event handler triggers each time the user focuses away from the input.

```jsx
//Use the useForm() value prop to set input values
const [luce, setLuce] = useState(0)

<input {...register("primo", {
  required: true, minLength: 5, maxLength: 10, pattern: /^[a-zA-Z]+$/,
  value: luce,
  onBlur: (event) => {
    console.log("Past input:", event.target.value);
  },
})}/>
```

1

1

1

1

1

1

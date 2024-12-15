# React-Hook-form 3: Controller, useController(), useformState() and form Context.

* [The \<Controller> input component.](react-hook-form-3-controller-usecontroller-useformstate-and-form-context..md#the-less-than-controller-greater-than-input-component)
* [The useController() custom hook.](react-hook-form-3-controller-usecontroller-useformstate-and-form-context..md#the-usecontroller-custom-hook)
* [The useFormContext() and \<FormProvider> component.](react-hook-form-3-controller-usecontroller-useformstate-and-form-context..md#the-useformcontext-and-less-than-formprovider-greater-than-component)
* [The useFormState() custom hook.](react-hook-form-3-controller-usecontroller-useformstate-and-form-context..md#the-useformstate-custom-hook)

The **handleSubmit()** method accepts _two_ argument functions: the first handles successful form submission, while the second handles failed submission. It returns the f**orm data** and the **formState.errors** object, respectively.

```jsx
//All validate will be triggered on submit.
const {register, handleSubmit} = useForm()

const valori = (data) => {console.log( "formData:", data )}
const sbagli = (errori) => {console.log( "Returned validate errors:", errori )}

<form onSubmit={handleSubmit(valori, sbagli)}>
  <input {...register("primo", {
    validate: (value)=>{ return value.length > 5 && "Validate error" }
  })}/>
  <input {...register("secondo", {
    maxLength: { value: 4, message: "Built-in error" }
  })}/>
</form>
```

Any additional errors returned in the callback functions will not be detected and will require a separate error handling.

```jsx
//We can use async argument functions and try/catch blocks
const valori = async (data) => {
  console.log( data )

  try{
    let prezzo = await fetch("https://api.coindesk.com/v1/bpi/currentprice.json")

    if (prezzo.ok) {
      throw new Error('Network response was not ok');
    }
    let risultato = await prezzo.json()
    console.log( risultato.chartName )
  }catch(error){
    console.error('Error during form submission:', error);
  }
}
```

### The \<Controller> input component.

The **\<Controller/>** is a higher-order component that wraps and **custom registers** an input component. Its properties, such as **name**, **defaultValue**, **shouldUnregister**. and **rules**, manage the **render** input registration.&#x20;

The **control** prop requires the control object from the **useForm()** hook, which binds the rendered component to the form and provides the necessary methods and properties to the **render component**.

```jsx
//The Controller props work the same way as the {...register} props
//The useForm() default will replace any Controller defaultValue.
import { Controller } from "react-hook-form"
const { control } = useForm({
  defaultValues: { primo: 'Initial Value', controlled: "Set value" }
})

<form>
  <Controller 
    render={({ field }) => (
        <div> <input {...field} /> </div>
      )
    }
    name="controlled"
    control={control}
    defaultValue="not counted"
    shouldUnregister={true}
    rules={{
      required: "Controller error"
    }}
  />

  <input type="submit"/>
</form>
```

The **render** property renders the \<Controller/> component, its props contains the **fields**, **fieldState**, and **formState** objects needed to register, validate, and update the input within the form's state management system.

```jsx
//We mostly destruct just fields for single registers
render={( props )} => {}
render={({ fields, fieldState, formState }) => {}
```

Their properties work the same as they do in an useForm() input:

> * **fields**: It handles the registration of the input element and their associated event handlers. Its properties are onChange, onBlur, value, disabled, ref, and name.
> * **fieldState**: It provides important metadata about the current input field and its validation state. Its properties include invalid, isTouched, isDirty, and error.
> * **formState**: It shares the formState properties from useForm().

The **\<Controller/>** attributes can set the **render** component props, like **defaultValue** being used to specify the initial **value** of the input field.

The **\<Controller/>** integrates components without needing to access their **ref**, like with components built without React, such as Material-UI and Ant Design.

{% tabs %}
{% tab title="Controller render" %}
The useForm() formState properties can be used within render.

```jsx
//Remember to no repeat the properties names.
const { register, control, formState: { errors }} = useForm()

<form onSubmit={handleSubmit(valori)}>
  <input {...register("primo", { required: "External error" })}/>

  <Controller
    render={({
    	field: {value, onChange, ...fields}, 
    	fieldState: {error}, 
    	formState: {errors: errori} 
      }) => {
      
      return (
        <div>
          <input
            {...fields} value={value} disabled={false}
            onChange={(e)=>{ onChange( e.target.value ) }}
          />
          {Object.entries(errori).length !== 0 && "Any errors on form"}
          {error && "Error on the field" }
        </div>
      )}
    }
    ...
  />
</form>
```
{% endtab %}

{% tab title="Controller render component" %}
A **render** \<component> should be defined outside the function containing the **\<Controller>**, so to prevent the input from being reset and re-rendered during form event handlers.

```jsx
//Any input onChange or submit reset the component if not external.
function Separato({props: {field: {value, onChange, ...field}}}){

  return(
    <div>
      <input 
        {...field} value={value} disabled={false}
        onChange={(e)=>{ onChange(e.target.value)}}
      />
    </div>
  )
}

function Esempio(){
  const {register, control, formState: { errors }} = useForm()

  return(
    <div>
      <form>
        <Controller
          name="controlled" control={control}
          render={(props) => {
            return <Separato props={props} />
          }}
        />

        <input type="submit" />
      </form>
    </div>
  )
}
```
{% endtab %}

{% tab title="Third-party <Select> component" %}
The field render property works on third party components.

```jsx
//Object defaultValues for Select type inputs.
import Select from "react-select";

const { register, control, formState: { errors }} = useForm({
  defaultValues: {
    seletto: { value: "strawberry", label: "Strawberry" }
  }
})

<Controller
  name="seletto" control={control}
  render={({field})=>(
    <Select 
      {...field}
      onChange={ (event)=> {field.onChange(event) }}
      options={[
        { value: "chocolate", label: "Chocolate" },
        { value: "strawberry", label: "Strawberry" },
        { value: "vanilla", label: "Vanilla" },
      ]}
    />
  )}
/>
```
{% endtab %}
{% endtabs %}

### The useController() custom hook.

The **useController()** custom hook can create _multiple and reusable_ controller inputs.                                     It returns the **render props** of the **\<Controller>** while using its arguments, it requires the useForm() **control** object.

```jsx
//It can be used for components inside or outside the form function 
//Remember to set a defaultValue to avoid the uncontroller/controlled error.
const Sottoforma = ({control1, name}) =>{
  const { field, fieldState } = useController({ 
    control: control1, rules: { required: true },
    name, defaultValue: "insert",
  });

  const { field: campo } = useController({
    control: control1, name: "secondo", defaultValue: "quid"
  })

  return(
    <div>
      <input {...field} value={field.value}
        onChange={(e)=>{ field.onChange(e.target.value) }}
      />

      <input {...campo} onChange={(e)=>{ campo.onChange(e.target.value) }}/>
      <p> {fieldState.isDirty && "Only this element has been modyfied"} </p>
    </div>
  )
}

const { register, control } = useForm()

return( 
  <div>
    <form>
      <input {...register("primo")} />
      <Sottoforma control1={control} name="used" />
    </form>
  </div>
)
```

### The useFormContext() and \<FormProvider> component.

The **useFormContext()** hook accesses the **useForm() context** provided by the **\<FormProvider>**, without passing the properties trought _component props_.                                                                                                              It enables child components to subscribe to methods and properties from the form context.

```jsx
//Often used in nested forms, it requires all useForm() methods
//At the base level we extract the useForm() props from methods.
//If rendering formState.props set the component outside the form function component.
function Costume(){
  let {formState, register} = useFormContext()
  
  return(
    <div>
      <input {...register("sauce1")}/>
      {formState.isDirty && "The thing has been dirtied" }
    </div>
  )
}

const methods = useForm({
  defaultValues: { primo: 'Initial Value', sauce: "contexto value"}
})

const valori = (data) => {console.log( data )}

return(
  <FormProvider {...methods}>
  <div>
    <form onSubmit={methods.handleSubmit(valori)}>
      <input {...methods.register("primo", { required: "The error state" })}/>
      <Costume />
    </form>
  </div>
  </FormProvider>
)
```

We can create **\<Controller>** and **useController()** inputs within the nested components, using the **context** control prop.

```jsx
//We access the register/formState from the controller.
let {control} = useFormContext()

let {field, formState} = useController({
  control: control, name: "sauce1"
})

return(
  <div>
    <input {...field}/>
    {formState.isDirty && "The thing has been dirtied" }
  </div>
)
```

### The useFormState() custom hook.

The **useFormState()** custom hook subscribes and provides access to the **formState** properties from outside the \<form> component.                                                                                                                                                It optimizes re-renders based on the **name** input or it destructed _formState properties_.

<details>

<summary>The formState properties</summary>

* **control**: It subscribes to the formState using the control useForm() object.
* **name**: optional, it links the formState property to the **input**, only works for limited properties like _dirtyFields_ and _touchedFields_, covers the entire formState form if absent.
* **disabled**: boolean, it can disable the useFormState() subscription.
* **exact**: boolean, limites the name property only to the exact input names.

We can render the name input in the same component as its useFormState().

```jsx
//Instead of just extracting teh formState from form context
function Custom1(){
  let {control, register} = useFormContext()

  let { errors } = useFormState({
    control,
    name: "sbaglio"
  })

  return(
    <div>
      <input {...register("sbaglio", {
        validate: (value)=>{
          return (value.length < 3) && "Input component error"
        }
      })}/>
    </div>
  )
}
```

</details>

The returned properties are the same as the **formState** useForm().

```jsx
//We can access formState from external components without re renders.
//Its formState properties need to be destructed due to being wrapped by proxies.
function Custom(){
  let {control} = useFormContext()
  let { isDirty } = useFormState({ control })

  return(
    <div> <p>{isDirty && "Triggered on form isDirty"}</p> </div>
  )
}

const methods = useForm({
  criteriaMode: "all", mode: "onChange",
  defaultValues: { primo: 'Initial Value', sbaglio: "shorte" }
})

return(
  <FormProvider {...methods}>
    <form>
      <input {...methods.register("primo", {required: "Error primo"})}/>

      <Custom />
      <Custom1/>
    </form>
  </FormProvider>
)
```

# Page

* 1
* 1
* 1
* 1

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

1

1

1

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
1

```jsx
//We can also direclty use the formState useForm() props
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
            {...fields}
            onChange={(e)=>{
              onChange( e.target.value )
            }}
            value={value}
            disabled={false}
          />
          {Object.entries(errori).length !== 0 && "Any errors on form"}
          {error && "Error on the field" }
        </div>
      )}
    }
    ...
  />

  <input type="submit"/>
</form>
```
{% endtab %}

{% tab title="Controller render component" %}
1

```jsx
// Some code



```

1

1

1

1
{% endtab %}

{% tab title="Third-party <Select> component" %}
1

```jsx
//
import Select from "react-select";

const { register, control, formState: { errors }} = useForm({
  defaultValues: {
    seletto: { value: "strawberry", label: "Strawberry" }
  }
})

<Controller
  name="seletto"
  control={control}
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

1

1

1

1
{% endtab %}
{% endtabs %}

1

1

1

1

1

1

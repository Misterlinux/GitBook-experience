# React-hook-form 2

* 1
* 1
* 1
* 1

The **reset()** method resets the form data with the useForm() defaultValues. It can update the input fields and configure the reset process using its _options object_.

<details>

<summary>Reset useForm() properties, options object properties.</summary>

* **keepErrors**: boolean, It keeps the useForm() errors object, allowing it to persist even if the new reset values are valid, the error won't interfere with the submission.
* **keepDirty**: boolean, contrary to the default reset() It won't reset the isDirty.
* **keepTouched**: boolean, it won't reset the isTouched value.
* **keepValues**: boolean, It will reset all form state properties _except_ for the input values.
* **keepDefaultValues**: boolean, by default each reset() will reset the **defaultValues** with its own value object. This prop keeps the useForm() defaultValues for subsequent resets.
* **keepIsSubmitted**: boolean, it won't trigger the submit event, but it will count the reset event as a submit for properties like submitCount and isSubmitSuccessful.
* **keepSubmitCount**: boolean, it won't reset the submit count.
* **keepIsValid**: boolean, it keeps the isValid formState value regardless of the new reset values, but a re render might invalidate it.

</details>

```jsx
//The option object requires a value, undefined if we reset defaultValues.
const {
  register, reset, 
  formState: { errors, isDirty, submitCount, isValid, isSubmitSuccessful}
} = useForm({
  defaultValues: { primo: 'uno', secondo: "due" }
})

function cambio(){
  reset();
  reset(undefined, {keepValues: true);

  reset({
    primo: "uno", secondo: "duolandia"
  }, {
    keepDefaultValues: true,
  });
}

<form>
  <input {...register("primo")}/>
  <input {...register("secondo")}/>
</form>
```

We **useEffect()** to manage a submit event followed by a reset, if reset() is invoked before the useForm() submit it might interfere with the submitted values during the batching process.

```jsx
//It will reset only after the form data is submitted.
useEffect(() => {

  if( isSubmitSuccessful ){
    reset({
      primo: "Ritenta sarai",
    })
  }
}, [isSubmitSuccessful])
```

The **resetField()** method resets a specific input value along with its properties. Its option object includes the **keepError**, **keepDirty**, and **keepTouched** properties.

The **defaultValue** property sets a new initial value for the input, which will override the useForm() defaultValues for subsequent reset() on that specific input.

```jsx
//resetFields() only accepts singular inputs
const { register, handleSubmit, resetField } = useForm({
  defaultValues: {
    primo: 'Initial Value',
  }
})

function resetto(){
  resetField("primo", {
    defaultValue: "new initial"
  })
}

<form>
  <input {...register("primo")}/>
</form>
```

The **setErrors()** method updates the errors object outside of the input validation rules, it won't affect the submit event.

It assigns an error to its input, along with an object that contains its error type and message. Its option object can include the **shouldFocus** property, which will focus the input field where the error is set. This property doesn't work on Controller inputs.

An input can store one setErrors(), any additional error will overwrite the previous one. If set for an unexistent input, it will remain after submit and will require a **clearError()**.

```jsx
//The shouldFDocus doesn't work on a disabled input and it affects the isValid prop 
//type and types are different properties for errors
const { 
  register, setError, clearErrors, formState: { errors }
} = useForm({
  criteriaMode: "all",
  defaultValues: {
    primo: 'Initial Value',
  }
})

function togli2(){
  setError("primo", {
    type: "server", message: "We got the error here Terzo"
  }, { shouldFocus: true })
}

<form>
  <input {...register("primo", {
    minLength:{ value: 4, message: "Its too short" }
  })}/>
</form>
```

The validate errors and setError() will occupy different positions in the **errors** object.

```jsx
//A single type for setError() and types for the validate
primo:{
  message: "We got the error here Terzo"
  ref: input
  type: "server"
  types: {
    minLength: 'Its too short'
  }
} 
```

The **clearErrors()** method will clear the **errors** formState property, but it won't skip the validations rules during its submit event.

```jsx
//It will affect formState properties like isValid
const { register, clearErrors, formState: { errors } } = useForm({
  criteriaMode: "all",
})

//The input argument will include any nested input
function togli3(){
  clearErrors()
  clearErrors("primo")	//will clear the "primo.uno", "primo.due" inputs
  clearErrors(["secondo", "terzo"])
}

<form>
  <input {...register("primo", { required: "set input" })}/>
</form>
```

The **setValue()** method _programatically_ sets values on a single registered input, for a multi-input change use reset().

A programmatic change is considered different from an user initialized change, it won't trigger a re-render or the formDate validation properties by default.

<details>

<summary>The setValue() properties.</summary>

Its object argument includes:

* **shouldDirty**: boolean, if true it will update the isDirty on update.
* **shouldTouched**: boolean, same as shouldDirty, refers to isTouched.
* **shouldValidate**: boolean, if true, it triggers the validation rules on update. It will also update the isValid, errors, etc.

</details>

On nested input values, update the nested name instead of the parent. Do not use setValue() on fieldArray elements.

```jsx
//The setValue(target, value) won't accept an undefined argument.
const { register, setValue, formState: {errors, isDirty, isValid}} = useForm({
  defaultValues: {
    primo: 'Initial Value', strato:{ sotto: "deepen" }
  }
})

//We can update using a state, but a mroe focused target is more performant.
function setter(){
  setValue("primo", "aggiunto", {
    shouldDirty: true, shouldValidate: true,
  })

  setValue('strato', { sotto: 'nuovo sottoTerra' } );	//less performant
  setValue( "strato.sotto", "new deepen" )		//more performant
}

<form onSubmit={handleSubmit(valori)}>
  <input {...register("primo")}/>
  <input {...register("strato.sotto")} />
</form>
```

1

1

1

1

1

1

1

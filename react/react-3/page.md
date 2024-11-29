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

1

1

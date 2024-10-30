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

1

1

1

1

1

1

1

1

1

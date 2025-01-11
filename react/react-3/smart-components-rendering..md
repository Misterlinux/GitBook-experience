# Smart components rendering.

* 1
* 1
* 1
* 1

The \<FormProvider> makes the **useForm() context** available to nested components.                                       The \<ConnectForm> component simplifies access to the form context. It retrieves methods from useFormContext() and passes them as **props** to its child **render function**, which uses them to register() input elements with the form.

```jsx
//The <ConnectForm> children render prop function will receive
//Its {...methods} argument from the parent
const ConnectForm = ({ children }) => {
  const methods = useFormContext()
  return children({ ...methods })
}

const DeepNest = () => (
  <div>
  <ConnectForm>
    {({ register }) => (
      <div>
        <input {...register("nested1")} placeholder="Input 1" />
        <input {...register("nested2")} placeholder="Input 2" />
      </div>
    )}
  </ConnectForm>
  <Primo />
  <Secondo />
  <div>
)

function Testa(){
  const methods = useForm()

  return(
    <FormProvider {...methods}>
      <form>
        <DeepNest />
        <input type="submit" />
      </form>
    </FormProvider>
  )
}
```

<details>

<summary>Nested input components in the render prop</summary>

The \<ConnectForm> component is re-usable and can allow different useForm() methods to be extracted and used.

```jsx
//The sibling components to <ConnectForm>
function Primo(){
  return(
    <div>
      <ConnectForm>
        {({register, watch}) =>{
          let valor = watch("additional")
          return (
            <div>
              <input {...register("additional")} placeholder='more'/>
            </div>
          )
        }}
      </ConnectForm>
    </div>
  )
}
```

Which is symilar of what we could do with the basic useFormContext().

```jsx
//Without the render prop the watch() forces a re-render onChange()
function Secondo(){
  let {register, control} = useFormContext()
  let riletto = useWatch({
    name: "rile",
    control
  })

  return(
    <div>
      <input {...register("rile")} placeholder='substitute'/>
    </div>
  )
}
```

</details>

### Builindg reusable useForm() with modular components.

A **form composition** implements the useForm() hook by leveraging its input function components.         It automatically renders and **registers** complex forms by iterating over the **React child elements**.

```jsx
//Basic form passing Input form components and props
function Esempio(){
  const onSubmit = (data) => console.log(data);

  return (
    <>
      <h1>Self rendering Form Components</h1>
      <Form onSubmit={onSubmit} defaultValues={{ 
        defaultValues: {firstName: "valori" }, 
        criteriaMove: "all"
      }}>
        <Input name="firstName" inputRules={{required: true}} />
        <Input name="lastName" />
        <Select name="sex" options={["female", "male"]} />

        <button>Submit</button>
      </Form>
    </>
  );  
}
```

The **parent <**&#x46;orm> component receives the _useForm() options_ and the _onSubmit() function_ as explicit **props**, while its child elements are implicitly passed via the **children prop**.                                                                    We iterate over these child elements, **registering** only the input components that have a name prop, leaving other child elements unchanged.

```jsx
//The {function expression} also checks if the children are iterables array
//We include a key prop due to the input being rendered as a list
//We destruct to the child.props to create a destructuble object
function Form({ defaultValues, children, onSubmit }) {
  const { handleSubmit, register } = useForm( defaultValues );

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      {Array.isArray(children)
        ? children.map((child) => {
            return child.props.name
              ? React.createElement(child.type, {
                  ...{
                    ...child.props,
                    register,
                    key: child.props.name
                  }
                })
              : child;
          })
        : children}
    </form>
  );
}
```

The child's **type** (its corresponding **component** function) and its **props** object are passed to createElement(). The rendered input's configuration props are created by **merging** the useForm() methods with the child's existing props. &#x20;

This enables the **dynamic rendering** of form inputs, each with a specific type, and registered based on its props.

```jsx
//Specific input components types have their own props
function Input({ register, name, ...rest }) {
  return <input {...register(name)} {...rest} />;
}

function Select({ register, options, name, ...rest }) {
  return (
    <select {...register(name)} {...rest}>
      {options.map((value) => (
        <option value={value} key={value}>{value}</option>
      ))}
    </select>
  );
}
```

<details>

<summary>Render prop and input component differences</summary>

The examples above illustrate two different approaches to creating modular forms: the **render prop pattern** and the use of **function components**.

In the **render prop** pattern, a function is passed as the children prop.  Unlike modular components that directly return JSX, the render prop pattern **delegates rendering to its prop** function, with the parent controlling the output via the arguments it passes.

```jsx
//The render prop is rendered on the parent tag invokation.
const ConnectForm = ({ children }) => {
  const methods = useFormContext()
  return children({ ...methods })
}

const DeepNest = () => (
  <ConnectForm>
    {({ register }) => (
      <> <input {...register("nested1")} placeholder="Input 1" /> </>
    )}
  </ConnectForm>
)
```

In a **modular form**, the child component **renders** the JSX, unlike the render prop pattern where the parent component controls the rendering function.

```jsx
//The prop name needs to be declared to destruct it.
function Messo({ prop: {register, name} }){

  return(
    <div>
      <input {...register(name)} />
    </div>
  )
}

function Ultimate(){
  const {register, handleSubmit} = useForm()
  const mostra = (data) => console.log( "returned", data )

  return(
    <div>
      <h5> Accidenti </h5>
      <form onSubmit={handleSubmit(mostra)}>
        <Messo prop={{register, name: "manolo"}} />

        <input type="submit" />
      </form>
    </div>
  )
}
```

</details>

1

1

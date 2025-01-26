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
  <Primo /> <Secondo />
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
//The render prop is rendered on the parent tag invocation.
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

### Yup validation

React-Hook-Form can integrate external **validation libraries**, like **Yup**.

It uses **declarative** objects, such as **validation schemas**, to define the valid data structure instead of the **inline** validation rules.                                                                                                                                                           We use the useForm({}) **resolver option** to integrate the Yup validation.

```jsx
//The strings are the input fomrState error message
const validationSchema = yup.object({
  firstName: yup.string().required("error one"),
  lastName: yup.string().required("error two"),
})

function Terzo() {
  const resolver = useYupValidationResolver(validationSchema)
  const { handleSubmit, register } = useForm({ resolver })

  const fatto = (dati) => console.log( dati )
  return(
    <div className="m-5">
      <form onSubmit={handleSubmit(fatto)}>
        <input {...register("firstName")} />
        <input {...register("lastName")} />
        <input type="submit" />
      </form>
    </div>
  )
}
```

The **resolver** function is typically implemented as a useCallback() function within a custom hook, with the schema as a **dependency** to prevent unnecessary re-validations.

This function **validates** the form data against its schema upon **submission**.                                                                  If validation succeeds, it returns an object containing the **form data** and an empty **errors** object, which then triggers the handleSubmit function.                                                                                                                           If validation fails, the resolver converts the Yup errors to update the **formState.errors** object.

```jsx
//The triggers before the handlesubmit
const useYupValidationResolver = (validationSchema) =>
  useCallback(
    async (data) => {
      try {
        const values = await validationSchema.validate(data, {
          abortEarly: false,
        })
        console.log( "Only if no-error", values )
        return {
          values, errors: {},
        }
      } catch (errors) {
        return {
          values: {},
          errors: errors.inner.reduce(
            (allErrors, currentError) => ({
              ...allErrors,
              [currentError.path]: {
                type: currentError.type ?? "validation",
                message: currentError.message,
              },
            }), {}
          ),
        }
      }
    },
    [validationSchema]
  )
```

<details>

<summary>Switching yup schemas on a form</summary>

A change to the validation schema triggers the useCallback(), creating a new **validation instanc**e. While a form submission triggers only the resolver, not the useCallback() itself.

```jsx
//The memoized component will re render on schema update
const useYupValidationResolver = (validationSchema) => {
  console.log( "Triggered on submit" )

  return useCallback(
    async (data) => {
      console.log( "only triggered on changed schema" )
      try {
      	...
      } catch (errors) {
      	...
      }
    },
    [validationSchema]
  )
}

const MyReusableForm = memo(({ validationSchema, onSubmit }) => {
  const resolver = useYupValidationResolver(validationSchema);
  const { handleSubmit, register, formState } = useForm({ resolver });

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("name")} />
      <input {...register("email")} />
      <button type="submit">Submit</button>
    </form>
  );
});

//Schema from name required() to email format + email required
function Terzo() {
  const schema1 = yup.object({ name: yup.string().required() });
  const schema2 = yup.object({ email: yup.string().email().required() });
  const [useSchema1, setUseSchema1] = useState(true);

  return (
    <div>
      <MyReusableForm validationSchema={useSchema1 ? schema1 : schema2} 
        onSubmit={(data) => console.log("Form 1 submitted", data)} />
        <button onClick={()=>setUseSchema1(!useSchema1)}>
          change schema
        </button>
    </div>
  );
}
```

</details>

### React-window done

The **React-Window** library optimizes the rendering of large **lists** of data through **virtualization**.

It renders only the items visible within the **viewport**, minimizing DOM updates. It mantains the complete **dataset** in a state, like **useForm()**, independent of visibility.

The react-window **\<VariableSizeList>** component virtualizes the rendering of inputs from a dataset. The **viewport** size can be set directly or determined automatically using the <**AutoSizer>** parent component, which provides the available _height_ and _width_ via a render prop.

The \<List> component renders each individual **item** using a child **function component**, which receives the item's index, a style object for positioning, and the data array passed as itemData, as key props.

```jsx
//itemCount sets the scroll area based on the size and count of all items.
//The index top style sets the spce between inputs.
import { VariableSizeList as List } from 'react-window';
import AutoSizer from 'react-virtualized-auto-sizer';

const WindowedRow = React.memo(({ index, style, data }) => {
  const { register } = useFormContext()
  const qtyKey = `${index}.quantity`

  return (
    <div style={{ ...style, color: "red", top: `calc(${style.top + 20}px)` }}>
      <label>{data[index].title}</label>
      <input {...register(qtyKey)} />
    </div>
  )
})

<AutoSizer>
  {({ height, width }) => (
    <List
      height={height} width={width}
      itemCount={items.length} itemSize={() => 80}
      itemData={items}
    >
      {WindowedRow}
    </List>
  )}
</AutoSizer>
```

The **style** prop, specific to each rendered item, can be destructured for further customization and defaults to position: _absolute and left: 0_.

Inputs registered in the \<List> function component are **stored independently** in the formState.                The virtualized mounting and unmounting of these inputs doesn't affect their current data.

```jsx
//The page content gets displayed as a flex-column and text-center
//The Form container keeps the column display with a align-center for the List 
//The List container sets its width and flex-grow-1 to occupy the avaiable space
//We set an array of objects to render as a virtualized list
import { FixedSizeList, VariableSizeList as List } from 'react-window';
import AutoSizer from 'react-virtualized-auto-sizer';

const items = Array.from(Array(1000).keys()).map((i) => ({
  title: `List ${i}`,
  quantity: Math.floor(Math.random() * 10),
}))

const WindowedRow = React.memo(({ index, style, data }) => {...})

function Terzo() {
  const formMethods = useForm({ defaultValues: items })
  return(
    <div className="d-flex flex-column text-center" style={{height: "50vh"}}>
      <h1>Using with React-window</h1>

      <form className="d-flex flex-column flex-grow-1 align-items-center">
        <div className="d-flex flex-grow-1 w-50 py-2">
          <FormProvider {...formMethods}>
            <AutoSizer>
              ...
            </AutoSizer>
          </FormProvider>
        </div>
      </form>
    </div>
  )
}
```

1

<details>

<summary>--> useFieldArray on virtualized</summary>

The useFieldArray() methods allows us to **edit** a viertualized list.

The **itemKey** prop in the component provides each virtualized item with **its unique field ID**, which updates only changed items within the list.

The **Input validation** doesn't cover unmounted virtualized inputs; apply it on form submission, where the complete dataset will be available.

```jsx
//Virtualized Form without AutoSizer
//The <FixedSizeList> is optimized for number values on itemSize
//while <VariableSizeList> for functions returning a number
//We use the useForm() defaultValue dataset to set the useFieldArray()
function Terzo() {
  const [items] = React.useState(() =>
    Array.from(Array(100).keys()).map((i) => ({
      title: `List ${i}`,
      quantity: Math.floor(Math.random() * 100)
    }))
  );

  return (
    <div className="App text-center d-flex flex-column align-items-center">
      <h1>Virtual useFieldArray</h1>
      <List items={items} />
    </div>
  );
}

function List({ items }) {
  const { register, control, handleSubmit, formState: {errors} } = useForm({
    criteriaMode: "all", defaultValues: { test: items }
  });
  const { fields, remove } = useFieldArray({ control, name: "test" });
  
  //We can handle error for the entire datase on submit.
  function dataerrors(data){
    let errori = data.test.filter((x)=> x.quantity > 95 )

    if( errori ){
      errori.map((x)=>{ console.log( x.title ) })
      window.alert("error values invalid")
    }
  }
  
  return (
    <form className="scrolla" onSubmit={handleSubmit(dataerrors)}>
      <FixedSizeList
        width={400} height={200}
        itemSize={40} itemCount={fields.length}
        itemData={fields} itemKey={(i) => fields[i].id}
      >
        {({ style, index, data }) => {
          return (
            <div style={style} className="wind">
              <input {...register(`test.${index}.quantity`)} />

              <span>
                <button onClick={() => remove(index)}>remove</button>
              </span>
            </div>
          );
        }}
      </FixedSizeList>
      <button type="submit">Submit</button>
    </form>
  );
}
```

1

1

1

1

1

</details>

1

1

1

1

1

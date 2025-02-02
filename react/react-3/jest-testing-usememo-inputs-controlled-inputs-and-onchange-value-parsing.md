# Jest testing, useMemo() inputs, controlled inputs and onChange() value parsing

* [The useMemo() input components on useForm()](jest-testing-usememo-inputs-controlled-inputs-and-onchange-value-parsing.md#the-usememo-input-components-on-useform)
* [The controlled and uncontrolled input components](jest-testing-usememo-inputs-controlled-inputs-and-onchange-value-parsing.md#the-controlled-and-uncontrolled-input-components)
* [Value parsing on input change](jest-testing-usememo-inputs-controlled-inputs-and-onchange-value-parsing.md#value-parsing-on-input-change)

A React project created with Create-React-App includes a **test.js** file in the src folder and comes pre-configured with **Jest**.

Jest is a **testing framework**, it organizes and runs the test file with the **describe()** and **it()** functions. It can be used to test any JavaScript code.

The **@testing-library/react** package provides the tools and utilities for component testing on React.\
It allows you to virtually render components and make assertions about their behavior.

```jsx
//The describe(string, fn) groups related test into a test suite.
//The it(string, fn) handles teh singular tests.
"dependencies": {
  "@testing-library/jest-dom": "^5.17.0",
}

//App.test.js
import { render, screen, act, fireEvent, waitFor } from "@testing-library/react";
import Secondo from './components/Secondo';

describe("App", () => {
  it("Singular test", async () => {
    ...
  });
  ...
}
```

<details>

<summary>Imported Utilities from Testing Library</summary>

* <kbd>**render**</kbd>: function. It mounts the React component into a virtual testing environment.
* <kbd>**screen**</kbd>: object. It provides query functions to find rendered elements within the rendered component.
* <kbd>**act**</kbd>: function. It wraps any code that interacts with the component, ensuring that all its React updates are **batched** during tests.
* <kbd>**fireEvent**</kbd>: function. It simulates user interactions on rendered components. It can trigger events like **click()**, **input()** and **submit()**.
* <kbd>**waitFor**</kbd>: function. It handles asynchronous operations in tests, including those commonly used with React Hook Form.

</details>

The **getByRole()** locates DOM elements for **fireEvent()** to trigger events on.                                                               Its **option** object finds elements by their **accessible name** using a regular expression.

{% tabs %}
{% tab title="Test Button role" %}
The **button role** is implicit in the \<button> element.                                                                                                         A button's accessible name is often its text content. It can also be explicitly set with **aria-label** or a **\<label>** element.

```jsx
//App.test.js
screen.getByRole("button", { name: /SUBMIT/i } ));

//Can be accessed by.
<button type="submit"> SUBMIT </button>

<button aria-label="submit" type="submit"> allegro </button>

<label for="unrelated"> submit </label>
<button id="unrelated" name="third" type="submit" > bottone </button>
```
{% endtab %}

{% tab title="Test input role" %}
The **register()** name is for internal form management and is not an accessible name.                           The getByRole('**textbox**') covers _text_ and _email_ input types, while getByRole('password') is used for password inputs.

```jsx
//Input accessible name with <label> content or aria-label attribute
screen.getByRole("textbox", { name: /answer/i }) );

<label htmlFor="pino"> answer </label>
<input id="pino" type="text" {...register("risposta")} />

<input aria-label="answer" type="text" {...register("risposta")} />
```

We can use **getByLabelText()** to find and interact with text input elements associated with a specific label.

```jsx
//The <label> text content perform
screen.getByLabelText(/Answer/i)

<label htmlFor="pino"> Answer </label>
<input id="pino" {...register("answer")}/>
```
{% endtab %}
{% endtabs %}

The **act()** function wraps the code that tests updates on the rendered component.                                                                                        The **await** keyword ensures that all **async** state changes are completed before the next **assertion** is executed.

Most **useForm()** operation, are implicitly asyncronous, they trigger and update side effects in the formState andtheir tests require to be wrapped in await act().

```jsx
//We check if the error message renders on empty input submit
<input {...register("answer", { required: "Answer is required" })} />
{formState.errors?.answer && <span>{formState.errors?.answer.message}</span>}
<button type="submit"> SUBMIT </button>

it("should have a submit button", async () => {
  await act(async () => {
    fireEvent.submit(screen.getByRole("button", { name: /SUBMIT/i } ));
  })

  expect(screen.getByText("Answer is required")).toBeInTheDocument();
});
```

We check for Form submission errors by verifying its displayed **error messages** or by testing a **mock function** on form submit.

{% tabs %}
{% tab title="Error message inTheDocument" %}
The **fireEvent.input()** simulates updating _event.target.value_ by using a mock **event object**.                               The **DOM element** found by getByRole() is set as the **target**, and its value property is updated.

```jsx
//The second argument of input() is the event object
<input aria-label="answer" name="answer" id="answer"
  {...register("answer", { minLength: { value: 5, message: "Too short" } })}
/>

it("Minlength error message", async () => {
  await act(async () => {
    fireEvent.input(screen.getByRole("textbox", { name: /answer/i }), {
      target: { value: "gege" }
    });

    fireEvent.submit(screen.getByRole("button", { name: /SUBMIT/i } ));
  })

  expect(screen.queryByText("Too short")).toBeInTheDocument();
})
```
{% endtab %}

{% tab title="Mock function BeenCalled " %}
**Jest** mock functions simulate functions in the test file.\
We can control the **mocks**' behavior and enable assertions on their usage and return values.

The form component is given a **function prop**, used for the mock function in the test.                                                It will verify that the mock gets called on submission, confirming that input **validation** passed.

```jsx
//login will contain teh mock function, triggered onSubmit()
function Secondo({ login  }) {
  const { register, handleSubmit, formState } = useForm({
    mode: "onChange"
  });
  const [message, setMessage] = useState("");

  const onSubmit = (data) => {
    login (data.answer);
    setMessage(data.answer === "42" ? "that's correct" : "that's incorrect");
  }
  return ()
}

it("we are finally testing the submit", async () => {
  const mockLogin = jest.fn()
  render(<Secondo login={mockLogin } />);
  
  await act(async () => {
    fireEvent.input(screen.getByLabelText(/Answer/i), { 
      target: { value: "docedaedro" } 
    });

    fireEvent.click(screen.getByRole("button", { name: /SUBMIT/i }));
  });

  expect(mockLogin).toHaveBeenCalled();
});
```
{% endtab %}
{% endtabs %}

The Jest **beforeEach()** function sets up the different test environments. It renders a new form component before each test, preventing any test interference.

```jsx
//The test will proceed even if the login prop is not provided in the test.
beforeEach(() => {
  render(<Secondo login={mockLogin}/>);
});
```

The Jest matcher() functions verify test conditions within **expect()** calls. The **.not** modifier checks for the absence of a condition.

```jsx
//The input fill and submit to not have passed due to error.
it("testing if the value doesn't trigger a submit", async () => {
  await act(async () => {
    fireEvent.input(screen.getByLabelText(/Answer/i), { 
      target: { value: "42" } 
    });

    fireEvent.click(screen.getByRole("button", { name: /SUBMIT/i }));
  });

  await waitFor(()=>{
    expect(mockLogin).not.toHaveBeenCalled();
  })
})
```

The **waitFor()** utility handles asynchronous operations in tests. It is specifically for operations that are not triggered by user events and are not within an act () block.

### The useMemo() input components on useForm()

The **memo()** is a React higher-order component that memoizes a function component, preventing unnecessary re-renders.

It differs from the **useMemo()** and **useCallback()** hooks.

> * memo() prevents functions components from re-rendering.
> * useMemo() memoizes values to avoid unnecessary recalculations.
> * useCallback() memoizes functions to prevent the creation of new function instances, which can help prevent excessive re-renders.

```jsx
//It won't re-render on formState change or form submission.
//If no function prop is present, there is no change.
const Diversivo = memo(() => { 
  console.log("Won't get triggered on formState change");
  return <div>Form element</div>;
});

function Secondo() {
  const {register, handleSubmit} = useForm()
  const manda = (data) => console.log(data)

  return (
    <div>
    <form onSubmit={handleSubmit(manda)}>
      <input {...register("primo")} />
      <p> {formState.isDirty ? "Typed input" : "No change" } </p>
      <Diversivo />
      <input type="submit" />
    </form>
    </div>
  )
}
```

The memo() accepts an optional **comparison function** to control how **props** are checked for changes between renders.

By default, memo() uses a shallow comparison (using ===) and the component re-renders only if the comparison function returns false.                                                                                                                                     You can provide a custom comparison function that receives the previous and current props.

```jsx
//The control is necessary to render the input
//The numero useState() will trigger a re-render after 3
const Diversivo = memo(({control, numeri}) => {
  console.log("Doesn't need to use all the props", control);

  return (
    <div>
      <Controller name="controlled" control={control}
        render={({ field }) => (
          <div> <input {...field} /> </div>
        )}
      />
    </div>
  );
}, (prevProps, nextProps) => {
  console.log( "Previous and current props object", prevProps, nextProps )
  return nextProps.numero < 3;
});

function Secondo() {
  const { register, handleSubmit, formState, setValue, control, watch } = useForm();
  const onSubmit = (data) => { console.log( data ) }
  const [numero, setNumero] = useState(0)

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <button type="button" onClick={()=>{ setNumero((e)=> e+1) }}> Adda </button>
      <h3> { numero } </h3>

      <input {...register("answer")} />
      <p> {formState.isDirty ? "tornato scuro" : "tutto pulito" } </p>
      
      <Diversivo control={control} numero={numero} />
      <button type="submit"> Submit </button>
    </form>
  );
}
```

<details>

<summary>Object references and memo() re-render exceptions</summary>

An **object variable** holds a **reference**, not the object's data, of the data memory location.

By default, memo() compares only p**rop references**, not their values (==). That's why internal changes to the **useForm()** `control` prop are not detected by the memo() shallow comparison.

Form state internal changes, via the **control**, cause useFormState() to return a **new object** and trigger a re-render.

```jsx
//No new control gets created, so it doesn't trigger a memo() re-render
const Diversivo = memo(({control}) => { 
  let { isDirty } = useFormState({ control })
  console.log("Triggered on dirty form", control);
  
  return (
    <div>
      Memoized element      
    </div>
  );
});
```

</details>

### The controlled and uncontrolled input components

There are 2 types of input components, **contolled** and **uncontrolled**.

React-Hook-Form uses uncontrolled inputs for performance. It stores updated **input values** in the **DOM** and retrieves them via **refs** when needed.

External form libraries often use controlled inputs. These inputs store every change in the **form's state**, triggering a re-render.

The **\<Controller>** component adapts controlled inputs to work like uncontrolled inputs.                                                  It filters the onChange() events from its rendered controlled  input, only propagating changes when the value has actually changed.

```jsx
//The mui-material is an external controlled input component.
import { TextField, Select, MenuItem, Input } from "@mui/material";

const { handleSubmit, reset, watch, control, register } = useForm({
  defaultValues,
})

<form onSubmit={handleSubmit(onSubmit)}>
  <Controller
    render={({ field: {value, onChange, ...fields} }) => (
      <Select {...fields} value={value}
      onChange={(e)=>{ 
        console.log( "è stato premuto" )
        onChange( e.target.value ) 
      }}>
        <MenuItem value={10}>Ten</MenuItem>
        <MenuItem value={20}>Twenty</MenuItem>
      </Select>
    )}
    control={control}
    name="select"
    defaultValue={10}
  />
</form>
```

### Value parsing on input change

The useForm() returns all input values as strings, regardless of the **input type**.                                                      The **valueAs** function can **parse** these strings on submit, but empty inputs can still cause errors.                      We can parse on Change allowing the immediate **setValue()** reset of invalid input values.

The input `step` attribute controls the valid numeric increments and decimal values allowed for form submission.

```jsx
//Decimal separators are country dependent (, in EU, . in US)
//We reset the input setValue() if input is invalid.
const { register, handleSubmit, setValue } = useForm({
  defaultValues: { numero: 0 },
});

const numberChange = (event) => {
  const parsed = parseFloat(event.target.value);
  setValue("numero", isNaN(parsed) ? 0 : parsed);
};

<form onSubmit={handleSubmit(spice)}>
  <input type="number" step="any"
    {...register("numero", { onChange: numberChange })}
  />
</form>
```

# REACT 4

* 1
* 1
* 1

**useMemo(**function, dependencies**)** is a React Hook that **caches** the **return value** of a function _between re-renders_.

```
const memoized = useMemo(
  () => memoize(depend),
  [depend]
);
```

A useMemo() function will be **re-called** only if one of its **dependencies** changes.

<details>

<summary>useMemo() render example</summary>

We import an array of objects as a prop with 2 useState().

```
import TodoList from './components/Externals';

import { createTodos } from './components/External1';
const todos = createTodos();

const [tab, setTab] = useState('all');
const [isDark, setIsDark] = useState(false);

<div>
  <button onClick={() => setTab('all')} />
  <button  onClick={() => setTab('active')} />
  <button onClick={() => setTab('completed')} />
</div>

<input
  type="checkbox"
  checked={isDark}
  onChange={e => setIsDark(e.target.checked)}
/>

<TodoList
  todos={todos}
  tab={tab}
  theme={isDark ? 'bg-warning' : 'bg-success text-white'}
/>

```

We **useMemo()** a slow imported function using 2 props, any _theme_ useState() changes will **re-render** the parent component but **won't re-call** the useMemo() function.

<pre><code>import { filterTodos } from './External1'

function Todolist({ todos, theme, tab }) {

<strong>  const visibleTodos = useMemo(
</strong>    () => filterTodos(todos, tab),
    [todos, tab]
  );

  return (
    &#x3C;div className={theme}>
      &#x3C;p>Note: filterTodos is artificially slowed down!&#x3C;/p>
      &#x3C;ul>
        {visibleTodos.map(todo => (
          &#x3C;li key={todo.id}>
            {todo.completed ?
              &#x3C;s>{todo.text}&#x3C;/s> :
              todo.text
            }
          &#x3C;/li>
        ))}
      &#x3C;/ul>
    &#x3C;/div>
  );
}

</code></pre>

The useMemo() function is a delayed function that filters the array object to be rendered

```
export function filterTodos(todos, tab) {

  let startTime = performance.now();
  
  while (performance.now() - startTime < 500) {
    // Do nothing for 500 ms to emulate extremely slow code
  }

  return todos.filter(todo => {
    if (tab === 'all') {
      return true;
    } else if (tab === 'active') {
      return !todo.completed;
    } else if (tab === 'completed') {
      return todo.completed;
    }
  });
}

```

</details>

<figure><img src="../.gitbook/assets/useMemo().png" alt="" width="375"><figcaption><p>slow button useMemo() change and fast checkbox</p></figcaption></figure>

### The useReducer() React Hook

The **useReducer(reducer, state)** adds a reducer function to change the state object.

```
//The reducer function uses (initial) state and (dispatch) action as arguments. 
//some cases return update the state, other replace it with action properties

function reducer(state, action) {
  switch (action.type) {
    case 'incremented_age': {
      return {
        name: state.name,
        age: state.age + 1
      };
    }
    case 'changed_name': {
      return {
        name: action.nextName,
        age: state.age
      };
    }
  }
  throw Error('Unknown action: ' + action.type);
}

const initialState = { name: 'Taylor', age: 42 };
const [state, dispatch] = useReducer(reducer, initialState);
```

The **useReducer()** returns the current **state** and the **dispatch** update function to **re-render** the state.

```
//dispatch() objects needs a type for its reducer() 
const [state, dispatch] = useReducer(reducer, initialState);

function handleButtonClick() {
  dispatch({ type: 'incremented_age' });
}

function handleInputChange(e) {
  dispatch({
    type: 'changed_name',
    nextName: e.target.value
  }); 
}

<div>
  <input
    value={state.name}
    onChange={handleInputChange}
  />

  <button onClick={handleButtonClick}>
    Increment age
  </button>
  <p>Name: {state.name}. Age: {state.age}.</p>
</div>

```

<details>

<summary>Reducer return objects</summary>

The **reducer()** function doesn't update _single properties_, it **returns new state objects**.

```
//we can decostrcut the state and change some props
//or we can use methods to return or delete specific props from the object

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }];
    }
    case 'changed': {
      return tasks.map(t => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter(t => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

```

1

1

1

1

1

1

1

</details>

An **initial state** object returned from a **function**, even if called once, will be re-called each re-render.

We add an **initializer function** to the **useReducer()** hook.

```
//Any argument in the initializer function will be the second useReducer() argument
const [state, dispatch] = useReducer(reducer, createInitialState(username));
const [state, dispatch] = useReducer(reducer, username, createInitialState);

//If no argument is needed in the initializer function then it's null
const [state, dispatch] = useReducer(reducer, none, createInitialState);

```

### Scaling up React components with useContext() and useReducer()

A parent component includes its **{children}** components as a **prop.**

```
//we can style ANY children component in the Parent
const Text = () => <p>This is favorable</p>

function Sentence({children}){
  return(
    <div className='text-danger text-center'>
      {children}
    </div>
  )
}

<Sentence>
  <p>These are children</p>
  <Text />
</Sentence>

```

We **useContext()** and **useReducer()** to manage complex states and event handlers at **every component level.**

<details>

<summary>Single component useContext() and useReducer() </summary>

We **createContext()** Provider values for the **\[state, dispatch]** of **useReducer().**

Any imported nested **component** will have access to both the state and event handler functions.

```
//while rendering any {children} component 
import { useReducer, useContext, createContext} from 'react';

const Stato = createContext(null);
const Statodis = createContext(null);

export default function Task({children}) {

  function reducer(state, action) {
    switch (action.type) {
      case 'incremented_age': {
        return {
          name: state.name,
          age: state.age + 1
        };
      }
      case 'changed_name': {
        return {
          name: action.nextName,
          age: state.age
        };
      }
    }
    throw Error('Unknown action: ' + action.type);
  }
  const initialState = { name: 'Taylor', age: 42 };
  const [instate, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <Stato.Provider value={instate}>
        <Statodis.Provider value={dispatch} >
          {children}
        </Statodis.Provider>
      </Stato.Provider>
    </div>
    );
}
```

The **useContext()** needs to be in the same page as the provider to work.                           We need to **export** it as a **custom Hook**.

```
//It won't work on an imported component
import Task from "./components/External1"
let instate = useContext(Task)

//We export the provided values 
export function useStato() {
  return useContext(Stato);
}
export function useStatodis() {
  return useContext(Statodis);
}

import Task, { useStato, useStatodis } from "./components/External1"

let stato = useStato()
let dispatch = useStatodis()

```

We **import** the _component_ and the _custom hooks_:

```
import Task, { useStato, useStatodis } from "./components/External1"

<div className='row me-0 '>
  <Florida>
    <p>Children components</p>
    <Text />
  </Florida>

  <Task>
    <Increase />
  </Task>
</div>

```

Now any **children** component has access to the **dispatch()** event handler.

```
//and will share the useReducer() state
function Increase(){

  let stato = useStato()
  let dispatch = useStatodis()

  function handleButtonClick() {
    dispatch({ type: 'incremented_age' });
  }
  
  function handleInputChange(e) {
    dispatch({
      type: 'changed_name',
      nextName: e.target.value
    }); 
  }

  return(
    <div>
      <input
        value={ stato.name }
        onChange={handleInputChange}
      />

      <button onClick={handleButtonClick}>
        Increase
      </button>
      <p>Name: {stato.name}. Age: {stato.age}.</p>
    </div>
  )
}

```

</details>

<figure><img src="../.gitbook/assets/scaleUp.PNG" alt=""><figcaption><p>Scaled up useReducer() and useContext()</p></figcaption></figure>

### The useRef() React Hook

The **useRef()** lets us reference a value that’s not needed for rendering

```
//It returns an object with a single property .current

const valref = useRef(0);
valref.current == 0
```

Contrary to **useState()**, **useRef()** changes _won't re-render_ the page and its new value will be **immediately available** between different functions (unlike useState() which has to render it first).

<details>

<summary>Difference in updating useRef() and useState()</summary>

In this example, we show the useRef() value using a handler function.

```
let ref = useRef(0);
const [ refe, setRefe ] = useState(0) 

console.log("gets re-called only with useState")

function handleClick() {
  ref.current = ref.current + 1;
  alert('After click useRef() is ' + ref.current);
}

function altroClick(){
  setRefe((x)=> x + 1)
  alert("After click useState() is " + refe)
}

<div>
  <button onClick={handleClick}>
    The useRef()
  </button>
  <button onClick={altroClick}>
    the useState()
  </button>
  <button onClick={separate}>
    Calling both
  </button>
</div>
```

</details>

<figure><img src="../.gitbook/assets/useReff().png" alt="" width="276"><figcaption><p>Returned useRef() and useState() values</p></figcaption></figure>

1

1

1

1

1

1

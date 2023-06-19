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

1

1

1

1

1

1

1

1

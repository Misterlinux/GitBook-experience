# REACT 4

* 1
* 1
* 1

**useMemo(**function, dependencies**)** is a React Hook that **caches** the **return value** of a function _between re-renders_.

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

```
import { filterTodos } from './External1'

function Todolist({ todos, theme, tab }) {

  const visibleTodos = useMemo(
    () => filterTodos(todos, tab),
    [todos, tab]
  );

  return (
    <div className={theme}>
      <p>Note: filterTodos is artificially slowed down!</p>
      <ul>
        {visibleTodos.map(todo => (
          <li key={todo.id}>
            {todo.completed ?
              <s>{todo.text}</s> :
              todo.text
            }
          </li>
        ))}
      </ul>
    </div>
  );
}

```

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

1

1

1

1

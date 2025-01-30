# Page

* 1
* 1
* 1
* 1

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

1

1

1

1

1

1

1

# Images handling and the classNames utility

* 1
* 1
* 1

We can store images in both the **/public** and the **/src** folder.

Images loaded from the /public folder are served directly from the **web server**, bypassing the React application, which enables easier _caching_. These images can't be edited with js.

Images from the /src folder need to be **imported** into the component first, they can be edited with both css and **js**. Additionally, **Webpack** can be utilized to optimize these images during the building process.

On a create-ract-app we can access the webpack.config.js by using **npm run eject**.

```jsx
//For /public you can src the direct route 
import amiibo from "../img/amiibo.png"

<div>
  <img src="./amiibo.jpeg" alt="" className="img-fluid" />
  <img src={amiibo} alt="" className="img-fluid" />
</div>
```

<mark style="background-color:blue;">**ClassNames**</mark> is a utility JavaScript library that enables the joining of classNames on React components. It allows for conditional classNames, making it easier to manage dynamic class names.

```jsx
//Any javascript operation that return a boolean can be used
//roles.includes('admin') or functions 'admin-role': isAdmin(),
npm install classnames

import classNames from "classnames";

let [isActive, setIsActive] = useState(true)
let [conta, setConta] = useState(0)

const classi = classNames('text-white', {
  'bg-success': isActive,
  "bg-warning": !isActive,
  "border-danger": conta % 2 == 0,
  "border-secondary": conta % 2 == 1
});

<div className={`border border-5 ${classi}`}>
  Variable className
</div>
```

We can render the classNames value as an array.

```jsx
//Objects get destructed
let arr = ['text-danger', { "bg-secondary": true, "bg-primary": false }];
let arraiato = classNames('a', arr);
```

1

1

1

1

1

1

1

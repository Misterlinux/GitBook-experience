# React-Spring 1

* 1
* 1
* 1
* 1

**React-spring** is a **library** to create **animated** UI components.

```
npm i react-spring

App.js
import { animated, useSpring } from '@react-spring/web'
```

We _**import**_ the **animated** higher-order **component**, a function that takes a component and returns a new one.

{% tabs %}
{% tab title="Animation object" %}
We **useSpring({})** hook to set up the **animation object** properties **(**from, to**)**, then we deconstruct it on the **style** property to trigger it on mount.

```jsx
//we append the component on the animated high-order tag
function MyComponent() {

  const springs = useSpring({
    from: { x: 0 },
    to: { x: 100 },
  })

  return (
    <animated.div
      style={{
        width: 80,
        height: 80,
        background: '#ff6d6d',
        borderRadius: 8,
        ...springs
      }}
    />
  )
}

<div>
  <MyComponent />
</div>
```
{% endtab %}

{% tab title="Api animations methods" %}
We **deconstruct** the **api object** from the **useString({})** hook, and use its **methods** to control the **state** of the spring animation on _event listeners_.

```jsx
//Use className on the animated.div to style with CSS
//useString({}) needs to include all the animation properties used by the api
//The api methods control the state of the animation (start, stop set, etc)

.box{
    display: flex;
    width: 80px;
    height: 80px;
    background-color: brown;
    border: solid 5px yellow;
    border-radius: 10px;
}

function MyComponent() {

  const [springs, api] = useSpring(() => ({
    from: { x: 0, y: 0 },
  }))

  const handleClick = () => {
    api.start({
      from: {
        x: 0,
        y: 0,
      },
      to: {
        x: 150,
        y: 50,
      },
    })
  }

  return (
    <animated.div
      onClick={handleClick}
      className="box"
      style={{
        ...springs      
      }}
    />
  )
}
```
{% endtab %}
{% endtabs %}

<figure><img src="../.gitbook/assets/moved1.gif" alt="" width="293"><figcaption><p>Spring animation object + onClick api</p></figcaption></figure>

1

1

1

1

# JS  6

* Promises
* kinda

A **Promise** is an **object,** it **resolves** or **rejects** an **asynchronous function** and its value, it has the methods _promise.then(), promise.catch() and promise.finally(),_ from Promise.prototype:

```
//It can take one or both parameters (resolve, eject), for the.() and catch() 

const myPromise = new Promise(function (resolve, reject) {
  let sampleData = [2, 4, 6, 8];

  if (sampleData[1]) {
    //both resolve/reject RETURN the value in ()
    resolve( "resolved string" );  
  } else {
    reject('An error occured!');
  }
});

myPromise
  .then(function (e) {
    console.log(e);          //4
  })
  .catch(function (error) {
    throw new Error(error);  //if reject we get Error('An error occured!')
  })
  .finally(function () {
    console.log('PROMISE COMPLETED');  //printed in any case
})

```

The **.then()** method can take 2 arguments, they are callback functions, for the cases of the Promise _resolve_ and _reject,_ while catch() has only one for handling errors:

```
const promise1 = new Promise(function (resolve, reject) {
  (5> 10) ? resolve('Successo !') : reject("not pervenuted")
});

promise1.then(function (value, none) {
  console.log(value)      // expected output: "Successo!"
  console.log(none )      //(in promise) not pervenuted?
});

```

**Promise.all()** takes an _iterable_ of promises as input, and returns a promise:

```
console.log(                //Promise {<pending>}
  Promise.all([1,2,3,4])    //  [[Prototype]]: Promise
)                           //  [[PromiseState]]: "fulfilled"
                            //  [[PromiseResult]]: Array(4), [[Prototype]]: Array(0)

```

Promise.all() needs **all its promises** to be resolved, it will wait until _all promises are fulfilled_:

```
const p1 = Promise.resolve(3);
const p2 = 1337;
const p3 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("foo");
  }, 5000);
});

//after 5 seconds we get
Promise.all([p1, p2, p3]).then((values) => {
  console.log(values);         //[3, 1337, "foo"]
});

```

We can use **.catch()** to check each single promise:

```
const ph1 = new Promise((resolve, reject) => {
  setTimeout(() => resolve('p1_delayed_resolution'), 1000);
});

const ph2 = new Promise((resolve, reject) => {
  reject(new Error('p2_immediate_rejection'));
});

Promise.all([
  ph1.catch((error) => error),
  ph2.catch((error) => error ),
]).then((values) => {
    console.log(values[0]);           // "p1_delayed_resolution"
    console.error(values[1]);         // "Error: p2_immediate_rejection"
})

//if we did Promise.all([ph1, ph2]) we would just get the Error p2

```

### Api and Fetch()

Fetch() allows us to **send network request** and **load information,** without reloading the page, it returns an **Promise**:

```
//without an [options] the fetch() is just a GET downloading content from the url

let promise = fetch(url, [options])    //[options] can be methods/headers

```

At this stage we don't have a body yet, we check the **HTTPS status** for errors:

```
let response = await fetch(url);

if (response.ok) { 
  //if HTTP-status is 200-299 get the response body
  let json = await response.json();
} else {
  alert("HTTP-Error: " + response.status);
}

```

The **response** promise object provides methods to access it in **various formats**:

```
// Some code



```


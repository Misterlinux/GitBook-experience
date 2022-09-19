# JS 7

* its just a&#x20;
* meme bro

A **Set()** is a list-like data structure that _can't be accessed through index_ like arrays:

```
//we add new elements to a set with .add()

let set= new Set()
set.add(1)
let primo= {tin: 10, ten:"dieci"}

```

It can't have more than **one strict equal element** (===):

```
//we can use .size property to count the elements in it

let uno= new Set()
uno.add(1)
uno.add(1)
uno.add({uno: 1})
uno.add({uno:1)}

uno.size            //Set(3) {1, {…}, {…}}
//the 2 objects arent strict equals so it will add
```

We use **add(),** **delete()** and **has()** methods on Sets:

```
//.has() returns a boolean if the element is present in the Set

const rik= new Set()
rik.add( {altro: "fatto"})
rik.add("non e vero")

rik.has("NON E VERO" )                 //false
rik.has("NON E VERO".toLowerCase() )   //true
rik.has({altro: "fatto"})              //false

//.delete() will remove said element from the set
rik.delete( "non e vero" )

```

We can use both the **forEach()** and **for()** loop on Sets:

```
//we could also use rik.keys() and rik.values() but it will work the same

for(const x of rik) {
  console.log(x);
}

rik.forEach((x)=>{
  console.log(x)
})

```


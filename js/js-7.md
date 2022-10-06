# JS 7

* Will add smaller content to compensate

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

To pass **from Set to Array** and vice-versa:

```
//By becoming a set it will also eliminate its duplicates

let sort= [1,1,1,1,2,3,4,5,5,3,2,1,2,3]
let set= new Set(sort)        //Set(5) {1, 2, 3, 4, 5}

//to then go back to arrays

[...new Set(sore)]            //[1, 2, 3, 4, 5]
Array.from( new Set(sore))    //[1, 2, 3, 4, 5]
```

We can use Sets to get **intersections** and **difference** sets:

```
//we start from 2 Sets, we need to convert one into an array to use .filter(9
//while the other will pass elements from the second that return true if on the first

const intersection = new Set([...rik].filter((x) => seconn.has(x) ));

const difference = new Set([...rik].filter((x) => !seconn.has(x)));
//opposite operation with difference
```


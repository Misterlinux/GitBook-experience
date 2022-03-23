# JS 3

* Filter(), map() ,and forEach() on arrays

Some **methods** can implement arrow **functions** for better results, like **.map()**:

```
//.map() will return a NEW ARRAY following the function in the METHOD CALL ()
namesArray.sort().map( (x) => x.toUpperCase() )        //['ANTIGONI', 'CHRIS', 'ELAMIN',]

//with callback functions we can also use functions in calls without the ()
function upper(x){
    return x.toUpperCase()
}
namesArray.map(upper)

```

**.forEach()** is different:

```
//it doesnt return an array, but it still lopps trought the array 
let anni = [1964, 2008, 1999, 2005, 1978, 1985, 1919]

anni.forEach( (x) => {
  console.log( 2022-x )    //we will get a list of results not an array, 58 14 23 17 44 37 103
})

//you can use a forEach after a .map() but not the opposite
let numeronia = [4,6,8,2,4,1]
numeronia.map( (x)=> x+2 ).forEach( (x) => console.log( "the number is " + x))  //the number is 6...

```

**.filter()** will return an array with only the ones that satisfy:

```
//It will create a new array with only the passed elements if we store it with
let anni = [1964, 2008, 1999, 2005, 1978, 1985, 1919]
const unicovo = anni.filter( (x) =>
  2022-x > 21
)

console.log( "these people can drive " + unicovo)    //these people can drive 1964,1999,1978,1985,1919

```

**.find()** on the other hand, will return the _first_ element matching:

```
var product1 = {
  id: 1,
  name: "Toaster X56 Plus",
  price: 12.98,
  stock: 105,
};

var products = [product1, ... ];
//we use find() not only to return the first element mattching but also to not have an array
//products.find()would return    { id: 4, name: 'Star Ship', price: 100, stock: 5 }
//products.filter() would return [ { id: 4, name: 'Star Ship', price: 100, stock: 5 } ]
function add(x){
    let scelto = products.find((xx)=>{
        return xx.id == x
    })
}

add(1)    //we check the array products for objects with property id ==(1) and get returned just the object

```

**Map() and filter()** can be different when returning properties and objects:

```
let quatt = {
    cosa: [2, 9 ,6, 2 ]
}

//they return different values
quatt.cosa.filter( (x)=> x<10 )    //[ 2, 9, 6, 2 ]
quatt.cosa.map( (x)=> x<10 )       //[ true, true, true, true ]

//and to chain you will have to use filter()
quatt.cosa.filter( (x)=> x<10 ).forEach( (x) =>  console.log( "This shoudl work with " + x ) )    //This shoudld work with 2 

//having an array of objects and properties with arrays 
let terzo = {
    name: "terzo",
    totali: 30,
    already: 8,
    dove: {
        citta: "naples",
        posto: "sud"
    },
    cosa: [ 10,5,6, 3]
}

let quatt = {
    name: "quatt",
    totali: 12,
    already: 8,
    dove: {
        citta: "roma",
        posto: "centro"
    },
    cosa: [2, 9 ,6,2 ]
}

let tutti = [primo, secondo, terzo, quatt]
//we can have an object with methods 
let app = {
    cibo: function(x){
        let fin = []
        tutti.map((xx) =>{
            if(xx.cosa.includes(x) ){
                return fin.push( xx.name )
            }
        })
        return fin
    },
//with map we need an extra array to push the specific properties of the if()
    filto: function(zona){
        return tutti.filter( (x)=> x.dove.posto == zona ).length
    }
//filter() returns the objects as an array and we get the length
}

console.log( app.cibo(10) )            //[ 'terzo' ]
console.log( app.filto("centro")  )    // 1 ,we get the length of the array of objects filtered

```

before we start with objects let's check the **for() and while() loop**

```
//for() is a more specific loop with a set counter and stop limit
for(let tin= 0; tin < limes.length; tin++ ){
    console.log( tin)        //0,1,2,3,4,(limes.length-1)
}

//but sometimes you may need a looser condition for the loop, also some object keys in advance
//we can get the values for properties in the wedges, object["string"] = number

function limesToCut4(wedgesNeeded, limes) {
    let index = 0;
    const wedges = {
        'small': 6,
        'medium': 8,
        'large': 10
      }
    while(wedgesNeeded > 0 && index < limes.length){
        if(limes[index]) {
            wedgesNeeded -= wedges[limes[index]] ;
            index++;
        } else {
            return index;
        }
    }
    return index;
}

console.log( limesToCut(0, ['small', 'large', 'medium']) )        //we get 0

//while( condition ) we can modify the index somewhere else and then close it with else

```

### Objects, methods, and keys

**Objects** are variables that contain a collection of named values, stored in **property: value** pair:

```
//and when we want to extract the property/value we:
const car = {
  type:"Fiat", 
  model:"500", 
  color:"white",
};

car.type              //Fiat
Object.keys(car)      //['type', 'model', 'color']  as an array we can also get
Object.keys(car)[0]   //type

```

Objects can be **edited** by assigning a new property:value:

```
//we don't need keywords 
car.type = "BMW"     //it changes the "Fiat" 
car.age = 50;        //will add a new property age: 50

//also we can add different types of data
car.nuovo = [1,2,3]            //like array
car.altro = {uno: [1,2,3,4,5], due: [1,2,3,4,5], tre: [1,2,3,4,5]}        //and objects of arrays

//we can also use this on an empty object
const person = {};
person.firstName = "John";
person.lastName = "Doe";

//To delete properties and values we use the keywords
delete car.color;
```

with different types of data, we can use **Object.keys()** on them:

```
//and we can use it on the array

Object.keys(car.nuovo)        //will return the indexes as an array ['0', '1', '2']
Object.keys(car.altro)        //will return properties [ 'uno', 'due', 'tre' ]
Object.keys(car.altro.due)    //will be of the array inside the object ['0', '1', '2', '3', '4']

```

**Object Methods** are functions stored in the object that can use **.this** for properties in the object:

```
//the function stored in the fullo method can use the property:values of the object it's in
const car = {
  type:"Fiat", 
  model:"500", 
  color:"white",
  fullo: function() {
    return this.type + " " + this.model;
  }
};

person.fullo()    //Fiat 500 
person.fullo      //[Function (anonymous)]

```

We can use the **for()** loop with both properties and values of an object:

```
const person = {
  firstName: 'John',
  lastName: 'second',
  cars: [
    {name:"Ford", models:["Fiesta", "Focus", "Mustang"] },
    {name:"BMW", models:["320", "X3", "X5"] },
    {name:"Fiat", models:["500", "Panda"] }
  ]
}

//with for(), we set the counter/Object.keys IN object and also use it for vlues too
for (let x in person) {                  //key        object[key]=value
  console.log( x + ": " + person[x] )    //firstName: John,             lastName: second
}

//as for the array, its keys would be the index, we can also go beyond for nested properties
for (let i in myObj.cars) {
  yy = myObj.cars[i].name + " " ;
  console.log( yy )                  //this will print the "Ford" name but
  
  for (let j in myObj.cars[i].models) {
    y = myObj.cars[i].models[j] + " ";
    console.log( y )                 //also the array elements of the name[i] "Fiesta Focus Mustang" 

  }
}

```

This can help us for more precise loops of if statement:

```
const prepTime = {
    'Pure Strawberry Joy': 0.5,
    'Energizer': 1.5,
    'Green Garden': 1.5,
    'Tropical Island': 3,
    'All or Nothing': 5
  }

//IF between the properties/keys is present the parameter we return the VALUE of the property
function timeToMixJuice(name) {
    if(Object.keys(prepTime).includes(name)) {
      return prepTime[name]
    } else {
      return 2.5;
    }
}

//timeToMixJuice("Energizer")            //1.5

```

**Methods** can edit and return other properties:

```
let univa ={
    name: "wallace",
    molti: ["uno", 2, 3, 45],
    money: 0,
    aggiung: function(inn){
        this.nuovo = inn
    },
    ancori: function(on){
        this.nuovo += " " + on
    },
    yunn: function(oll){
        this.molti.push(oll) 
    },
    spicc: function(we){
        this.money += we
    },
    compra: function(){
        if(this.money > 10){
            return "finally, you got it"
        }else{
            return "no money ;(?"
        }
    }
}

univa.aggiung("this")     //we first add a new property this.nuovo and then we set its value 
univa.ancori("new")       //at "this", then with ancori method we change it with "this new"
univa.yunn( 23 )          //we can have the argument be added at an array property, this.molti.push(oll) 
univa.spicc(24)           //univa.compra() would return "finally, you got it" with money 24 > 10
univa.spicc(-16)          //"no money ;(?"  with money=8< 10

```

### ES6 syntax and more objects

We can use a **Default parameter** in a function:

```
function defaultParameter(name = "sam") {
  return console.log( name);
}

defaultParameter("oltre")    //"oltre"
defaultParameter()           //"sam"

```

We can also use the **spread operator** for arrays and objects:

```
//Allows us to .concat() the arrays in the order present in the array
function combineArrays(arr1, arr2, arr3) {
  return [...arr3 , ...arr2, ...arr1];
}
combineArrays( [12, 34], [23, 45], [100] )    //[ 100, 23, 45, 12, 34 ]

//We also can use it for Math. operations, without loops or filters
let maximus = Math.max( ...mat )              //100 

```

And in _objects_ we can edit the properties:

```
const dog = {
  name: "Lucas",
  age: 7,
  breed: "cocker"
}

const dogOwner = { ...dog, owner: "Juan", breed: "cocker spaniel"}
//we changed the breed and added a "OWNER" property in the new object
console.log( dogOwner )          //{ name: 'Lucas', age: 7, breed: 'cocker spaniel', owner: 'Juan' }

//and we can use it on functions as parameter
function destructuring(obj) {
  const { name, age, breed } = obj      //here we need the same names from the object to use the properties values
  return age;
}
//for example here we can use the argument for an object and then extract a property
//we return the property selected
destructuring( dogOwner )

```

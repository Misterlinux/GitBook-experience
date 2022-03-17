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

**.filter()** will allow to get the first matching:

```
//It will create a new array with only the passed elements if we store it with
let anni = [1964, 2008, 1999, 2005, 1978, 1985, 1919]
const unicovo = anni.filter( (x) =>
  2022-x > 21
)

console.log( "these people can drive " + unicovo)    //these people can drive 1964,1999,1978,1985,1919

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

//as for the array, its keys would be the index, we can also go beyond




```

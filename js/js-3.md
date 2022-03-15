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

about objects and keys

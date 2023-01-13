# Extra-JS

* here we put js stuff we may add later

### Date instance

We use the **Date()** javascript object to represent time.

```
//To create a new Date() instance we 

let data= new Date( 2002, 02, 20, 10, 50, 22 )
//new Date(year,month,day,hours,minutes,seconds,ms)
Wed Mar 20 2002 10:50:22 GMT+0100 (Ora standard dell’Europa centrale)

//we can also use the ISO 8601 format as a STRING with specific syntax
let iso= new Date( "1995-12-17T03:24:00" ) YYYY-MM-DDTHH:mm:ss.sssZ
Sun Dec 17 1995 03:24:00 GMT+0100 (Ora standard dell’Europa centrale)

```

We can parse the Date value as different strings, using also locale settings:

```
//toString() is a string parsing like new Date()
let white= new Date() == white.toString()

//We can get different time strings based on the timeZones
white.toLocaleString()        //13/1/2023, 13:54:00
white.toLocaleString("en-US") //1/13/2023, 1:53:44 PM

//we can also get hours or dates specifically
white.toDateString()              //Fri Jan 13 2023
white.toLocaleDateString("en-US") //1/13/2023
white.toTimeString()              //14:19:43 GMT+0100 (Ora standard dell’Europa centrale)
white.toLocaleTimeString("en-US") //2:19:43 PM

//on Date the en-GB shows 13/01/2023
```

We also have **set()** and **get()** **methods** for Date() objects:

{% tabs %}
{% tab title="Get() Date methods" %}
We can get() a current date() information

```
adesso.getFullYear(),    
adesso.getMonth(),       //numeral month 0-11 
adesso.getDate(),        //day of the month 1-31
adesso.getDay(),         //numeral, day of the week 0-6
adesso.getHours(),       //0-23
adesso.getMinutes(),
adesso.getSeconds(),

```
{% endtab %}

{% tab title="Set() Date method" %}
We can set() and modify a date() object

```
setFullYear()  //sets current time on a specific year
setMonth()     
setDate()      //pushes days from current date
setHours()     	
setMinutes()   
setSeconds()   

```

To use it on date() we:

```
//new Date().setFullYear(2001) will return the milliseconds, so we date() again

let past= new Date( new Date().setFullYear(2001) )
//Sat Jan 13 2001 15:49:34 GMT+0100 (Ora standard dell’Europa centrale)

let future= new Date(new Date( new Date().setDate(100) )) 
//Mon Apr 10 2023 15:51:28 GMT+0200 (Ora legale dell’Europa centrale)
```
{% endtab %}

{% tab title="Destructuring/time difference" %}
We get the variables for each date() property:

```

let data= new Date()
const [month, day, year, hour, minutes, seconds] = [
  data.getMonth(),
  data.getDate(),
  data.getFullYear(),
  data.getHours(),
  data.getMinutes(),
  data.getSeconds(),
];

```

We can also use Date() objects for time difference:

```
//we get the value as milliseconds
let start= new Date( "1995-12-17T03:24:00" )
let end= new Date("1995-12-17T05:24:00")

console.log( end - start )                     //7200000
console.log( ((((end - start)/1000)/60)/60) )  //2 hours

```
{% endtab %}
{% endtabs %}




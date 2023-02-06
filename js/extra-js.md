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

We can **parse the Date value** as a string:

```
//toString() is a string parsing like new Date()
let white= new Date()    //Sat Feb 04 2023 12:38:00 GMT+0100 / OBJECT
white.toString()         //Sat Feb 04 2023 12:38:00 GMT+0100 / STRING
```

The **toLocateDateString()** method returns a date string with a language-sensitive timezone:

```
//We can get different date strings based on the timeZones
white.toLocaleString()        //13/1/2023, 13:54:00
white.toLocaleString("en-US") //1/13/2023, 1:53:44 PM

//we can also get DATE and TIME 
white.toDateString()              //Fri Jan 13 2023
white.toLocaleDateString("en-US") //1/13/2023
white.toTimeString()              //14:19:43 GMT+0100 (Ora standard dell’Europa centrale)
white.toLocaleTimeString("en-US") //2:19:43 PM

//on Date the en-GB shows 13/01/2023
```

<details>

<summary>.get() any timezone as a Date() Object</summary>

We can use the return from .toLocateString() for a new Date() object **but**:

<pre><code>//You will need to use only the english-American locale to avoid day/month switch 

new Date()        //Mon Feb 06 2023 11:47:36 GMT+0100
new Date(new Date().toLocaleString() ) //Fri Jun 02 2023 11:47:36 GMT+0200 
new Date(new Date().toLocaleString("en-US") ) //Mon Feb 06 2023 11:47:36 GMT+0100

//and the only (option) you can use is Timezone.

<strong>new Date(new Date().toLocaleString("en-US", {timeZone: "America/Chicago"}) )
</strong>//Mon Feb 06 2023 04:52:05 GMT+0100

</code></pre>

It's gonna be a Date() object, including all its methods .get()/.set()/etc

</details>

We use **new Intl.DateTimeFormat().format()** method to format a Date() according to the locale and formatting **option**:

```
//we set the state-language in the time format

let kiev= new Date()
//Sun Jan 15 2023 17:53:25 GMT+0100 (Ora standard dell’Europa centrale)

new Intl.DateTimeFormat('it-IT').format(kiev)
//15/1/2023

```

Both **toLocateString()** and **new Intl.DateTimeFormat().format()** share similar options **objects:**

{% tabs %}
{% tab title="toLocateString() options" %}
Only on .toLocateString() we can use "narrow" and "2-digit" values:

```
//2-digit will add the 0 for single-digit numbers

let more = {
    weekday: "narrow/short/long",	        // M/ Mon/ Monday

    day: "numeric/2-digit",                     // 2/ 02
    month: "numeric/2-digit/narrow/short/long", // 2/ 02/ F/ Feb/ February
    year: "numeric/2-digit",                    // 2023/ 23,
    
    hour: "numeric/2-digit",
    minute: "numeric/2-digit",
    second: "numeric/2-digit",
    
    timeZoneName: "short/long"         // Ora standard a Europei Centrale/ CET
}

new Date(new Date().toLocaleString("en-US", more) )

```

We can also .split(",") the data returned:

```
// it would be [weekday, date, hour timezone]

luni, 06.02.2023, 12:12:22 p.m. Ora standard a Europei de Vest
['luni', ' 06.02.2023', ' 12:12:22 p.m. Ora standard a Europei de Vest']

```
{% endtab %}

{% tab title="new Intl.DateTimeFormat().format() options" %}
```
let objects={
    timeZoneName: "long",        //15/01/2023, Central European Standard Time
    
    hour: 'numeric', 
    minute: 'numeric', 
    second: 'numeric',
    
    timeZone: "Europe/Kiev",    //19:08:41 (we limited to time, and a timezone)
    day: "numeric",             //15
    
    month: "short/long/numeric", //Jan/ January/ 1
    year: "numeric",             //01/2023 (1 in other languages)
    
    weekday: "short/long"        //Sun/ Sunday
    dayPeriod: "long"            //in the evening, only works for en-US
}

let kiev= new Date()
new Intl.DateTimeFormat('en-US', objects).format(kiev)

```

The split(",") will change based on the values of some options properties:

```
//depending if the month is a number/string

['Mon', ' 2/6/2023', ' 2:48:19 in the afternoon Eastern European Standard Time'] 
['Mon', ' February 6', ' 2023 at 2:49:23 in the afternoon Eastern European Standard Time']

```
{% endtab %}
{% endtabs %}

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




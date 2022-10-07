# JS 8

* Geolocation API

**HTML Geolocation API** allows the _browser_ to make an API **call** on the user position:

```
//we can deconstruct the position argument to get the COORDinateS

navigator.geolocation.getCurrentPosition( async ( {coords: {latitude, longitude}}) =>{

    console.log("We are at lat " + latitude + " and lon " + longitude) 

  }, showError
);

/we can add a callback function for all the possible errors

function showError(error) {

    if(error){
      switch(error.code) {
        case error.PERMISSION_DENIED:
          user.innerHTML = "User denied the request for Geolocation."
          break;
        case error.POSITION_UNAVAILABLE:
          user.innerHTML = "Location information is unavailable."
          break;
        case error.TIMEOUT:
          user.innerHTML = "The request to get user location timed out."
          break;
        case error.UNKNOWN_ERROR:
          user.innerHTML = "An unknown error occurred."
          break;
      }    
    }
}

```

We can use [Weatherbit API](https://www.weatherbit.io/api/weather-current) to fetch weather information:

{% tabs %}
{% tab title="await syntax" %}
```
//In order to use the API we need an base URL and a key 

let bit= "https://api.weatherbit.io/v2.0/current?lat=35.7796&lon=-78.6382&key=API_KEY&include=minutely"
let bitkey= "6669a6fd01c14380aac859c88b82b495"

let weatherbit= 
"https://api.weatherbit.io/v2.0/current?lat=" + latitude +"&lon=" + longitude +"&key=" + bitkey 

let response= await fetch(weatherbit)
let jason= await response.json()

console.log( jason.data[0] )    //to access weather info
```
{% endtab %}

{% tab title="Promise syntax" %}
```
let bit= "https://api.weatherbit.io/v2.0/current?lat=35.7796&lon=-78.6382&key=API_KEY&include=minutely"
let bitkey= "6669a6fd01c14380aac859c88b82b495"

let weatherbit= 
"https://api.weatherbit.io/v2.0/current?lat=" + latitude +"&lon=" + longitude +"&key=" + bitkey 

fetch( weatherbit )
  .then(response =>{
      return response.json()
  })
  .then(commits => {
      console.log( commits.data[0] )
  });

```
{% endtab %}
{% endtabs %}

We can also use [openweathermap ](https://openweathermap.org/api)API to fetch **cities with similar names**:

```
//when using this API for coordinates it ended up being less precise
//also this URL is specific for cities with similar names

let openkey= "dc439c07940578bea7bddf0375c7074c"
let cerca= `http://api.openweathermap.org/data/2.5/find?q=${citta}&appid=${openkey}`

let risposta= await fetch( cerca )
let jass= await risposta.json()

console.log( citta )

for(let x=0; x< jass.list.length; x++){
    console.log( jass.list[x] )
    console.log( jass.list[x].coord )
}

```

<figure><img src="../.gitbook/assets/findingsimila.PNG" alt=""><figcaption><p>Similar cities come with their weather and coordinates</p></figcaption></figure>

We can use the [Foursquare ](https://developer.foursquare.com/docs/places-api-getting-started)API to fetch **cities locations**:

{% tabs %}
{% tab title="location API" %}
```
//The second Key is just a string of random digits

let foursquare= 'https://api.foursquare.com/v2/venues/explore?near=';
let access= 'OZATO4USDMG5ZYJMKY54YTK4Y2TPDWK34PSUG3GIV0RULAXP';

let accesspri= 'OSXQJ50AFX20J2HNOHKFGPWKVVNYNSP13IOGOB5YETXLPLR4'; 

let cit= city.value    //name, we can also choose the &limit=__
const urlToFetch = `${foursquare}${cit}&limit=10&client_id=${access}&client_secret=${accesspri}&v=20180101`;

let response= await fetch(urlToFetch)
let commit= await response.json()
let lista= await commit.response.groups[0].items.map((item) => item.venue)
//To filter the metaData and items with venus

console.log( lista[0] )
console.log( lista[0].location )    //we get lan, lon position
console.log( lista[0].name  )       //we get the name of the place

console.log( lista[0].categories[0].icon.prefix )
console.log( lista[0].categories[0].icon.suffix  )    
//`${immagine.prefix}bg_64${immagine.suffix}` to shiw the icon

```
{% endtab %}

{% tab title="Api results" %}
<figure><img src="../.gitbook/assets/foursquare.PNG" alt=""><figcaption><p>from location we get .address </p></figcaption></figure>
{% endtab %}
{% endtabs %}






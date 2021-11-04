---
description: Syntax and properties
---

# CSS 1

* [Position, Canvas and Animation](css-1.md#position-canvas-and-animation)

## Get the css working

**In the HTML file by EMMET**

```sql
link 

<link rel="stylesheet" type="text/css" href="style.css">
```

**Cascading style sheets **files .CSS is organized in:

```sql
-external
<link rel="stylesheet" type="text/css" href="style.css">
(rel stands for relantionship)

-internal
Selectors {
  properties: values !important;
}

-inline
<p style="color:red"> indeed </p>

id# > class. > tag
```

Selectors mirror html tags for priority ( tag > id# > .class )

To adapt elements for different screens we use **media queries**:

```
@media only screen and (max-width: 400px) {
    .head > h1{
        font-size: 1.1em;
    }
    .type{
        font-size: 80%;
    }
    @keyframes left {
        0% { background-position: -805px 0; }
        100% { background-position: 0px 0; 
               left:5%;}
    }
}
  
//like we choose a smaller font for an H1, and the complete 
keyframe statuses
```

Also, the properties are inherited and so we have to change only the different ones.

### **Position, Canvas and Animation**

check this example:

{% embed url="https://codepen.io/misterlinux/project/editor/XrJJyL" %}
animation on html/css page
{% endembed %}

We used animations/transition/canvas and pixel art.

#### Transitions and animations

So, to modify CSS properties trought different states we can use:

```
.box{
  background: blueviolet;
  width: 200px;
  height: 150px;
  transition: 1s;
}

.box:hover{
  width: 300px;
}

//we transition in 1second the box's width from 200 to 300 
after a :hover, but we have to use transform to do more like rotate()

.type{
  border-right: .1em solid green;
  animation: color 1s step-end infinite;
}

@keyframes color{
  0% { border-color: green; }
  50% { border-color: transparent; }
  100% { border-color: green; }
}

//we animate the border to intermit in 1 second, infinite
some elements can't be animated
```

The syntax  is \[all]\[0.5s]\[ease] / **\[target]\[time]\[way]**.

We can use **Tranform** for more properties and multiple animations:

```
.box{
    background: blueviolet;
    width: 50px;
    height: 50px;
    transition: height 1s linear, width 4s ease-out, transform 3s;
    transition-delay: 1s
}

//to multiple or add a transform to a transition

.box:hover{
    width: 200px;
    height: 150px;
    transform: rotate(180deg);
}

```

To animate the sprite pixel art (and the text on the title) we used **steps()**:

```
animation: left 8s steps( 10,start) infinite;

steps( start, end)
```

which allows to divide the animating sequence in parts and choose which frame at the **start/end.**

To use pixel art we use it as **background**:

```
.rotate{
    background:url("image") 0 0 no-repeat;
    width: 32px;
    height: 64px;
    animation: example 8s steps(21) infinite;
}

background :url("./yosi3.png") -730px 0px;

//and we can choose the frame of start by changing x/y
```

and for our **animated text** and effect we had:

```
<h2 class="type"> This line is (almost) automatically animated </h2>

.type{
    white-space: nowrap;
    overflow: hidden;
    border-right: .1em solid green;
    
    transition: all 5s steps( 35, end);
    animation: color 1s step-end infinite;
}

//nowrap is to have it all text in one line
while hidden is to have the text limited by the starter width
(to then animate the final 30em width)

body:hover .type{
    width: 30em;
}

@keyframes color{
    0% { border-color: green; }
    50% { border-color: transparent; }
    100% { border-color: green; }
}
```

Here we have the text expanding, with steps for each letter, also for the buttons we can use the \~ :

```
first-sequence ~ second-sequence {
  /* property:value; */
}

//the 2 siblings tags dont have to be close, just in sequence
```

Instead of importing an image for the egg, we used **Canvas** to "code" the image:

```
<body>
    <canvas id="egg" width="300px" height="350px" style="border:solid transparent 2px">
    </canvas>

</body>

<script>
  var c = document.getElementById("myCanvas");
  var ctx = c.getContext("2d");
  ctx.beginPath();
  ctx.arc(100, 75, 50, 0, 2 * Math.PI);
  ctx.stroke();
</script> 

//we get the id, we set the "2d" and we beging tracing
we draw a circle with (Xposition, Yposition, Ray,
starting radiant, end radiant) we can also set rotation. 
```

we set up a space with the **\<canvas>** tag with id, width and height to then script the image in. For our exercise, we also needed to draw an ellipse:

```
inn.ellipse( 95, 100, 10, 15, 0, 0, Math.PI*2);

//half an ellipse with 10/15 as different axis measures
the 5^ value is for rotation
```

we can also script **gradients** for the canvas:

```javascript
var grd = ctx.createLinearGradient(0, 0, 200, 0);
grd.addColorStop(0, "red");
grd.addColorStop(1, "white");

//we create a gradient with x1,y1,x2,y2 and then we add the colors
in state 0 or 1, to then add it to the existing shape

ctx.fillStyle = grd;
ctx.fill();
```



```javascript
// This is the route we will need to update
router.get('/customers', function(req, res) {
  res.status(200).json({
    customers: [{
      id: 2,
      title: 'Mr',
      firstname: 'Laurie',
      surname: 'Ainley',
      email: 'laurie@ainley.com'
    }
  ]});
})
```

Who can tell me what this is currently doing? What do we need to make it do?

So, the answer is here:

```javascript
const sqlStatement = 'select * from customers';
knex.raw(sqlStatement).then(function (data) {
  res.json(data);
});
```

This is [the library](https://knexjs.org) we will use for building queries. `Knex` provides helpers for creating queries so we normally won't use `knex.raw` in real applications, but we will use it today to practice SQL more, and write "raw" SQL statements.

## Transition and transform

**Transition **is based on the starter and ending properties and will take action on change (hover in this case)



**User Acceptance test**: Complete the end-point `/customers/:id`, so that it extracts that customer information from the database, and replies back with that information as JSON.

* select and filter by id
* hint: simple select and filter by ID

STRETCH GOAL (OPTIONAL) : If you get a request of /customers/notanumber (anything that isn't a number) it should return an HTTP 400 bad request.

## LESSON 2 : LIKE, WHATEVER

So, now we're going to deal with one of the most common issues with hotel databases: the guest's name being misspelled.

So, "Hilary Clinten" is added to the database. She calls up on the phone asking about her reservation and spells her name correctly on the phone. The hotel staff knows what _some of her name_ sounds like but not all of it and they want to find her as a customer on the system.

For this problem where we want to search in _part_ of a string we use the LIKE command:

```sql
select * from customers where surname like '%lint%';
```

This returns all of the customers who have the name 'lint' in their names.

```sql
select * from customers where surname like '%clint%';
```

This won't return anything at all. Why not?

```sql
select * from customers where surname ilike '%clint%';
```

This will. Why?

## EXERCISE 2A

**User Story:** As a staff member I want to search for a customer through its `surname`, but we don't know that it might be misspelled.

**User Acceptance test**: Complete the end-point `/customers/:surname`, so that it extracts that customer information from the database, and replies back with that information as JSON.

* select and filter through like

## EXERCISE 3

**User Story:** As a guest, I want to register my details in the system so that I can check availability for my stay.

**User Acceptance test**: Take the data being POSTed to the `/customers` endpoint check it is inserted into the database.

STRETCH GOAL (OPTIONAL): If a bad request is made to customers - first name is missing, for instance, return an HTTP 400 Bad reqest.

## HOMEWORK 4

**Notes on Postman**

In the next image you can see Postman doing a POST request. Highlighed areas indicate the fields that need to be changed and/or information that needs to be added. The arrow points to a tab where you will need to set the type of content of this request. As denoted by the arrow legend, you will need to set `Content-Type` to `application/json`.&#x20;

**User Story:** As a guest, I noticed that there is a typo on my details and wish to correct it.

**Use Case**: I go to '/customers/:id' endpoint and send updated data for each customer parameter: title, firstname, surname.

**User acceptance test**: PUT title=mr, firstname=donald, surname=trump on /customers/:id and check that the database was updated.

* update table
* remember your previous lesson
* hint: in the javascript code, instead of db.all() you will need ... what?

## HOMEWORK 4 : STRETCH GOAL (OPTIONAL)

The end point should properly detect which customer properties are being updated, and generate the appropriate SQL update statement.

## HOMEWORK 5

**User Story:** As a staff member, I want to create a new reservation.

Create and end-point to post a new reservation to `/reservations/`.

* insert into
* create the endpoint from scratch
* which HTTP method should you use?

STRETCH GOAL (OPTIONAL) : Return {"status": "error": "reason": "reason..."} if _anything_ was wrong with the request.

## LESSON 6 : I WISH I COULD DELETE HIM IN REAL LIFE

We've currently done inserting data and updating data, but sometimes inserting data was just a mistake and it needs to go.

It's a fairly simple command that looks like select, you just specify the table and a predicate and it wipes:

```sql
delete from customers where surname ilike '%trump%';
```

There are several things you need to worry about when you delete data and what you do about them will depend entirely upon what it is you are trying to do:

* What happens to the data that depends upon the data you deleted? -- What if Trump had a reservation? Either a) delete the reservation as well? or b) raise an error and force the user to delete the data manually if they really want him gone.
* What if you want to undo the deletion?
* What if you want to mark some data as deleted but you might still want to refer to it?
* Often it's a good idea to give data the 'status' deleted instead of actually deleting it.

## HOMEWORK 6

**User Story:** As a staff member, I want to delete a canceled reservation from the database.

**Notes on Postman** The delete request is actually pretty straight forward. We only need to select the type and provide the url:&#x20;

Create an end-point to delete a given reservation from `/reservation/:id/`.

* delete

## HOMEWORK 7

**User Story:** As a staff member, I want to get a list of all the existing reservations.

Create an end-point to get from `/reservations` all existing reservations.

* create the endpoint from scratch

## HOMEWORK 8

**User Story:** As a customer, I want to check the details of a reservation.

Create and end-point to get from `/reservations/:id` the details of a resrevation through its `id`.

* simple filtering
* create the enpoint from scratch

## HOMEWORK 9

**User Story:** As a staff member, I want to get a list of all the reservations that start at a given date.

Create and end-point to get from `/reservations/starting-on/:startDate` all the reservations that start at a given date.

* simple filtering
* create the enpoint from scratch

## HOMEWORK 10

**User Story:** As a staff member, i want to get a list of all the reservations that are active at a given date.

Create and end-point to get from `/reservations/active-on/:date` all the reservations that are active on a given date - some customer has a room reserved on that day.

* multiple filtering.
* create the enpoint from scratch

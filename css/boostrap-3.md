# Boostrap 3

* uno
* due
* tre

**Offcanvas** are hidden sidebars we can trigger with buttons:

```
//we need data-bs-target="#" or href="#" to trigger the hidden sidebar(offcanvas)

<button class="btn btn-warning" data-bs-toggle="offcanvas" data-bs-target="#ecco">
  Sinistra
</button>

//we need offcanvas and -(position) to choose where it's gonna appear, data-bs-backdrop="false" is to disable the close-offcanvas on click outside the sidebar
//while data-bs-scroll true is to limit the scoll on only the sidebar
<div id="ecco" class="offcanvas offcanvas-start" data-bs-scroll="true" data-bs-backdrop="false" >
  <div class="offcanvas-header ">
    <h3>This sidebar </h3>
    
//to add a close botton we just use -dismiss="offcanvas"
    <button class="btn-close" data-bs-dismiss="offcanvas"></button>
  </div>
  <div class="offcanvas-body">
    <p> So, we can implement limit scoll and close on click outside offcanvas </p>
  </div>
</div>

```

![](../.gitbook/assets/SIDEBAR.PNG)

### ScrollSpy Implementation

We can use the scrollSpy component even without Boostrap:

{% embed url="https://codepen.io/misterlinux/pen/zYRyLKP?editors=1100" %}
ScrollSpy with simple JS
{% endembed %}

A scollSpy is structured in a **menu** and a **content scroll:**

```
//in 2 columns

<div class="pure-g">
<div class="pure-u-1-2 ">
  <menu>
    <nav id="select" class="navbar flex-column p3 align-items-stretch">
      <a class="navbar-brand" href="#">Navbar</a>
      
<!-- nav-pills is what allows the active to have the color background -->      
      <ul class="nav nav-pills flex-column">
        <li class="nav-item active">
        
<!--a class for the <a> links isnt needed -->
          <a href="#uno" >Lorem.</a>
        </li>
        <li class="nav-item">
          <a href="#due">Lorem.</a>
        </li>
        <li class="nav-item">
          ...
        </li>
        
//we just added margin to group the nav-item
        <li class="nav-item ms-3">
          <a href="#cinc">margin items</a>
        </li>
      </ul>
    </nav>
  </menu>
</div>

//here we have the scroll content, each section has a menu-item correspondant
<div class="pure-u-1-2 contenuto" id="serve">
  <div>
    <section>
      <h2 id="uno">Id on title</h2>
      <p>
        So, the text on menu click is based on href="#" link and id on title
      </p>
    </section>
    <section>
      ...
    </section>

  </div>
</div>

</div>

```

For the Javascript we need to use the .**active** bootsrap class on click:

```
//we select each menu-item 

let eccoli = document.querySelectorAll("menu li")

//foreach on click we REMOVE the active class from each one, then we add ONLY to the clicked element
eccoli.forEach(i=>{
  i.onclick = (()=>{
      eccoli.forEach(o=> o.classList.remove("active"))
      i.classList.add("active")
  })
})

```

On scroll you need to use [**\_\_.getBoundingClientRect().y**](https://www.w3schools.com/jsref/tryit.asp?filename=tryjsref\_element\_getboundingclientrect):

```
//We don't scroll the entire page so we need a selector for the scroll section we use
let scrolling = document.getElementById("serve")

//and we need another one for the singular paragraphs
let singoli = document.querySelectorAll("#serve section")

scrolling.addEventListener("scroll", ()=>{

//while we scroll the selected for each of the paragraph we get the position relative to the viewpoint
  singoli.forEach((x,y)=>{
    let space = x.getBoundingClientRect().y

//we check x.getBoundingClientRect at the start and at the end of the scroll to calculate
//the space for each paragraèh using also the window.innerHeight
    if(space < window.innerHeight - 456){
    
//here we just do the same as with the click function
      eccoli.forEach( z=> z.classList.remove("active"))
      eccoli[y].classList.add("active")
    }
  })
})

```

### Toast and pop-up messages

With toast, we can add close-ready messages to the page:

```
//Toast structure is similar to card

<div class="toast-container position-fixed bottom-0 end-0 me-3 mb-3">
//to keep the toast fixed on the border of the page we just need position-fixed, it needs
//a coordinates to be visible, bottom/start/end/top

  <div class="toast show hide" id="tuno">
//we include the .hide for js toggle later, toaster-header will have a bottom-border from body
  
      <div class="toast-header">
        <h5>This is the left </h5>
        <button class="btn-close ms-auto" id="ecco1"></button>
      </div>
//like organizing grid in the toast
      <div class="toast-body pure-g">
        <div class="pure-u-1-3">
          <img src="https://bit.ly/3tvUtWn" class="pure-img">
        </div>
        <div class="pure-u-2-3 p-3">
          <p>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Iusto esse maiores, id a delectus quisquam!</p>
        </div>
      </div>
 
  </div>
</div>

```

For the JS we can toggle the .**show** .**hide:**

```
//with .toggle we can add .show if absent OR remove it if present
let ecco = document.getElementById("ecco")
let ecco1 = document.getElementById("ecco1")

function hiding(dele){
  dele.classList.toggle("show")
}

ecco1.onclick=(()=>{
  hiding(ecco)
})

```

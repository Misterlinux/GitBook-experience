# Boostrap 3

* [ScrollSpy](boostrap-3.md#scrollspy-implementation)
* [Toast & Pop-up](boostrap-3.md#toast-and-pop-up-messages)
* [Responsive and toggle Navbar](boostrap-3.md#responsive-navbar-with-toggle-button)
* [Complete responsive page](boostrap-3.md#complete-responsive-page)

The **bootstrap grid** layout is based on flexbox, it uses **container**, **row**, and **col**(uns).

The width is organized into **12 units**, **col**umns can be **auto** to adapt to their content.

The bootstrap **col**umns implement media query **breakpoints** using **sm**,**md**, and **lg**.&#x20;

```
//we can use container-fluid to remove the container default margins
//the breakpoints will take effect AFTER the md, from a 1-5-auto to 3-3-3 on smaller

<div class="container">
  <div class="row">

    <div class="col-3 bg-warning col-md-1">
      Lorem
    </div>
    <div class="col-3 bg-danger col-md-5">
      Lorem
    </div>
    <div class="col-3 bg-success col-md-auto">
      L
    </div>
    
  </div>
</div>

```

<figure><img src="../.gitbook/assets/bootstrap1.png" alt=""><figcaption><p>Grid columns on md and not-md screen</p></figcaption></figure>

We can **align** and **justify col** elements in the container:

<details>

<summary>Bootstrap align and justify</summary>

For **align** we need extra classes and CSS height:

```
.altezza{
    height: 7em;
    background-color: lightcoral;
}

//we need flex on container to align on row to work, also on content inside
<div class="container d-flex altezza">
  <div class="row align-items-center">

    <div class="col-3">
      One of three columns
    </div>
    <div class="col-3">
      One of three columns
    </div>
    <div class="col-3">
      One of three columns
    </div>
    <div class="col-3 align-self-start">
      Another column
    </div>
    
  </div>
</div>

```

While **justify** doesn't need extra CSS.

```
//it uses between/around to justify space them
<div class="container ">
  <div class="row justify-content-end">

    <div class="col-2">
      lorem
    </div>
    <div class="col-2">
      lorem
    </div>
    <div class="col-2">
      lorem
    </div>

  </div>
</div>

```

</details>

<figure><img src="../.gitbook/assets/alignjustify.PNG" alt=""><figcaption><p>align-items and justify-content</p></figcaption></figure>

We create a **break-line** between columns using a div with **w-100**:

<details>

<summary>break-line and breakpoints</summary>

Without the div the columns share space:

```
<div class="container">
  <div class="row">

    <div class="col-4 bg-primary">social skill</div>
    <div class="col-4 bg-warning">social skill</div>

    <div class="w-100"></div>

    <div class="col-3 bg-danger">Soldier </div>
    <div class="col-3 bg-success">Soldier </div>

  </div>
</div>

```

We use d-block to remove the break-line on breakpoint.

```
//will be removed on sm, while display on md after
<div class="container">
  <div class="row">
    <div class="col-4 bg-primary">social skill</div>
    <div class="col-4 bg-warning">social skill</div>

    <div class="w-100 d-none d-md-block"></div>

    <div class="col-3 bg-danger">Soldier </div>
    <div class="col-3 bg-success">Soldier </div>

  </div>
</div>


```

</details>

<figure><img src="../.gitbook/assets/breakpointbootstrap.PNG" alt=""><figcaption><p>w-100 breakpoint and without</p></figcaption></figure>

We can use **m**argin-auto to space columns on the sides.

<details>

<summary>margin-auto guide</summary>

We use **mr/ml-auto** to push the column to a side.

```
//the second push the third to the max right
<div class="row my-1">

  <div class="col-3 bg-primary">
    Lorem
  </div>
  <div class="col-3 mr-auto bg-warning">
    Lorem
  </div>
  <div class="col-3 bg-danger">
    Lorem
  </div>
  
</div>
```

We can use **m**argin on **breakpoints**, but we need to nullify it first.

```
//on md and after we push it to the right, after null the ml, on below md
//we push it to the left  
<div class="row">

  <div class="col-4 col-md-6 bg-primary">
    Bordato
  </div>
  <div class="col-4 ml-auto col-md-1 mr-md-auto ml-md-0 bg-warning">
    Bordato1
  </div>

</div>
```

</details>

<figure><img src="../.gitbook/assets/marginbreak.png" alt=""><figcaption><p>mr-md-auto and ml auto on media query</p></figcaption></figure>

We can use **offset** to move them by column **units** to the right.

<details>

<summary>Bootstrap offset guide</summary>

On **md**, we increase the offset on the smaller col, below md we invert offset and columns width to alternate between,&#x20;

```
<div class="container">

  <div class="row">
    <div class="col-3">sound register </div>
    <div class="col-3 offset-3">sound register </div>
  </div>

  <div class="row">
    <div class="col-3 offset-3 col-md-1 offset-md-2">sound register </div>
    <div class="col-3 offset-3 col-md-1 offset-md-5">sound register </div>
  </div>

</div>
```

</details>

When **nesting rows** we keep the 12 units width.&#x20;

<details>

<summary>Row nesting guide</summary>

We need a **row** class on the container.

```
<div class="container m-2">

  <div class="row">
    <div class="col-3 bg-primary d-flex justify-content-center align-items-center text-white">
      nesting 
    </div>

    <div class="col-8 bg-warning">
      nesting 

      <div class="row">
        <div class="col-8 bg-secondary">
          nested 
        </div>
        <div class="col-3 bg-danger">
          nested 
        </div>
      </div>
    </div>
  </div>

</div>

```

</details>

<figure><img src="../.gitbook/assets/rownesting.PNG" alt=""><figcaption><p>row nesting with align/justify flex</p></figcaption></figure>

### Scrollspy and getBoundingClientRect()

The **getBoundingClientRect()** method returns an object, its properties provide the **position** relative to its **viewpoint** and the **size** of the viewpoint, with the position value updated on **scroll**.

```
//we get x7y position or width/height

highlight.getBoundingClientRect().y/x/height/width
```

<details>

<summary>getBoundingClientRect() as scrollspy guide</summary>

In the HTML we need **nav-pills** (on containers to have the active bg color on nav-items child elements):

```
<div class="col-md-6 ">
  <nav class="navbar bg-light ">

    <nav class="nav nav-pills bounding flex-column w-100">

      <a href="" class="navbar-brand ms-2">Navbrand</a>
      <a href="" class="nav-link">
        Read text
      </a>

      <div class="nav-link text-dark">
        Top position:
        <p id="movimento"></p>
        Scroll window height:
        <p id="altezza"></p>
        Scroll window width:
        <p id="larghezza"></p>
      </div>

    </nav>

  </nav>
</div>
<div class="col-md-6 bounding" id="window">

  <p class="obiettivo" >
    We use <b>getBoundingClientRect()</b> to link specific js functions 
    to a specific scrolling position of the element, the starting 
    top value is given by the navbar
  </p>

</div>

//while on CSS we need the overflow scroll and a height
.bounding{
    position: relative;
    height: 400px;
}

.row .bounding:nth-child(2){
    overflow-y: scroll;
}

.bounding > p{
    padding: 600px 0 100px 0;
}

```

The starter **x/y** value of **getBoundingClientRect**() includes all the other elements on the page, we add **active** to the nav-link:

```
//we first select the window, the element to scroll to, and the nav-link
let finestra= document.getElementById("window")
let highlight = document.querySelector(".obiettivo")
let navi= document.querySelectorAll(".bounding .nav-link")

let visible = highlight.getBoundingClientRect().y
movimento.innerText= visible

finestra.addEventListener("scroll", ()=>{

  visible= highlight.getBoundingClientRect().y
  movimento.innerText= visible

  altezza.innerText= highlight.getBoundingClientRect().height
  larghezza.innerText= highlight.getBoundingClientRect().width

  if( visible< -70){
      navi[0].classList.add("active")
  }else{
      navi[0].classList.remove("active")
  }
  
})

```

To **calculate** where an element be **visible** we:

```
//We take the total top distance (padding-top) - window height - starting top
// 600px - 400px - 131px= -70px (we scrolling down so we need negative)

```

The problem is that the getBoundingClientRect().x/y value changes on **smaller screens**.

</details>

<figure><img src="../.gitbook/assets/boundingrect.png" alt=""><figcaption><p>Using <strong>getBoundingClientRect()</strong> for navlist-item <strong>scrollspy</strong></p></figcaption></figure>

Being getBoundingClientRect() an **object** we can get all its **properties** with a loop:

```
let bounded = highlight.getBoundingClientRect()

for( const xx in bounded ){
    console.log( xx + " : " + bounded[xx] )
}
```

The **scrollspy Bootstrap component** updates **nav-item** elements based on **scroll position**.

<details>

<summary>Column navbar and scrollspy guide</summary>

For the column navbar, we need, an **ID** target, **flex-column** for the navbar direction, and **align-items-stretch** for the width (due to the inverted flex-direction).

```
//also we need h-100 for the flex to cover the height
<div class="col-4">

  <nav id="colonna" class="h-100 flex-column align-items-stretch pe-4 border-end mt-4">
    <nav class="nav nav-pills flex-column">

    <a href="" class="navbar-brand my-2">Navbrand </a>
      <a class="nav-link" href="#item-1">Item 1</a>
      <nav class="nav nav-pills flex-column">
        <a class="nav-link ms-3 my-1" href="#item-1-1">Item 1-1</a>
        <a class="nav-link ms-3 my-1" href="#item-1-2">Item 1-2</a>
      </nav>
      <a class="nav-link" href="#item-2">Item 2</a>
      <a class="nav-link" href="#item-3">Item 3</a>
      <nav class="nav nav-pills flex-column">
        <a class="nav-link ms-3 my-1" href="#item-3-1">Item 3-1</a>
        <a class="nav-link ms-3 my-1" href="#item-3-2">Item 3-2</a>
      </nav>
    </nav>
  </nav>

</div>
```

On the **scroll** div, we need **data-bs-spy="scroll"** and **data-bs-target** for the navbar id.

Each time an element **ID** referenced by a **nav-link href** is scrolled into view, the nav-link gets .**active** .

```
<div class="col-8">

  <div data-bs-spy="scroll" data-bs-target="#colonna" data-bs-smooth-scroll="true" 
  class="scrolling">
    <div id="item-1">
      <h4>Item 1</h4>
      <p>
        Lorem ipsum dolor sit amet consectetur adipisicing elit. 
        Distinctio maiores saepe quis nihil ab esse consequuntur 
        recusandae expedita inventore unde alias, atque qui possimus 
        eum enim quas magni quibusdam id.
      </p>
    </div>
    <div id="item-1-1">
      <h5>Item 1-1</h5>
      <p>
        ...
      </p>
    </div>
    <div id="item-1-2">
      <h5>Item 1-2</h5>
      <p>
        ...
      </p>
    </div>
    <div id="item-2">
      <h4>Item 2</h4>
      <p>
        ...
      </p>
    </div>
    <div id="item-3">
      <h4>Item 3</h4>
      <p>
        ...
      </p>
    </div>
    <div id="item-3-1">
      <h5>Item 3-1</h5>
      <p>
        ...
      </p>
    </div>
    <div id="item-3-2">
      <h5>Item 3-2</h5>
      <p>
        ...
      </p>
    </div>
  </div>
  
</div>

```

</details>

<figure><img src="../.gitbook/assets/scrollspy8.gif" alt=""><figcaption><p>Scrollspy on column navbar</p></figcaption></figure>

For more **navbar layout** check the complete project:

{% embed url="https://codepen.io/misterlinux/pen/zYRyLKP" %}
navbar scrollspy page
{% endembed %}

### Toast cards and functions

The **toast** component mimics the push notifications, composed of a **toast** and **show** class, **toast-header, toast-body,** and a **data-bs-dismiss="toast"** to close it.

We use **position-fixed** to have the toast always visible and position with **top/bottom/start/end-0/50**.

```
//The toast-body has a level of transparency, and add some margin and position

<div class="toast-container position-fixed bottom-0 start-0 ms-2 mb-2">
  <div class="toast show " style="z-index: 2;">

    <div class="toast-header">
      <img src="https://bit.ly/3tvUtWn" class=" me-2 img-fluid w-25">
      <strong class="me-auto">Header bold</strong>

      <small>small text</small>
      <button type="button" class="btn-close" data-bs-dismiss="toast"></button>
    </div>

    <div class="toast-body">
      In order to be always visible we use position fixed
    </div>

  </div>
</div>

```

<figure><img src="../.gitbook/assets/Toast.PNG" alt=""><figcaption><p>Toast with margin and position</p></figcaption></figure>

For multiple overlaying toasts and **position-static** on the **toast-container**:

<details>

<summary>Toast-container and position-static guide</summary>

We can't use absolute positions like with static position, so we need an extra parent to **d-flex** and **justify** the toast-container,&#x20;

```
//The toast-container adds margins to the toasts

<div class="d-flex justify-content-center">
  <div class="toast-container position-static mb-2">

    <div class="toast show" role="alert">
      <div class="toast-header">
        <img src="https://bit.ly/3tvUtWn" class="rounded me-2 w-25">
        <strong class="me-auto">Overlaying</strong>

        <button type="button" class="btn-close" data-bs-dismiss="toast" ></button>
      </div>
      <div class="toast-body">
        toast-container adds a margin 
      </div>
    </div>
  
    <div class="toast show" role="alert">
      <div class="toast-header">
        <img src="https://bit.ly/3tvUtWn" class="rounded me-2 w-25">
        <strong class="me-auto">toast bootstrap</strong>
        
        <small class="text-muted">2 seconds ago</small>
        <button type="button" class="btn-close" data-bs-dismiss="toast" ></button>
      </div>
      <div class="toast-body">
        between the 2 toasts
      </div>
    </div>

  </div>
</div>

```

1

1

1

1

</details>

<figure><img src="../.gitbook/assets/justifyfixed.PNG" alt=""><figcaption><p>fixed-position, overlay and justify toasts</p></figcaption></figure>

We use extra **js** to **trigger a toast** on **button** click, triggered toats will close after a delay.

<details>

<summary>Toast button trigger</summary>

Triggered toast will automatically close, you can add **data-bs-delay=""** to change the delay or use **data-bs-autohide="false"** to keep it open.

```
//we also need an ID for the toast and the button
<div class="my-4">
  <button type="button" class="btn btn-primary" id="liveToastBtn">Show live toast</button>
</div>

<div class="toast-container position-fixed bottom-0 start-0 ms-3 mb-3">
  <div id="liveToast" class="toast" data-bs-autohide="false" >
    <div class="toast-header">
      <img src="https://bit.ly/3tvUtWn" class="w-25 img-fluid me-2">
      <strong class="me-auto">Toast on button</strong>

      <button type="button" class="btn-close" data-bs-dismiss="toast"></button>
    </div>
    <div class="toast-body">
      we used new bootstrap.Toast() on js
    </div>
  </div>
</div>

```

On **Js** we use the button and toast **ID**:

```
//we create a Toast() instance and then trigger the show() method
const toastTrigger = document.getElementById('liveToastBtn')
const toastLiveExample = document.getElementById('liveToast')

toastTrigger.addEventListener('click', () => {
    const toast = new bootstrap.Toast(toastLiveExample)
  
    toast.show()
})

```

</details>

<figure><img src="../.gitbook/assets/toastbottone.png" alt=""><figcaption><p>Toast on button click</p></figcaption></figure>

We can use **external buttons** to **close** a toast:

```
//we need a target and a dismiss on the button
<div class="toast-container top-0 start-50">
  <div class="toast show" id="chiude">

    <div class="toast-body">
      Just a body without a dismiss button
    </div>

  </div>
</div>

<div class="position-fixed top-0 end-0">
  <button type="button" class="btn btn-primary top-0 start-50" data-bs-dismiss="toast" data-bs-target="#chiude">
    close 
  </button>
</div>

```

1

1

1

1

### Bootstrap offcanvas&#x20;

**Offcanvas** are hidden sidebars, using **data-bs-toggle** and **data-bs-target** on the button**.**

While using **offcanvas** and **offcanvas-(position)** on the content.

```
//like modal it also can use data-bs-backdrop, we can close with data-bs-dismiss
//data-bs-scroll is for the scroll to work only on open offcanvas.

<button class="btn btn-warning" data-bs-toggle="offcanvas" data-bs-target="#ecco">
  Sinistra
</button>

<div id="ecco" class="offcanvas offcanvas-start" data-bs-scroll="true" data-bs-backdrop="false" >
  <div class="offcanvas-header ">
    <h3>This sidebar </h3>
    
    <button class="btn-close" data-bs-dismiss="offcanvas"></button>
  </div>
  <div class="offcanvas-body">
    <p> So, we can implement limit scroll and close on click outside offcanvas </p>
  </div>
</div>

```

![](../.gitbook/assets/SIDEBAR.PNG)

**1**

**1**

**1**

### Responsive navbar with Toggle button

Check the final result:

![](<../.gitbook/assets/Navbardrodowning (1).PNG>)

```
//navbar includes the justify-content-between, while bg gives it the background-color

<nav class="navbar bg-warning">
        <div>
//navbar-brand will be thicker and no text-link decoration
            <a href="" class="navbar-brand ps-3">Lorem.</a>
        </div>

//the navbar will be not displayed on smaller screens
        <div class="d-sm-flex d-none align-items-center">
//align-items in the container class is to vertically align the children tags

            <div class="mx-1">simao.</div>
            <div class="pure-menu-item pure-menu-has-children pure-menu-allow-hover pe-0">
                <a href="" class="pure-menu-link">SCROLL </a>
                <ul class="pure-menu-children">
                    <li class="pure-menu-item">
                        <a href="" class="pure-menu-link">daje</a>
                    </li>
                    <li class="pure-menu-item">
                        <a href="" class="pure-menu-link">daje</a>
                    </li>
                    <li class="pure-menu-item">
                        <a href="" class="pure-menu-link">allargato may</a>
                    </li>
                </ul>
            </div>
    
//In a navbar we need to have the .dropdown class in the container tag
            <div class=" dropdown">
                <a class="nav-link dropdown-toggle" data-bs-toggle="dropdown" href="#">Tutorials</a>
                <div class="dropdown-menu">
                    <a class="dropdown-item" href="#">Microsoft</a>
                    <a class="dropdown-item" href="#">JAVA</a>
                    <a class="dropdown-item" href="#">Database</a>
                </div>
            </div>

//for form we will need d-flex in the container tag
            <div>
                <form class="pure-form d-flex">
                    <input type="text" class="pure-input" placeholder="search">
                    <button class="pure-button btn-sm">try </button>
                </form>
            </div>
    
        </div>

```

We won't use Navbar-expand- and instead will create a new **navbar-toggle:**

![](../.gitbook/assets/Navbartoggled.PNG)

For the code:

```
//for it to be visible only on a small screen we d-block AND d-sm-none

        <div class="d-block d-sm-none">
            <button class="navbar-toggle" data-bs-toggle="collapse" 
            data-bs-target="#tuo">
                <span class="navbar-toggler-icon"></span>
            </button>
        </div>

//the .collapse class keeps the content hidden until the data-bs-toggle="collapse is clicked
//and the navbar-collapse has the id = data-bs-target
            <div class="navbar-collapse collapse text-dark" id="tuo">
            <ul class="navbar-nav text-center">
                <li class="nav-item"><a href="" class="nav-link"> COSETTE </a></li>
                <li class="nav-item"><a href="" class="nav-link">Ipsam?</a></li>
                <li class="nav-item"><a href="" class="nav-link">Quo.</a></li>
                <li class=" mx-auto ">
                    <form class="pure-form d-flex">
                        <input type="text" class="pure-input" placeholder="search">
                        <button class="pure-button">try </button>
                    </form>
                </li>
            </ul>
        </div>  
</nav>

```

### Complete responsive page

Check this exercise:

{% embed url="https://codepen.io/misterlinux/pen/zYRVoMe" %}

About the responsive Layout:

```
//The HTML structure is based of:

<div class="pure-g">

    <div id="nav" class="pure-u-1">
    </div>

    <div id="list" class="pure-u-1">
        <p>column is here</p>
    </div>

    <div id="main" class="pure-u-1">
        <p>main text is here</p>
    </div>
</div>

```

{% tabs %}
{% tab title="Mobile" %}
![](../.gitbook/assets/ONeof1.PNG)

The CSS code is:

```
// The Nav needs width/height and no position

#nav{
    background-color: orange;
    height: auto;
    width: 100%;
}

```
{% endtab %}

{% tab title="MD creen" %}
![](../.gitbook/assets/Oneof2.PNG)

For CSS we:

```
//#nav, #list and #main will have fixed position, overflow is for the scrollable content og List

#nav, #list, #main {
    position: fixed;
    overflow: auto;
}

//with height 100% we occupy the entire page with sidenav
#nav {
    width: 9.5em;
    height: 100%;
}

//the margin is the width occupied by the sidebar
#list {
    margin-left: 9.5em;
    width: 90% ;
    height: 33%; 
    border-bottom: 1px solid #ddd;
}

//is top-position is the height occupied by the #list, we need bottom if we want it to cover the Y space
//and we include the sidenav margin
#main {
    top: 33%;
    bottom: 0; 
    left: 9.5em;
    background-color: violet;
}

```
{% endtab %}

{% tab title="Lg screen" %}
![](../.gitbook/assets/Oneof3.PNG)

For the large screen CSS:

```
//we make columns with height

#list {
    width: 22em;
    height: 100%;
    border-right: 1px solid #ddd;
}

//we use static position to avoid the previous media-query positioning
#main {
    position: static;
    margin-left: 30em;
    bottom: 0;
}

```
{% endtab %}
{% endtabs %}

About every single layout now:

![](../.gitbook/assets/StickyIMAGE.PNG)

```
//For the sticky scrollable image:

<div class="splash-container d-block d-sm-none w-100">
    <div class="splash text-center text-dark">
        <h1 class="splash-head py-2">Big Bold Text</h1>
        <p class="splash-subhead mb-1">
            Lorem ipsum dolor sit amet, consectetur adipisicing elit.
        </p>
        <a href="http://purecss.io" class="btn btn-primary">Get Started</a>
    </div>
</div>

//height is for the image BUT the space is given by the margin between fixed elements
.splash-container {
    background: url("https://..");
    height: 54%;
    position: fixed !important;
}
//we can create the space with absolute and top position
#main{
    position: absolute;
    top: 46%;
}

//For the overlay-image content we need absolute and left/right position to center it
//width reduces the space occupied by the content to avoid spreading with margin

.splash {
    width: 55%;
    margin: auto;
    position: absolute;
    top: 100px;
    left: 0; 
    right: 0;
}

```

For the image grid with bottom-shadow + author link and Footer:

![](../.gitbook/assets/IMAGEFOOTER.PNG)

```
//for each row of images we use the pureCSS grid
//To keep different images at the same height we add to use extra CSS inside the img tag

<div class="image pure-u-1-3">
    <a href="">
      <img class="pure-img riga" src="https://..">
    </a>

    <aside><span>by
        <a href="http://..">
          Dillon McIntosh
        </a></span>
    </aside>
</div>

<div class="texto pure-u-2-3 p-3 riga">
    <h4>This is the odd space </h4>
    <p>A collection of beautiful photos gathered from Unsplash.com.</p>
</div>

//first we need each image to be relative (to then apply the effect)
.image{
    position: relative;
}
//in case we want to stretch images in the grid layout
.image img{
    width: 100%;
}

//with position absolute we can be inside the image, bottom and text align give the text position
//while width 100% will cover the entire single image
.image aside {
    position: absolute;
    bottom: 0;
    padding: 0.5em 0.5em;
    color: white;
    width: 100%;
    font-size: 80%;
    text-align: right;

/*the shadow can be done with abackground, the top(white) from the black 90% down*/
    background: -webkit-linear-gradient(top,#ffffff00 0%,rgba(12,2,2,0.7) 100%);
}

//For the footer

<div class="footer text-center bg-dark p-2 w-100">
  <div class="pure-u-1">
      View the source of this layout to learn more. Made with love by the Pure
      Team.
  </div>
</div>

//fixed position and bottom will stick the footer and not be moved by scroll
.footer {
    color: orange;
    font-size: 70%;
    bottom: 0;
    position: fixed;
}

```

For the **scrollable messages** in the **MD** screen:

![](../.gitbook/assets/Scrollable-messages.PNG)

```
// we used CSS on the images and grid on the text

<div class="px-3 py-2 border-bottom border-warning email-item-selected pure-g">
    <div class="pure-u">
        <img width="64" height="64" class="me-2" src="https://bit.ly/3LkXD5I">
    </div>

    <div class="pure-u-3-4">
        <h6 class="email-name mb-0"> Hello from Toronto </h6>
        <p class="email-subject mb-0"> Article by 
            <a class="email-author">Minimister </a> about 
            <button class="btn btn-success btn-sm text-sm"> CSS </button> & 
            <button class="brn btn-danger btn-sm"> Js </button>
        </p>
        <p class="email-desc">
            Hey, I just wanted to check in with you from Toronto. I got here earlier today.
        </p>
    </div>
</div>

//The email-name is UpperCase , and email-subject is font-size 90%, for btn we just added
.btn-danger:hover{
    text-decoration: underline;
}

```

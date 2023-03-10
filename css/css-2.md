# CSS 2

[Image gallery and Carousel](css-2.md#image-galleries-and-carousels)

* [Dropdown and Hover effects](css-2.md#dropdown-and-hover-on-navbars)

### Flex and page layout

The **Flex**box layout justifies and aligns items within its container, while flex child elements expand their width to **fill it dynamically**.

```
//we still need to set height, and justify won't change how the space managed
<div class="stripe">
    <div></div>
    <div></div>
    <div></div>
    <div></div>
    <div></div>
</div>

.goleador{
    display: flex;
    height: 50px;
    flex-direction: column/ row;
}

.goleador div:nth-child(odd){
    flex: 1;
    background-color: purple;
}

.goleador div:nth-child(even){
    flex: 1;
    background-color: yellow;
}

```

<figure><img src="../.gitbook/assets/flexjustify.PNG" alt=""><figcaption><p>column and row</p></figcaption></figure>

{% tabs %}
{% tab title="Sidebar-flex" %}
To justify/align any element we need to use **display:flex each time**, also position will follow the HTML layout:&#x20;

```
//The height will be given by the content
<div class="contiene">
    <div class="sidebar">

    </div>
    <div class="stuff">

    </div>
</div>
<div class="footer">

</div>

```

In the CSS we use **Flex** to dynamically set the **width** of each div:

```
//Flex-direction is row by default
.contiene{
    display: flex;
}

//1/5 of flex for sidebar while 4/5 for stuff
.contiene .sidebar{
    flex: 1;
    background-color: brown;
}

.contiene .stuff{
    display: flex;
    flex: 4;
    justify-content: center;
    background-color: orange;
}

//while footer can just be
.footer{
    display: flex;
    background-color: black;
}

```

<figure><img src="../.gitbook/assets/fullflex.PNG" alt=""><figcaption><p>full page flex layout</p></figcaption></figure>
{% endtab %}

{% tab title="Navbar-flex" %}
We use **flex-direction column** for the navbar to be on the top, and use **flex to change the list** direction:

```
//from default row to column direction
.contiene{
    flex-direction: column;
}

//The list is now flex-direction row
.contiene .sidebar ul{
    display: flex;
}

```

<figure><img src="../.gitbook/assets/middle.PNG" alt=""><figcaption><p>smaller page layout</p></figcaption></figure>
{% endtab %}

{% tab title="Mobile-navbar flex" %}
For smaller screens we just set the navbar as column **on column flex container**:

```
//we need to use display: flex for it to work
.contiene .sidebar ul{
  display: flex;
  flex-direction: column;
}

```

<figure><img src="../.gitbook/assets/flexlittle.PNG" alt=""><figcaption><p>mobile pae layout</p></figcaption></figure>
{% endtab %}
{% endtabs %}

The values for both **justify-content** and **align-items**:

```
flex-start | flex-end | center         //left, right, center
space-between |                        //uses all the space to space the elements
space-around |                         //uses spaces for he border
stretch |                              //will stretch elements to use all the space                
baseline                               //align for baseline

```

We can use **inline-flex** to use flex only on a text element width:

<details>

<summary>Inline-flex, triple border and limited border guide </summary>

We use font-awesome icons \<i> that work as text:

```
<ul class="trino">
  <li>
    <i class="fas fa-laptop-code"></i>
    ...
  </li>
  <li>
    <i class="fas fa-laptop-code"></i>
    ...
  </li>
  <li>
    <i class="fas fa-laptop-code"></i>
    ...
  </li>
</ul>

```

We use **inline-flex** to have the border circle **only the text width** and **not the entire container,** and also to use all flex properties:

```
//we need justify and align for the :before:after layers
.trino li i{
    color: #C80815;
    border: 1px brown solid;
    padding: 0.6em;
    border-radius: 100%;

    display: inline-flex;
    justify-content: center;
    align-items: center;
}

.trino li i::before{
    padding: 1em;
    border-radius: 100%;
    border: 1px brown solid;
}

.trino li i::after{
    content: "";
    position: absolute;

    padding: 1em;
    border-radius: 100%;
    border: 1px brown solid;
}

```

To get a smaller border we reduced the container:

```
.trino li:nth-child(2){
    width: 80%;
    margin: 0 auto;
    border-bottom: 2px solid brown;
}

```

</details>

<figure><img src="../.gitbook/assets/Untitled.png" alt=""><figcaption><p>row and column flex layout</p></figcaption></figure>

The **flex-wrap** property sets if flex element will **fit in one line (nowrap)** or can **warp onto multiple lines,** we can use **flex with %** to set more precise for the elements.

{% tabs %}
{% tab title="Flex-wrap and 33%" %}
We don't need to create row containers, the **CSS can split** by itself.

```
//for each 3 elements we change a line
<div class="griglia">
  <div>
    <i class="fas fa-volleyball-ball"></i>
    ...
  </div>
  <div>
    <i class="fas fa-volleyball-ball"></i>
    ...
  </div>
  ...
</div>

```

We can also use **wrap-reverse** if we needed to change the order.

```
//3 for each line
.griglia{
    display: flex;
    flex-wrap: wrap;
}

.griglia div{
    flex: 33%;
    text-align: center;
}

//then we border the third and forth for the effect
.griglia div:nth-child(3n+1){
    border-right: 1px black solid;
}

.griglia div:nth-child(3n){
    border-left: 1px black solid;
}

```

<figure><img src="../.gitbook/assets/flexflow.PNG" alt=""><figcaption><p>33% flex layout</p></figcaption></figure>
{% endtab %}

{% tab title="Flex-wrap and 50%" %}
We go from 1/3 to 1/2 of the page for the elements:

```
.griglia div{
    flex: 50%;
}

```

<figure><img src="../.gitbook/assets/flexflowmin.PNG" alt=""><figcaption><p>50% flex </p></figcaption></figure>
{% endtab %}

{% tab title="Flex-wrap and 100%" %}
A single element per line:

```
.griglia div{
    flex: 100%;
}

```

<figure><img src="../.gitbook/assets/flexgrowmobile.PNG" alt=""><figcaption><p>100% flex-wrap</p></figcaption></figure>
{% endtab %}
{% endtabs %}

When using a flex-container with **multiple items** we can space them using **align-content** (different from align-items) or **gap:**

<details>

<summary>Align-content and gap guide</summary>

We have a flex-container that flex wraps,&#x20;

```
<div class="quatto">
  <div></div>
  <div></div>
  <div></div>
  <div></div>
</div>

```

We use **align-content** to space the vertical space of **multiple** flex items:

```
//each image wrapping at 40%
.quatto div{
    flex: 40%;
    height: 25px;
}

//we give space to the top/bottom using align-content, while padding is for outside
.oltre .quatto{
    display: flex;
    flex-wrap: wrap;

    align-content: space-around;
    padding: 5px;
}

```

If we want to define the **space between** the flex elements we use **gap**:

```
//it won't work with align-content
.oltre .quatto{
    display: flex;
    flex-wrap: wrap;
    
    gap: 10px;
    padding: 5px;
}

```

</details>

<figure><img src="../.gitbook/assets/aligngap.png" alt=""><figcaption><p>align-content and gap </p></figcaption></figure>

















We use the **\<img>** tag for _**images**_, it creates a space to link the image to:

```
//we can use order to change the order of flex-items
<div class="vedi">
    <img src="https://live.staticflickr.com/65535/52575562589_e7f248ff8f_c.jpg" alt="">
    <img src="https://live.staticflickr.com/65535/49197710168_014f46e3a2_w.jpg" alt="">
    <img src="https://live.staticflickr.com/65535/52148924164_6c7a2d74af_m.jpg" alt="">
</div>

.vedi{
    display: flex;
    height: 115px;
}

.vedi img:nth-child(1){
    flex: 1;
    order: 2;
}
.vedi img:nth-child(1){
    flex: 2;
    order: 1;
}
.vedi img:nth-child(1){
    flex: 3;
    order: 3;
}

```

<figure><img src="../.gitbook/assets/images.PNG" alt=""><figcaption><p>&#x3C;img> with flex order</p></figcaption></figure>

1

1

1

1

We can also have a **fixed** image background with content and scroll:

```
//We embed the text in an image

<div class="banner">
  <div>
    <h1>This is the Reason we are here</h1>
    <button>ECCOCI</button>
  </div>
</div>

//we set fixed and cover over the 
.banner {
    background: url("https://bit.ly/3NwLCLI") 50% -7em no-repeat fixed;
  
    background-size: cover;
    height: 20em;
    margin-bottom: 3em;
}

```

![We also added a position:ficed width:80% in the navbar](<../.gitbook/assets/Fixed (1).PNG>)

How to **flex** space and **style borders** on icons:

```
<ul class="ombra">
    <li>
        <i class="fas fa-laptop-code"></i>
        <h1>Icons and</h1>
        <p>
            Lorem 
        </p>
    </li>
        <li>
        <i class="fas fa-laptop-code"></i>
        <h1>Double </h1>
        <p>
            This
        </p>
    </li>
    <li>
        <i class="fas fa-laptop-code"></i>
        <h1>Border effect </h1>
        <p>
            The
        </br>
            Lorem
        </p>
    </li>
</ul>

//line of 3 elements with equal space, also </br> is to break the line
//to style the icons in li with a double border:

.ombra{
    display: flex;
    flex-direction: row;
    text-align: center;
}

.ombra li{
    flex: 1;
}

/*first border and setting the ray with padding */
.ombra i{
    border: 1px brown solid;
    padding: 0.6em;
    border-radius: 100%;
    color: #C80815;
}

//we use :before for the second layer and display inline-block
.ombra i::before{
    display: inline-block;
    padding: 1em;
    border-radius: 100%;
    border: 1px rgba(165, 42, 42, 0.6) solid;
}

//To rotate a square border you will need 2 transforms:

.square li{
    border: 1px pink solid;
    padding: 1em;
    transform: rotate(-45deg);
}

.square li i{
    transform: rotate(45deg)
}

```

Adaptive **Row** with centered text and button:

```
<div class="incluso1">
    <div class="dentro">
        <img src="./img.png" alt="">
    </div>
    <div class="dentro">
        <h2>Title </h2>
        <p>
            Text on
        </p>
        <button>Buttoned </button>
    </div>
</div>

//we both need align for the text to be in the same line and the justify for space them

.incluso1{
    display: flex;
    flex-direction: row-reverse;
    align-items: center;
    justify-content: space-between;
}

.dentro{
    text-align: center;
}

.dentro img{
    width: 100%;
}

//setting width 100% will adapt it to its flex space, also for smaller screens we simply

.incluso1{
    flex-direction: column;
}
.incluso img{
    width: 50%;
}
//the width is just to leave some margin on the sides
```

&#x20;Also check the difference in **the use of flex** n/% on the grid and colorband:

```
<div class="griglia">
    <div>
        <i class="fas fa-volleyball-ball"></i>
        <h2>Using the </h2>
        <p>Lorem </p>
    </div>
</div>

//Using % on flex elements and Wrap allows us to organize rows of 3 elements styling only the li elements

.griglia{
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
}

.griglia div{
    flex: 33%;
}

//in case we want to have all elements in a line using flex:n we:

<ul class="colorato">
    <li> 
        <i class="fas fa-hamburger"></i>
        <p>Here we have the </p>
    </li>
    etc...
</ul>

.colorato{
    display: flex;
    flex-direction: row;
}

.colorato li{
    flex: 1;
}

//with this, the elements added will be kept all in the same line, for smaller screens we just flex it as columns
```

And for the style of **list** , icons, and spacing:

```
<ul class="fa-ul">
    <h2>Contacts </h2>
    <li>
        <div>
            Email
            <i class="fa-li fa-spin fas fa-spinner"></i>
        </div>
        <div>Lorem ipsum dolor sit amet. </div>
    </li>
</ul>

//We use the FA-UL to set a list with the icons and the FA-LI to set the list icon 
//and we space the icon+text from the Lorem

.fine ul li{
    display: flex;
    justify-content: space-between;
}
```

Also **to make fonts-awesome spin we use** fa-spin like up here.

For **centering**, remember that **text-align** works with images too, **justify** on flex works only when multiple elements as children and you can also use **margin:auto**.

For each flex-direction: row we **don't need** to set the children as columns.

And, that the **href=""** in the \<a> tag stops some **animation** for showing.We can use the **calc()** to calculate some properties like:

```
max-width: calc(100% - 4em);
```

we didn't need to use **overflow** for the sidebar, in case:

```
overflow: visible (lets the content exceeding visible)
          hidden  (cuts the content by the space it has)
          scroll  (adds the scroll to fit the space)
          auto    (adds scroll only if necessary on content) 
          
```

About the **CSS Reset** , in case we need to have a standard css without browser inconsistencies, for smaller projects go:

```
*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
```

But to handle bigger projects and more efficiently we use in the **CSS file**:

```
html,body,div,form,fieldset,h1,h2,h3,h4,h5,h6,p,blockquote,pre
{
    font-size: 100%;
    font-weight: normal;

    margin: 0;
    padding: 0;

    vertical-align: baseline;

    border: 0;
    outline: 0;
    background: transparent;
}

table
{
    border-spacing: 0;
    border-collapse: collapse;
}

th,
td
{
    text-align: left;
    vertical-align: top;
}

img
{
    border: 0;
}

```

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

<details>

<summary>Image tag and flex order example</summary>

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

</details>

<figure><img src="../.gitbook/assets/images.PNG" alt=""><figcaption><p>&#x3C;img> with flex order</p></figcaption></figure>

**We can't use flex** for single images or absolute layers, we use **width**, then **we use flex centering** for the **img container** and the **img tag**:

```
<div class="testo">
    <div class="immaggine">
        <img src="https://live.staticflickr.com/65535/49197710168_014f46e3a2_w.jpg" alt="">
    </div>
    <h1>We paint</h1>
</div>

//we center the text+image container container
.testo{
    position: relative;
    display: flex;
    justify-content: center;
    align-items: center;
}

//then we set flex and width of the image container for the <img> tag
.testo .immaggine {
    width: 45%;
    display: flex;
    justify-content: center;
    align-items: center;
}

//THEN we set image width % 
.testo .immaggine img{
    width: 50%;
}

```

The **\<img> height** will be proportional to its width, we can't use % and the image container won't reduce it.

<figure><img src="../.gitbook/assets/Immagine 2023-03-13 124055.png" alt=""><figcaption><p>flex centered img container + width% image</p></figcaption></figure>

Remember that **opacity is affected by the HTML tag order**:

```
//if we put the <h1> first it would be affected by the opacity
<div class="immaggine">
    ...
</div>
<h1>We paint</h1>

.testo .immaggine img{
    ...
    opacity: 0.5;
}

```

We can also use the **background** CSS **** property to style the entire element it's used in, we need a **height/width** for the background.

```
<div class="secondo">
    <div></div>
    <h1>We paint more</h1>
</div>

.secondo{
    position: relative;
    display: flex;
    justify-content: center;
    align-items: center;
}

.secondo div{
    width: 80%;
    height: 10em;
    background-image: url("https://live.staticflickr.com/65535/49197710168_014f46e3a2_w.jpg");
    background-position: center;
    background-size: cover;

    opacity: 0.6;
}

```

<figure><img src="../.gitbook/assets/Immagine 2023-03-13 135148.png" alt=""><figcaption><p>div tag with url ackround and absolute text</p></figcaption></figure>

**Background-position** and **background-size** work similarly to how they worked with gradients, on CSS 1.

```
//position accepts X/Y values
background-position: top/left/bottom/right/%/px

//its default value is the native size of the image, auto/contain
background-size: X/Y/%
background-size: cover    //it stretches the image to occupy all the space, can crop

```

By default, a **background-image** that is smaller than the element it's in will **repeat** itself to **fill** it.

We use **background-clip** to extend it underneath its border-box, padding-box, or content-box.

<details>

<summary>backround-repeat and background-clip guide</summary>

We can set the **background-repeat** on the X/Y of the element, or not use it.

```
//it has background-repeat: repeat/ background-clip: border-box by default
.cliprepeat div{
    background-image: 
        url("https://live.staticflickr.com/65535/49197710168_014f46e3a2_w.jpg");

    width: 90%;
    border: 10px dotted black;
}

background-repeat: no-repeat            //will use the image only once
                   repeat-x/repeat-y    //will repeat only on width/height
                   space                //repeats only if available space, no clip
                   round                //repeats and stretches to not cut

```

while **background-clip**:

```
//we can use padding-box/ content-box to not include the border
.clipped div{
    background-image: url("https://live.staticflickr.com/65535/49197710168_014f46e3a2_w.jpg");
    background-repeat: no-repeat;
    background-clip: padding-box;

    width: 90%;
    border: 10px dotted black;
}

```

</details>

<figure><img src="../.gitbook/assets/Cattura.PNG" alt=""><figcaption><p>Background.clip and background-repeat different uses</p></figcaption></figure>

We can include most properties with a **background shorthand** and put **multiple background-images** in one element.

<details>

<summary>Multiple background  and shorthand property</summary>

Any space that is not filled by the image can be filled by **background-color**:

```
.molti div{
    background: 
        lightblue 
        url("https://live.staticflickr.com/65535/49197710168_014f46e3a2_w.jpg")
        no-repeat
        right top;

    width: 80%;
}

//background can include
background-color
background-image
background-repeat
background-position
```

We can add **multiple backgrounds** to an image:

```
//remember to use , for any new backround
.altri div{
    background: 
        url("https://live.staticflickr.com/65535/52148924164_6c7a2d74af_m.jpg")
        no-repeat
        left bottom,

        url("https://live.staticflickr.com/65535/49197710168_014f46e3a2_w.jpg")
        repeat;

    width: 80%;
}

```

</details>

<figure><img src="../.gitbook/assets/shorthand.PNG" alt=""><figcaption><p>background shorthand using multiple images and background-color</p></figcaption></figure>

We use a **fixed background-image** to have a fixed **scroll** image:

```
//The fixed navbar will remain visible, while the background will disappear on scroll
<div class="barra">
</div>
<div class="gravity">
</div>

.barra{
    position: fixed;
    top: 0;
    
    height: 3em;
    width: 100%;
    background-color: red;
}

.gravity{
    background: url("https://bit.ly/3NwLCLI") 20% 3em fixed;
    
    background-position: bottom;
    background-size: cover;
    height: 22em;
}

```

<figure><img src="../.gitbook/assets/fixedscroll2.gif" alt=""><figcaption><p>fixed background-image with fixed top navbar</p></figcaption></figure>

1

1

### CSS scrollbar styling

We use pseudo-elements to style the **scrollbar/scrollbar-track/scrollbar-thumb**:

<details>

<summary>Scrollbar style guide</summary>

We need to set the **scroll overflow** in the container first:

```
<div class="skrull">
    <div>
        <div>
          Lorem, ipsum dolor sit amet consectetur adipisicing elit. 
          ...
        </div>
    </div>
</div>

.skrull{
    overflow-x: scroll;
    overflow-y: scroll;

    width: 30%;
    height: 8em;
}

```

The **bottom/right scrollbar** area is set by height/width:

```
//width sets the left one, while height the bottom one
.skrull::-webkit-scrollbar{
    width: 12px;
    height: 4px;
}

```

while **scrollbar-track** is the actual areas

```
.skrull::-webkit-scrollbar-track {
    background: orange;
}

```

We use **scrollbar-thumb** for the css thumb:

```
//we can't reduce it, so we use border to color with the backround
.skrull::-webkit-scrollbar-thumb{
    background-color: rebeccapurple;
    border-radius: 10px;
    border: 3px solid orange;
}

```

</details>

<figure><img src="../.gitbook/assets/scollint.PNG" alt=""><figcaption><p>CSS scrollbar</p></figcaption></figure>

1

1

1

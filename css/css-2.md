# CSS 2

## Navbar and Flex use

Check this flex-sample webpage:

{% embed url="https://codepen.io/misterlinux/pen/KKXwerR?editors=1100" %}
Responsive flex page
{% endembed %}

For the **Sidebar** setting of the page we have:

```
<div class="contiene">
    <div class="sidenav">
    </div>
    <div class="resto">
    </div>
</div>
<div class="foot">
    <div class="fine">
        <div class="parole">
        </div>
        <ul class="fa-ul">
        </ul>
    </div>
</div>

//We separate the sidebar+content from the footer to have it cover the entire width
//we set a flex row and give the sidebar color and less space

.contiene{
    display: flex;
    flex-direction: row;
}
.sidenav{
    flex:1;
    background-color: brown;
}
.resto{
    flex: 4;
}
.sidenav ul li{
    display: flex;
    color: white;
}

//for the sidebar elements as a column we just use flex
.foot{
    display: flex;
    flex-direction: row;
    background-color: black;
    color: white
}
.fine{
    display: flex;
    flex-direction: row;
    flex: 100%;
    margin: 1em 3em;
}
.fine .parole, ul{
    flex: 1;
}

//the footer is also a row to avoid becoming an extra sidebar AND we add an additional row
//with the margins for the content of the content of the footer
```

And the @media-query for a smaller screen:

```
@media only screen and (max-width: 900px)  {
    .contiene{
        flex-direction: column;
    }
    .sidenav ul{
        display: flex;
        flex-direction: row;
        justify-content: space-around;
    }
    .fine{
        flex-direction: column;
    }
}

//for the first media-query we change the sidenav with a navbar AND changing the list
//and setting the nav elements in a single row, for the footer we just put the content as a column for space
```

And for the smallest screen we:

```
@media only screen and (max-width: 500px){
    .sidenav ul{
        flex-direction: column;
    }
    .foot{
        flex-direction: column;
    }
}

//here we give each navbar element its line and the footer adapts the other flex row it had
```

About **how to background-position the images** (both in sidebar and content), including **text**:

```
<div class="title">
    Angelo Zarate
</div>

.sidenav .title{
    background-image: url("./img.png");
    background-position: top right;
    height: 12em;
    display: flex;
    align-items: flex-end;
    box-shadow: 0px -20px 20px -20px #001f9c inset;
}

//here we have text over image, a set height space, and the flex to align the text on the bottom with align-item
//background-position= X%/Y% or left-right/top-bottom/center
//simply allows us to set the starting point of image display
//Also in case you want a precise centered text:

  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);

```

About [**Background-size**](https://www.w3schools.com/csSref/playit.asp?filename=playcss\_background-size\&preval=contain):

```
//we haven't used it much prefering width/flex 

background-size:contain;
background-size:cover; 

//contain gets and adapts the entire image in but can result in smaller or bigger result
//cover uses the entire space, considering our image was big already its not usable

```

Remember some rules for the use of **box-shadow** use:

```
//in the previous example 
box-shadow: 0px -20px 20px -20px #001f9c inset; (bottom)
            0px -20px -20px black inset; (right)
            0px 20px 20px -20px #001f9c inset; (top)
            20px 0px 20px -20px #001f9c inset; (left)
            
//we can set the side of the shadow with the X/Y position, also using inset for the intern shadow
//remember: X / Y(space)/ blur / spread and color
```

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


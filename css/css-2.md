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

About **how to position the images** (both in sidebar and content), including **text**:

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
```

Remember some rules for the use of **box-shadow** use:

```
//in the previous example 
box-shadow: 0px -20px 20px -20px #001f9c inset; (bottom)
            0px -20px -20px black inset; (right)
            0px 20px 20px -20px #001f9c inset; (top)
            20px 0px 20px -20px #001f9c inset; (left)
```

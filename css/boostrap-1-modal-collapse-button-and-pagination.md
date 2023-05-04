# Boostrap 1 Modal, Collapse button and Pagination

* [Modal & PureCSS](boostrap-1-modal-collapse-button-and-pagination.md#purecss-and-modal)
* [Collapse Buttons](boostrap-1-modal-collapse-button-and-pagination.md#collapse-buttons-and-content)
* [Pagination & Bootpag](boostrap-1-modal-collapse-button-and-pagination.md#pagination-and-bootpag)

Even if we include only some **modules** of bootstrap we use the entire package:

```
//On the <head> or inside the body
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.2.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-rbsA2VBKQhggwzxH7pPCaAqO46MgnOM80zW1RWuH61DGLwZJEdK2Kadq2F9CUG65" crossorigin="anonymous">
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.2.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-kenU1KFdBIe4zVF0s0G1M5b4hcpxyD9F7jL+jjXkk+Q2h455rYXK/7HAuoJl+0I4" crossorigin="anonymous"></script>

```

### Boostrap Spacing & Display

We can use classes to add **margin** and **padding**:

```
//so, we can use m_- or p_- for Margin or Padding, from 0-5 and even negative numbers
<div class="ms-3"></div>

//we can use x/y for left/right or top/bottom 
<p class="my-2"></p> 

//or if we want left/rigth/top bottom, s(tart),e(nd), t(op), b(ottom)
<h3 class="pe-4"></h3> 

```

And for _**display**_ and _**media-query** breakpoints_:

```
//we display with d-
d-flex /d-block /d-inline-block

//and media-query START from their breakpoints

d-block d-sm-none    //D(isplay)-block but AFTER -sm-D(isplay)-none, only visible in small
d-none d-sm-block    //D(isplay) AFTER small-block but BEFORE is -none
d-none d-sm-block d-md-none    //visible only in md (none in default and AFTER md)

```

### PureCSS and Modal

For modals we have _buttons_ opening **extra windows** with content, through **data-bs-toggle="modal"** and **data-bs-target**:&#x20;

```
//the button will have the -toggle for the modal and the target for the modal content
<button class="pure-button" data-bs-toggle="modal" data-bs-target="#uno">Stati</button>

//parent tag has a modal class to hide the modal before the click/fade for animation 
//and the ID for the target button
//the structure is modal-dialog>
                                 modal-content>
                                                modal-header...                                                   

<div class="modal fade" id="uno">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <button class="btn-close" data-bs-dismiss="modal"></button>
      </div>
      <div class="modal-body">
      </div>
      <div class="modal-footer">
      </div>
    </div>
  </div>
</div>

//to close we use the btn-close bootstrap and close our modal -dismiss="modal"

```

{% tabs %}
{% tab title="Button to" %}
![Clicking on it will](../.gitbook/assets/Modal.PNG)
{% endtab %}

{% tab title="Modal" %}
![Opened modal with BTN-CLOSE button](../.gitbook/assets/Camodulatottura.PNG)
{% endtab %}
{% endtabs %}

In the **modal content**, we can add Extra PureCSS pure-g(rid), images, forms, and bootstrap effects:

<details>

<summary>Bootstrap double modal guide</summary>

We create **2 modal** inside the other.

**data-bs-backdrop="static"** disables the click-off from the modal.

**modal-dialog-scrollable** will overflow: scroll the modal if Y space is smaller.

We can **bs-dismiss** the current **modal** and **bs-toggle** a new one.

**modal-dialog-centered** centers the modal by X and Y.

**modal-fullscreen-sm-down** will expand the modal to **fullscreen** if screen **below sm** media query.

```
<button class="pure-button" data-bs-toggle="modal" data-bs-target="#tutto"> 
    Mixing 
</button>

<div id="tutto" class="modal fade" data-bs-backdrop="static">

    <div class="modal-dialog modal-dialog-scrollable">
        <div class="modal-content">

            <div class="modal-header">
                <h2>We start </h2>
                <button class="btn-close" data-bs-dismiss="modal"></button>
            </div>

            <div class="modal-body pure-g">
                <div class="pure-u-1 pure-u-sm-1-2">
                    <p> We have the menu here </p>
                </div>
                <div class="pure-u-1 pure-u-sm-1-2">
                    <img src="https://bit.ly/3FMr8fu" alt="" class="pure-img">
                </div>
            </div>

            <div class="modal-footer">
                <button class="pure-button" data-bs-dismiss="modal" >Close </button>

                <button class="pure-button" data-bs-target="#secundum" data-bs-dismiss="modal" data-bs-toggle="modal">
                    Avanti 
                </button>
            </div>

        </div>
    </div>

</div>

<div class="modal fade" id="secundum" >

    <div class="modal-dialog modal-lg modal-dialog-centered modal-fullscreen-sm-down">
        <div class="modal-content">

            <div class="modal-header">
                <h3>This is larger </h3>
                <button class="btn-close" data-bs-dismiss="modal"></button>
            </div>
            <div class="modal-body">
            
                <h3>So we can close stuff with a link to the next modal
                    with <a href="#linkato" data-bs-dismiss="modal" data-bs-toggle="modal" >this </a> LINK
                </h3>

            </div>
            <div class="modal-footer">

            </div>

        </div>
    </div>
</div>

```

</details>

{% tabs %}
{% tab title="First Modal" %}
![First modal with grid form and image](../.gitbook/assets/Complete.PNG)
{% endtab %}

{% tab title="Second modal" %}
![Here the second modal with link ](../.gitbook/assets/Cattumodulera.PNG)
{% endtab %}
{% endtabs %}

### Pagination and bootpag

On **pagination** we create a list of links:

```
//pagination-sm/md/lg sets the size of the list 

<ul class="pagination pagination-lg justify-content-center my-5">

    <li class=""><a class="page-link" href="#">
      <span> &laquo;</span></a>
    </li>
    <li class=""><a class="page-link" href="#">1</a></li>
    <li class=""><a class="page-link" href="#">2</a></li>
    <li class=""><a class="page-link" href="#">3</a></li>
    <li class="page-item active"><a class="page-link" href="#">
      <span>&raquo;</span>
    </a></li>

</ul>

```

![1 is .disabled, 3 is .active](../.gitbook/assets/naviga.PNG)

To have a _**dynamic pagination**_ bar we can use a Jquery Plugin [**Bootpag**](https://botmonster.com/jquery-bootpag/#pro-page-7):

```
//to start using it we need Jquery and Bootpag in the <head> (and of course bootstrap)

<script src="http://code.jquery.com/jquery-2.1.3.min.js"></script>
<script type="text/javascript" src="http://botmonster.com/jquery-bootpag/jquery.bootpag.js"></script>
<link rel="stylesheet" href="http://netdna.bootstrapcdn.com/bootstrap/3.3.4/css/bootstrap.min.css">

```

With the **plug-in** we only need an **id** for the **pagination and content**:

```
<div style="text-align: center">
	<div id="dynamic_content">Pagination goes here</div>
	
	<div id="show_paginator"></div>
</div>

```

Everything is done on **javascript** :

```
//we load the parameters of the paginator with bootpag

$('#show_paginator').bootpag({
      total: 24,      //total pages/links
      page: 1,        //starting page
      maxVisible: 6,  //max number of visible
      next: ">",      //we can change the next button default arrow
      //href: "https://www.amazon.it/" //we can also give each page an automatic link

}).on('page', function(event, num){
     $("#dynamic_content").html("Page " + num); //onclick of the page link we change the html
});

```

{% tabs %}
{% tab title="First Pag" %}
![](../.gitbook/assets/bootpag.PNG)
{% endtab %}

{% tab title="Second Pag" %}
![](../.gitbook/assets/bottpag1.PNG)
{% endtab %}
{% endtabs %}

We can change the **pagination property** by using **$(this).bootpag({  }).**

```
<div class="text-center">
    <div class="demo1" >Pagination goes here</div>

    <div class="content"></div>
</div>


$('.demo1').bootpag({
    total: 5
}).on("page", function(event, num){
    $(".content").html("Page " + num); 
 
    //we can modify the bootpag after the click with new properties
    $(this).bootpag({
      total: 10,                   //we add more pag elements
      maxVisible: 6,
      next: 'next',
      href: "#pro-page-{{number}}", //we can give each button clicked an href
      prev: null   		    //we can delete the prev button 
    });
 
    if(num==5){
      $(".content").html("complimenti YOU GOT GNOMED")
    }
    //and we can add conditions on specific clicked buttons
});

```

{% tabs %}
{% tab title="First pag" %}
![](../.gitbook/assets/bootcap.PNG)
{% endtab %}

{% tab title="Second pag" %}
![](../.gitbook/assets/bootpog2.PNG)
{% endtab %}
{% endtabs %}

### Bootstrap Forms

We design Boostrap forms using **form-label/form-input** pairs, linked by **htmlfor/ID** attributes.

{% tabs %}
{% tab title="Form design" %}
Each input occupies 100% of the width unless a row>col is set.

```
//We can add read-only and form-control-plaintext to an input

<div className="row justify-content-center">
  <div className="col-6">
    <form>

      <div className="mb-3">
        <label htmlFor="scritta" className="form-label">Primo texto</label>
        <input id="scritta" type="text" className="form-control" />
      </div>

      <div className="mb-3">
        <label htmlFor="plain" className="form-label">Set email</label>
        <input id="plain" type="text" className="form-control-plaintext" 
                value="clash92@gmail.com" readOnly />
      </div>
        
      ....

    </form>
  </div>
</div>

```

<figure><img src="../.gitbook/assets/basicform.PNG" alt=""><figcaption><p>form with Readonly input, switch checkbox and inline form</p></figcaption></figure>
{% endtab %}

{% tab title="Switch input" %}
The **switch** is a styled checkbox input, so we need a **form-switch** container.

```
<div className="mb-3">

  <div className="form-check form-switch">
    <label for="spring" className="form-check-label">Resident </label>
    <input id="spring" type="checkbox" className="form-check-input" />
  </div>

</d
```
{% endtab %}

{% tab title="Inline form" %}
An inline form can be added with a **row** container and col label/input.

```
//we use g(utters) to space the col, col-form-label is for the align

<div className="row g-3 mb-3">

  <div className="col-auto">
    <label for="bugatti" className="col-form-label">contentID</label>
  </div>
  <div className="col-auto">
    <input id="bugatti" type="text" className="form-control" placeholder="texto" />
  </div>
  <div className="col-auto">
    <button className="btn btn-primary">submit</button>
  </div>

</div>

```
{% endtab %}
{% endtabs %}

We can add **multiple input**s to a single form **row.**

{% tabs %}
{% tab title="Multiple forms" %}
We add a row container to the form to create **col** inputs.

```
//Textarea can increase its height with rows=""

<div className="row justify-content-center">
  <div className="col-6">
  
  <form>
    <div className="row">

      <div className="col-6"> floating input... </div>
      <div className="col-6"> input+form-text... </div
      <div className="col-12 mb-3">
        <label for="muro" className="form-label">Your text here</label>
        <textarea id="muro" rows="3" className="form-control"></textarea>
      </div>

      <div className="col-8 mb-2"> datalist... </div>
      <div className="col-4 mb-2"> select... </div>

      <div className="col-3"> radioButton... </div>
      <div className="col-4"> radioButton... </div>
      <div className="col-5"> reverseCheck... </div>

      <div className="col-10 mt-3">
        <button className="btn btn-primary" type="submit">Submit</button>
      </div>

    </div>
    </form>

  </div>
</div>

```

<figure><img src="../.gitbook/assets/inputTextform.PNG" alt=""><figcaption><p>Floating label, form text, textarea, datalist, option, radio/reverse-checkbox</p></figcaption></figure>
{% endtab %}

{% tab title="Form-floating/form-text" %}
**Form-floating** sets the **label inside** the input and keeps it visible when the input is set, for it to work we need the **input on top of the label**.

**Form-text** puts text **below** the input, we can use aria-describedby/for to link it and input.

```
//we also need form-floating as a container

<div className="col-6">
  <div className="form-floating">
    <input id="mailin" type="password" className="form-control" 
            placeholder="mail" aria-describedby="mailindesc"/>
    <label htmlFor="mailin">Email adress</label>

    <div id="mailindesc" className="form-text">Password needs to be 6-12 characters long</div>
  </div>
</div>

```

<figure><img src="../.gitbook/assets/passwordForm.PNG" alt=""><figcaption><p>Form floating + form-text</p></figcaption></figure>
{% endtab %}

{% tab title="Datalist" %}
**Datalist** allows us to include a series of options for the input type text.

We need to add **list** to the \<input> and link it to the **datalist** using **ID.**

```
//We can add extra text in <option>, but only value will appear in the input

<div className="col-8 mb-2">
  <div className="form-floating">

    <input id="lista" type="text" className="form-control" list="multipli" 
            placeholder="molti"/>
    <label htmlFor="lista">Datalist</label>
    <datalist id="multipli">
      <option value="ancona">provincia</option>
      <option value="bari"></option>
      <option value="Catania"></option>
      <option value="Duccio"></option>
      <option value="Empoli"></option>
    </datalist>

  </div>
</div>
```

<figure><img src="../.gitbook/assets/datasetForm.png" alt=""><figcaption><p>Datalost options on input text</p></figcaption></figure>

1

1

1

1
{% endtab %}

{% tab title="select/options" %}
1

1

1

1

1

<figure><img src="../.gitbook/assets/selectForm.png" alt=""><figcaption><p>Select options on the form</p></figcaption></figure>
{% endtab %}
{% endtabs %}

1

1

1

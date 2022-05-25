# Boostrap 1

* list
* are
* some
* here

Even if we include only some **modules** of **** bootstrap we use the entire package:

```
//On the <head> or inside the body
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC" crossorigin="anonymous">
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/js/bootstrap.bundle.min.js" integrity="sha384-MrcW6ZMFYlzcLA8Nl+NtUVF0sA7MsXsP1UyJoMp4YLEuNSfAP+JcXn/tWtIaxVXM" crossorigin="anonymous"></script>

```

### PureCSS and Modal

For modals we have _buttons_ opening **extra windows** with content:&#x20;

```
//the button will have the -toggle for the modal and the target for the modal
<button class="pure-button" data-bs-toggle="modal" data-bs-target="#uno">Stati</button>

//parent tag has a modal to hide the modal before the click/fade for animation and ID for the target button
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

//to close we use the btn-close boostrap and close our modal -dismiss="modal"

```

{% tabs %}
{% tab title="Button to" %}
![Clicking on it will](../.gitbook/assets/Modal.PNG)
{% endtab %}

{% tab title="Modal" %}
![Opened modal with BTN-CLOSE button](../.gitbook/assets/Camodulatottura.PNG)
{% endtab %}
{% endtabs %}

In the content, we can add Extra PureCSS pure-g(rid), images, forms, and bootsrap effects:

```
//in the parent tag we can use DATA-BS-BACKDROP="static" to block the click-out of modal

<button class="pure-button" data-bs-toggle="modal" data-bs-target="#tutto" >Mixing </button>

<div id="tutto" class="modal fade" data-bs-backdrop="static">

//If the content is too long we can focus the scoll inside the modal
    <div class="modal-dialog modal-dialog-scrollable">
        <div class="modal-content">
            <div class="modal-header">
                <h2>We start </h2>
                <button class="btn-close" data-bs-dismiss="modal"></button>
            </div>
            <div class="modal-body pure-g">
                <div class="pure-u-1-2 pure-u-sm-1">

                </div>
                <div class="pure-u-1-2 pure-u-sm-1">

//So, pure-img allows the image to respond to the screen without being cut
                    <img src="https://bit.ly/3FMr8fu" alt="" class="pure-img">

                </div>
            </div>
            <div class="modal-footer">
                <button class="pure-button" data-bs-dismiss="modal" >Close </button>
//In case we have more modals we can -target="#" -dismiss the modal we have and -toggle the new one
                <button class="pure-button" data-bs-target="#secundum" data-bs-dismiss="modal" data-bs-toggle="modal" >Avanti </button>
            </div>
        </div>
    </div>

</div>

//we after-dismiss and -toggle the current modal we can open anew one

<div class="modal fade" id="secundum" >

//In dialog we can add the -centered that will center page the modal if its little enough
//-fullscreen/-fullscreen-sm-down are to have a modal occupy the ENTIRE PAGE, SM-DOWN will do it on smaller screens
    <div class="modal-dialog modal-lg modal-dialog-centered modal-fullscreen-sm-down">
        <div class="modal-content">
            <div class="modal-header">
                <h3>This is larger </h3>
                <button class="btn-close" data-bs-dismiss="modal"></button>
            </div>
            <div class="modal-body">
            
//we can target a modal with <a> and -dismiss/-toggle modal
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

{% tabs %}
{% tab title="First Modal" %}
![First modal with grid form and image](../.gitbook/assets/Complete.PNG)
{% endtab %}

{% tab title="Second modal" %}
![Here the second modal with link ](../.gitbook/assets/Cattumodulera.PNG)
{% endtab %}
{% endtabs %}

### Collapse Buttons and content

So, the **Collapse** Js plugin allows us to toggle the height visibility of **Target** content:

```
//We can use both buttons or <a> links as buttons, it needs to toggle the "collapse" plugin
//AND the target for the content to collapse/show

<a href="" 
 class="pure-button close" 
 data-bs-toggle="collapse" 
 data-bs-target="#opened"> 
 X 
</a>

//we need the COLLAPSE class and target, the rest is PureCSS
<div class="collapse" id="opened">
    <ul class="pure-menu-list centered">
        <li class="pure-menu-item"><a href="" class="pure-menu-link">Rerum?</a></li>
        <li class="pure-menu-item"><a href="" class="pure-menu-link">Repellendus.</a></li>
        <li class="pure-menu-item last"><a href="" class="pure-menu-link">Maxime?</a></li>
    </ul>
</div>

```

{% tabs %}
{% tab title="Collapse button" %}
![With the X button](../.gitbook/assets/Collapse.PNG)
{% endtab %}

{% tab title="Collapse content" %}
![This is the show content](../.gitbook/assets/CattCOLLAPSINGura.PNG)
{% endtab %}
{% endtabs %}

Also, it's possible to open **multiple** collapses by targetting classes:

```
<button data-bs-target=".multi-collapse" class="btn btn-primary" type="button" data-bs-toggle="collapse" aria-expanded="false" aria-controls="multiCollapseExample1 multiCollapseExample2">
  Toggle both elements
</button>

//We can open both the collapse r have buttons for each
<div class="collapse multi-collapse" id="multiCollapseExample1">
  <div class="card card-body">
    Some placeholder content for the first collapse component of this multi-collapse example. This panel is hidden by default but revealed when the user activates the relevant trigger.
  </div>
</div>

<div class="collapse multi-collapse" id="multiCollapseExample2">
  <div class="card card-body">
    Some placeholder content for the second collapse component of this multi-collapse example. This panel is hidden by default but revealed when the user activates the relevant trigger.
  </div>
</div>

```

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

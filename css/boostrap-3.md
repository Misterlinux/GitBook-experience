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
      <!-- nav-pills is what allows the active to have the color background -->
      <a class="navbar-brand" href="#">Navbar</a>
      <ul class="nav nav-pills flex-column">
        <li class="nav-item active">
          <!--to avoid having already checked active links I removed the classes from a-->
          <a href="#uno" >Lorem.</a>
        </li>
        <li class="nav-item">
          <a href="#due">Lorem.</a>
        </li>
        <li class="nav-item">
          ...
        </li>
        <li class="nav-item ms-3">
          <a href="#cinc">margin items</a>
        </li>
      </ul>
    </nav>
  </menu>

</div>
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

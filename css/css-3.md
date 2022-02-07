# CSS 3

* Form carousel effect

### Form carousel effect

First check this exercise:

{% embed url="https://codepen.io/misterlinux/pen/ZEaOQXW" %}
both in a column and wide screen
{% endembed %}

so, the basic HTML structure of the carousel and form is:

```
<div class="tavole tre">
  <input id='tabC-4' type='radio' name='tabgroupC' checked />
  <label class="quasi botto" for="tabC-4">1</label>
  ❭
  <input id='tabC-5' type='radio' name='tabgroupC'>
  <label class="quasi botto" for="tabC-5">2</label>

//we have radio inputs(that's gonna be invisible with CSS) and labels as buttons to allow the
carousel effect and the ID to pass to change form -->

  <table class="rigato">

// the structure of the box form is table>TD for each form inside

    <td>
      <div class="cartone" >
        <header>
          <h3 style="font-weight: bolder;">Your basic data</h3>
        </header>
        <section>
          <input class="text" type="text" placeholder="Full Name">
        </section>
        <section>
          <div class="selle">

            <select>
              <option>Where did you </option>
              <option>Facebook</option>
              <option>Twitter</option>
              <option>Linkedin</option>
            </select>

          </div>
        </section>
//divided in header+section+footer input>type="text" and select>option creates the options
        <footer>
          <label class="botto" for="tabC-6">Next</label>
          <label class="botto dangerous" for="tabC-4">Back</label>
        </footer>    
//so, on laber>for we have the form destination like we clicked the radio botton      
      </div>
    </td>

  </table>
</div>
```

For the CSS we start with layout and bottoms:

```
//so we deal with the redio input in the carousel

[type=radio] {
    opacity: 0;
    width: 0;
    position: absolute;
    display: inline-block; 
 }
 
 //we have the width to set area of the form, margin to center it,overflow to keep the others forms out of frame
 //text-align to center both radio bottons and form content (we changed that later)
 .tavole.tre{
	text-align: center;
	width: 50%;
	margin: auto;

	overflow: hidden; 
}

//for the total width of form we use forms*100% of the table, and we start with the first at left:0% position
//we have the fixed to have clean transition (with the time on button click)
.tavole.tre .rigato {
	width: 400%;
	left: 0%; 

	table-layout: fixed;

	transition: all 0.3s;
	margin: 10px 20px;
}
```

For the actual movement on click we:

```
//selectors dependant on context (checked buttons) on the tavole->row forms moving the margin from 0% moving 100% for form width 
.tavole.tre input:nth-of-type(1):checked ~ .rigato {
	margin-left: 0%; 
}
.tavole.tre input:nth-of-type(2):checked ~ .rigato {
	margin-left: -100%; 
}
```

For the final styling we:

```
.cartone {
	text-align: left;
}
//we re-set the align for the text form 

.selle select {
	width: 83%;
	margin-top: 0.3em;
	padding: 0.3em 0.4em;
	cursor: pointer;
}

.cartone[type="email"] input, 
.cartone[type="password"] input, 
.cartone .text,
.cartone textarea {
	margin-top: 0.3em;
	padding: 0.3em 0.2em;

	width: 80%;
}

To have the input area spaces be the same we need different % width
```


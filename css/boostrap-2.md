# Boostrap 2

* Cards and stuff
* more
* MORE

### Cards and color customitation

Cards are block of content easily customizable:

```
//Example of a single card structure, card in the container tag gives it the borders

<div class="card">
  <div class="card-header">Colored text on bg</div>
//card header gives it the gray background and border to the rest of the card

  <img src="https://bit.ly/3PIz2ee" class="pure-img p-3" >
  <div class="card-body">
    <h5 class="card-title">Card title</h5>
    <h6 class="text-muted">Card subtitle</h6>
//images can beput inside/outside the body in case you want extra margin

  </div>
  <div class="card-footer border-danger">
    <a href="#" class="card-link">Card link</a>
    <a href="#" class="card-link">Another link</a>
  </div>
//footer has the same as heaer, grey background  

</div>

```

![basic simple card](../.gitbook/assets/BasicCard.PNG)

To put more cards in row with padding between we can use pureCSS columns:

```
//Using the pureCSS, we need to include for each card

.pure-g > .card{
    box-sizing: border-box;
}

//ALSO we need to use P(adding) instead of margin to keep the grid
<div class="pure-g ">
  <div class="pure-u-1-4 text-center px-3">

//we put card after the padding to keep the borders separated
    <div class="card border-danger">
      <div class="card-header text-start bg-warning">
        Header gray background
      </div>
      <img src="https://bit.ly/3PIz2ee" class="pure-img p-3" >
      <div class="card-body bg-info">
        <h5 class="card-title"> Title and margins from text</h5>
        <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p>
        <a href="#" class="btn btn-primary">Go somewhere</a>
      </div>
      <div class="card-footer text-muted text-start ">
        grey text for posted time of the post
      </div>
    </div>
  </div>
  <div class="pure-u-1-4 text-center px-3">
    <div class="card">
      <div class="card-header text-start text-danger bg-success">Colored text on bg</div>
      <div class="card-body text-white bg-warning ">
        <h5 class="card-title">Card title</h5>
        <h6 class="text-muted">Card subtitle</h6>
        <p class="card-text">Card-links will have their own borders </p>
      </div>
      <div class="card-footer border-danger">
      
//card-link elements have margin included
        <a href="#" class="card-link">Card link</a>
        <a href="#" class="card-link">Another link</a>
      </div>
    </div>

  </div>
  <div class="pure-u-1-4 text-start px-3">
  	...
  </div>
  <div class="pure-u-1-4 text-center px-3 ">
  	...
  </div>
</div>

```

![4 cards in grid, space padding and colors](../.gitbook/assets/CARTA.PNG)

Or we could use Bootstrap W(idth) and wrap flex:

```
//First need the CSS for the container tag

.riga{
  display: flex;
  flex-wrap: wrap;
  flex-direction: row;
}

<div class="riga">
  <div class="w-25">
  
//to keep the card borders we have to use m(argins) in the same tag as card 
    <div class="card mx-3">
      <img src="https://bit.ly/3PIz2ee" class="card-img-top p-3" >
      <div class="card-body ">
        <h5 class="card-title">Card title</h5>
        <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p>
        <a href="#" class="btn btn-primary">Go somewhere</a>
      </div>
    </div>
  </div>
  <div class="w-25">
  	...
  </div>
  <div class="w-25">
  	...
  </div>
  <div class="w-25">
  	...
  </div>
</div>

```

**Colors** and **text-align** are:

```
text-start/ text-center/ text-end
left      / center     / right

//we can color bg(background), text-, border- using
-primary/-secondary/-warning/-danger/-dark/-info

```

also

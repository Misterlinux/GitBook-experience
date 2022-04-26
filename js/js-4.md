# JS 4

* Primo in lista
* secondo in lista

### Dom Js exercise with Dynamic objects

Check the following exercise:

{% embed url="https://codepen.io/misterlinux/pen/wvpOQKq" %}
BlackJack for 1/2 players
{% endembed %}

#### How we start the game

First, we need to select the number of players on the left menu:

```
contaplay.addEventListener("submit", (event)=>{
    event.preventDefault()
    
    if(numplayer.value=="uno"){
        ableButton()

        pescare()
        bancare()

        gioca = 1;
        document.getElementById("cont").disabled = true;
    }
    
    //if one player modee we draw 1 for bank/player, set the gioca counter at 1 (player)
    //we disable the menu to avoid overlap playmodes and ablebutton() the player buttons to play
})

else if(numplayer.value=="due"){
        pescare1()
        disableButton1()

        gioca = 2
        giocatore2.setAttribute("class", "giocatore2")
        giocatore2.style.display = "block"

        document.getElementById("cont").disabled = true;
    //so, we add the pescare1() to draw the second player first card, AND disable player 2 buttons 
    //waiting for player 1 to play first, ALSO, we change the DISPLAY value to make player 2 visible in HTML

}

```

#### How we created a deck and random draw

So, first, we start the structure of the "deck" with an objects and an array:

```
let deck = {
    spade: [ 1, 2, 3 , 4, 5, 6, 7, 8, 9, 10, 11, 12, 13 ],
    fiori: [ 1, 2, 3 , 4, 5, 6, 7, 8, 9, 10, 11, 12, 13 ],
    cuori: [ 1, 2, 3 , 4, 5, 6, 7, 8, 9, 10, 11, 12, 13 ],
    quadri: [ 1, 2, 3 , 4, 5, 6, 7, 8, 9, 10, 11, 12, 13 ],
}
//with the deck object we store the "cards" numbers we are gonna draw during the game, with the correspective card suits 
//The plan is NOT to remove an object property once the cards array finish, we use another array
 
let mazzi = [ "spade", "fiori", "cuori", "quadri"]
//for the cards suits that rappresent the properties of the deck object, and remove from here
//when the array of cards is empty

```

First we pick a **random card suit**:

```
let seme = Math.floor( Math.random()* mazzi.length )
//we generate a random number for the index in MAZZI, Math random goes from 0 to 1(not included)
//AND we use the .length of the mazzi array (this allow it to work if an element is removed)
//ALSO we use Math.floor to avoid getting an index non-existent in the array (0.99 * 4 will round at index 3) 

let carte = mazzi[seme]
//we use this to get the string of the random selected card suit
//we could also use:    mazzi[ Math.floor( Math.random()* mazzi.length ) ]
//BUT we need the variable for later

```

Second, to pick a **random index from the array of the Object property**:

```
let pescato = Math.floor( Math.random()* deck[ carte ].length )
//as before we pick an index based on .length and Math.floor on the avaiable cards in the array
 
```

Then select the array index element using variables, **Dynamically access object property:**

```
deck[ carte ][pescato]
//we select the card suit property of the Object, with the pescato index
//we don't have to use the DOT deck.spade, also deck["spade"], this allow us to use variables in brackets

//we invoke the function to place the card data in the correct HTML ID 
playdraw( deck[ carte ][pescato] , players, playnum, playstat, carte )

```

To remove the "drawn" card from the array we use **splice():**

```
deck[carte].splice( pescato, 1 )
//we modify the deck.carte array ,removing only the index (pescato) element (by 1)

```

Lastly, in case the **last card from an array is drawn** we remove it from the mazzi array:

```
if( deck[carte].length == 0 ){
    let vuoto = mazzi.indexOf( carte ) 
    mazzi.splice( vuoto, 1 )
}
//We check the array.lentgh of the current array we draw the card from 
//if empty we use the index of the "string" to splice it from the mazzi array
//so we can't draw from it without needing to delete a property

```

And when **all cards from all the arrays are drawn** we **re-start the page**:

```
if( mazzi.length== 0 ){
    over.innerText = "No more DECK to draw"
    setTimeout(function () {
        location.reload()        //it works on the entire page without windows.
    }, 1000);
}

//after .splice() we will end up with an mpt array, and if, we print on HTML the messagre
//and restart the server after 1 second they read it

```

All of the above is what is used for the **draw functions** of the 2 players, **the bank** has a similar function:

```
pescare()            //will drawn a random cards and pass with playdraw()
pescare1()           //on the other side bancare() wont need a playdraw()

function bancare()

for (let x in deck) {
    if(deck[x].length == 0){
        let vuoto = mazzi.indexOf( x ) 
        mazzi.splice( vuoto, 1 )
    }
}
//we use a different way to .splice the empty arrays, using the Object properties and checking each length()
//BUT we use it to STILL .splic from the mazzi array, x is the name of the card suit
//the rest is just code from the playdraw() we included to not have a separate function() with arguments not needed

```

### How we display cards and for 2 players

So, the **playdraw()** function that player and player1 have, we use it to show the cards drawn in the HTML:

```
playdraw( deck[ carte ][pescato], players1, playnum1, playstat1,carte )

function playdraw(it, lista, numer, stat, seme ){
    lista.push(it)
    //we push the drawn value in the player array of cards, the argument lista works for both players by player/player1 argument 

    let corretto = document.getElementById( numer.id )
    let para = document.createElement("span");
    //ALSO when we take document.getElementbyId("numer") we take the entire HTML tag <p id="playnum"></p>
    //we will need just the ID that for the append.

    para.innerText = it;
    //we create a span and put the drawn content into it
    //THEN check the card suit for the icon on the card

    if( seme == "quadri"){
        para.setAttribute("class", "fa-solid fa-diamond");
        corretto.appendChild(para);
        //we use Fontawesome to use classes for the icons AND the numer.id as append target
        //also for the setAttribute we put "class/id", "value"
        
    }else if(seme == "cuori"){
        para.setAttribute("class", "fa-solid fa-heart");
        corretto.appendChild(para);
    }else if(seme == "spade"){
        para.setAttribute("class", "fa-solid fa-heart spada");
        corretto.appendChild(para);
    }else{
        para.setAttribute("class", "fa-solid fa-clover");
        corretto.appendChild(para);
    }

//To have a number and icon be on different levels in the CSS 

#playnum > span {
    border: solid white 1px;
    margin: 3px;
    padding: 18px 13px;

    color: white;
    font-size: 8px;
}
//spacing and border

#playnum span::before{
    color: black;
    text-align: center;
    position: absolute;
    z-index: -2;

    font-size: 25px;
    margin: 2px;
    transform: translate(-44%, -45%);
}
//The font-awesome icons will be put in the ::before pseudoclass, we use the z-index and absolute position

#playnum span.fa-diamond::before, #playnum span.fa-heart::before{
    color: red;
}
//the diamond and heart will be red

#playnum span.spada::before{
    color: black;
    transform: translate(-46%, -48%) rotate(-180deg);
}
//we rotated an icon to make it look like spades

```

After displaying the card we sum the array values of the player/payer1:

```
let sum = lista.reduce(summing)
//we sum the array values with Array.reduce(function)

function summing(total, num){
    return total + num
}
//we have 2 default parameters, one for the sum counter + the forEach() elements 

if( stat.id== "playstat" ){
    shoot(sum, playstat, playnum, players)
}else{
    shoot(sum, playstat1, playnum1, players1)
}
//we take the sum to shoot() with the player HTML, also remember the ==, if not the stat.id to change
//ALSO we use stat.id to differentiate the sum and array of player/player1

```

### How to handle win/lose state and play attempts

In the shoot() function,&#x20;

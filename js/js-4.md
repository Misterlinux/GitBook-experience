# JS 4

* [How we display cards and for 2 players](js-4.md#how-we-display-cards-and-for-2-players)
* [How to handle win/lose state and play attempts](js-4.md#how-to-handle-win-lose-state-and-play-attempts)
* [How the automated house plays](js-4.md#how-the-automated-house-plays)

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

In the **shoot()** function, we handle win attempts and win conditions:

```
function shoot(sball, dove, numeri ){
    if(sball< 21){
        dove.innerHTML = "Card?"
        //the player can still play
    
    }else if( sball== 21 && dove.id!== "bancostat"){
        //if blackjack AND it is a player, not the house
        //we set the text on win condition and number drawn
    
        dove.innerHTML = "Stand"
        playfinal.innerHTML = sball
    
        disableButton()
        //we disable the player1 after sending the card score
        
        giokato = giokato + 1
        //we have a player counter that we will compare to the player counter mode 
        //it will increase for each player that presses the play button
    
        if( giokato == gioca){
            if(giokato==1){
                bancobot(final)
    
            }else{
                bancobot()
                disableButton1()
            }
            //We need different arguments for 1 or 2 players,only player 1 will have the final while 

        }
        else{
            document.getElementById("playstand1").disabled = false;
            document.getElementById("playgio1").disabled = false;
            //this happens after player 1 blackjacks so we let player 2 start 
        }
    }
    else if( dove.id== "bancostat"){
        dove.innerHTML= "sballato"
        //this will work even if ball = 21 for bancato 
    }
...
}

```

For **lose condition,** when neither house or players have ==21 or <21:

```
...
else{
    //if not ==21 and not <21
    let perso = document.createElement("button")
    let retry = document.createTextNode( "re-try" )
    
    perso.appendChild(retry)
    perso.setAttribute("id", "retry");
    //we create a button, a TextNode for the button text with appendChild
    //also (the attribute, the name of the attribute)

    document.getElementById( dove.id ).appendChild( perso )
    //the dove.id is also whre the result is, we append a button to restart after lose condition

    perso.onclick = () =>{

        if(dove.id == "playstat"){
            attento+= 1;
        }else{
            attento1+= 1;
        }
        //attento is the number of attempts, increases for each loss

        if( attento> 2 || attento1> 2 ){
            
            banko.style.backgroundColor = "yellowgreen";
            playa.style.backgroundColor = "indianred";

            bankowin.innerText = "Banco Wins"
            playawin.innerText = "Too many attempts"
            
            dinuovo()
            //if at the 3th attempt we put the losing condition on the HTML
            
        }else if( attento == 1 && attento1== 0 ){
            playstand.style.backgroundColor = "salmon";
        }else if( attento == 2 && attento1== 0 ){
            playstand.style.backgroundColor = "crimson";
        }else if( attento1 == 1 ){
            playstand1.style.backgroundColor = "salmon";
        }else if( attento1 == 2 ){
            playstand1.style.backgroundColor = "crimson";
        }else{
            console.log("something happened with " + attento + " " + attento1 )
        }
        //a bit complex on the counter, but we need to keep the button colors separated 

        dove.innerHTML= ""
        numeri.innerHTML= ""            
        sball= 0

        if( numeri.id == "playnum" ){
            players= []
        }else{
            players1= []
        }
        //when re-starting but not yet losing, we reset score arrays and empty arrays

    }
}

```

After creating the re-try button and onClick(), we use the **dinuovo()** function to reset the game state after losing with 3 attempts:

```
function dinuovo(){
    disableButton()

    let restart = document.createElement("button")
    let resta = document.createTextNode( "ritenta" )
    restart.appendChild(resta)
    //after we disable() we create the button and text 

    document.getElementById("ritentando").appendChild( restart )
    //we append the created button 

    //so, in theory we need to disable the conta button before the restart
    document.getElementById("cont").disabled = true;
    
    restart.onclick = () =>{

        resetto()
        //we resetto() most of the innerHTML

        let ricomincia = document.createElement("button")
        let ricomi = document.createTextNode( "rigioca" )
        ricomincia.appendChild( ricomi )
        //WE NEED ANOTHER button after they click and understood the lose state

        document.getElementById("ricominciando").appendChild( ricomincia )

        contaplay.addEventListener("submit", (event)=>{
            event.preventDefault()
            //we can have multiple addEventListener for submit

            giokato = 0;
            ricominciando.innerHTML= ""

            attento = 0;
            attento1 = 0;

            if(numplayer.value=="uno"){
                giocatore2.setAttribute("class", "giocatore2")
                giocatore2.style.display = "none"

            }
            //we reset the counters for the players, ALSO we add the player one option
            //to pass from player 2 to 1 while keeping the new counters after the reset with the submit

        })

        if(gioca == 1){

            ricomincia.onclick = () =>{
                giokato = 0;
                bancare()
                pescare()
                ricominciando.innerHTML= ""
    
                ableButton()
    
                attento = 0;
                attento1 = 0;
            }

        }else{
            ricomincia.onclick = () =>{
                bancare()
                pescare()
                pescare1()
                ricominciando.innerHTML= ""
    
                giokato = 0
    
                ableButton()
                disableButton1()
            
                attento = 0;
                attento1 = 0;
            }

        }
        //and in case we don't use the player menu, we reset the play-state based on the number of players

    }
}

```

### How the automated house plays

After we get the players drawn score, we need to start the House play with **bancobot(),** first we start if the player gets 21, with the house challenging it by drawing cards at 1 second delay:

```
function bancobot(sfida){
    //argument present only with player 1 mode
    if(sfida){
        let ginn = banco.reduce(funk)

        function funk(total, num){
            return total + num
        }
    
        if( sfida > ginn ){
            //if the player has the bigger bid then the house starts drawing with bancare()
            bancare()
            let timeout;

            //with the timer_on true at !0 we starts the timedCount() 
            //with the timer_on at 1 we get !1 false
            if (!timer_on) {
                timer_on = 1;
                timeout = setTimeout(timedCount, 1000);
                mybanc(ginn, sfida)
            }

            //we use the setTimeout, that is supposed to happen once, BUT
            //we set the timedCount() inside, using the ginn as the counter after summing each time
            //we clear the timer after it gets to or surpasses the players bid
            function timedCount() {
                timeout = setTimeout(timedCount, 1000);

                if(ginn >= sfida ){
                    clearTimeout(timeout);
                    timer_on = 0;
                    mybanc(ginn, sfida)
                }else{
                    bancare()
                    ginn = banco.reduce(funk)
                    mybanc(ginn, sfida)
                }
            }

            dinuovo()

            ginn = banco.reduce(funk)
            bancofinal.innerHTML = ginn
            //we stop the game with dinuovo and show the result
        }
        else{
            //auto win if the players bid is lower than the house
  
            banko.style.backgroundColor = "yellowgreen";
            playa.style.backgroundColor = "indianred";

            bankowin.innerText = "Banco Wins"
            playawin.innerText = "Player Lost"

            bancofinal.innerHTML = ginn
            disableButton()

            dinuovo()
        }
    }
...

```

The process is similar for the **2 players mode,** we just double the house score and sum the 2 players score, the game proceeds the same **** :

```
//THIS IS FOR THE 2 PLAYERS MODE, we get the 2 arrays, first we SUM the 2 arrays from players
    function twoplayers(arr1, arr2) {
        return [...arr2, ...arr1];
    }

    let versus = twoplayers(players, players1)

    let versato = versus.reduce(finaling)

    function finaling(total, num){
        return total + num
    }
    //after summing the arrays we sum all values inside

    let playfinal1 = document.getElementById("playfinal1")
    playfinal1.innerHTML = ""
    playfinal.innerHTML = versato

    //for the 2 player mode we will double the house score for 42
    let ginn = banco.reduce(funk) * 2

    function funk(total, num){
        return total + num
    }

```

### Players buttons and more win conditions

So, we now need to show how the players play with the functions set up, to **draw** more cards or **stand** we have:

```
playgio.addEventListener("click", (event)=>{
    event.preventDefault()
    pescare()
})
//we just use the button to use the draw function

playstand.addEventListener("click", ()=>{
    let final = players.reduce(finaling)
    
    function finaling(total, num){
        return total + num
    }

    playfinal.innerHTML = final
    disableButton()
    giokato= +1;
    //we sum, set the counter and display the value on the HTML to let the bancobot() play

    if( giokato == gioca ){
        bancobot(final)
    }else{
        document.getElementById("playstand1").disabled = false;
        document.getElementById("playgio1").disabled = false;
        document.getElementById("playstand").disabled = true;
        document.getElementById("playgio").disabled = true;
        //in the 2 players mode we need to disable player 1 buttons and enable player 2 buttons

    }
})

```

And for the colors the **win conditions of house/players use mybanc()**:

```
function mybanc(ginn, sfida){
    if( ginn==sfida && sfida < 43 ){
        banko.style.backgroundColor = "yellowgreen";
        playa.style.backgroundColor = "indianred";

        bankowin.innerText = "Banco Wins"
        playawin.innerText = "Player Lost"
        //this works for both 21/42 player score, if == then house win
    }else if( ginn > sfida && ginn< 22 && sfida< 22 || ginn > sfida && ginn< 43 && sfida> 22 ){
        banko.style.backgroundColor = "yellowgreen";
        playa.style.backgroundColor = "indianred";

        bankowin.innerText = "Banco Wins"
        playawin.innerText = "Player Lost"
        //if house > player BUT both are less then 22, 1 player more
        //if house > player, house is less than 43 AND the players are bigger than 22 , 2 players mode
    }else{
        banko.style.backgroundColor = "indianred";
        playa.style.backgroundColor = "yellowgreen";

        bankowin.innerText = "Banco Lost"
        playawin.innerText = "Player Wins"
        //if not then the player won
    }

    bancofinal.innerHTML = ginn
}

```

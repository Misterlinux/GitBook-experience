# JS 5

* ERROR and stuff
* there is more
* accidenti

**Error is a javascript object**, with **.message** being its main **property**:

```
//we can create Error objects with their message, THEN throw them if we want to block
//the code with red messages

let errore= "Error number " + Math.random()
const blokka= new Error(errore)    //we include in () the Error.message

console.log(blokka)
console.log(blokka.message)
throw blokka                       //throw new Error(errore)

//when we throw we block the entire code sequence

```

<figure><img src="../.gitbook/assets/ERRORATO.PNG" alt=""><figcaption><p>we get the error printed and its .message property</p></figcaption></figure>

To work with Errors _without blocking the code_ we use **TRY** and **CATCH**:

```
//We use TRY to see if we get a throw Error, if we do we CATCH the (error) and perform an action

try{
    throw new Error("printed after try")
}catch(errorino){
    console.log(errorino.message)
    //we get printed the error.message without blocking the code 
}

```


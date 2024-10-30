# JSON Syntax and Structure

* 1

**JSON** (JavaScript Object Notation) is a language-independent **text format**. It's a **text representation** of data using a _subset_ of javascript object **syntax** and it is used to _pass data between systems_.           JSON is **self-describing**, meaning it doesn't require external context to describe its structure and content.

The **JSON.stringify()** and **JSON.parse()** methods can convert and parse a js object into a JSON object string. A JSON object is a **string** that conforms to the JSON syntax, representing a data structure.

```jsx
//We need quotation marks on JSON key/value pairs 
//We can edit and access it like a js object
let notazione = {
  "primo": "nome",
  "secondo": 15
}
console.log( notazione.secondo )    // 15
console.log( notazione["secondo"] ) // 15

notazione.secondo = 22
console.log( notazione["secondo"] )  //22

let stringa = JSON.stringify(notazione) //"{"primo":"nome","secondo":15}"
JSON.parse(stringa)		        //{primo:"nome",secondo:15}
```

<details>

<summary>Differences between JSON and XML</summary>

XML is a **markup language** also used to store and transport data.                                                            Like JSON, it's self-describing, and can be parsed using **XMLHttpRequest**.                                 However, unlike JSON, it requires tags, is generally slower, and can't natively transport arrays. It requires data to be wrapped between tags in its XML data, whereas with JSON, we can simply parse it.

```jsx
//Rendering an object with JSON and XML
let object = {"employees":[
  { "firstName":"John", "lastName":"Doe" },
  { "firstName":"Anna", "lastName":"Smith" },
  { "firstName":"Peter", "lastName":"Jones" }
]}

<employees>
  <employee>
    <firstName>John</firstName> <lastName>Doe</lastName>
  </employee>
  <employee>
    <firstName>Anna</firstName> <lastName>Smith</lastName>
  </employee>
  <employee>
    <firstName>Peter</firstName> <lastName>Jones</lastName>
  </employee>
</employees>
```

</details>

According to the JSON rules, no functions can be passed in a JSON object, and objects like Date() need to be converted to strings before being serialized to JSON.

```jsx
//We can eval() the string functions if necessary
let obj = {
  "primo": 12999, "monni": "function () {return 30;}", "data": "1986-12-14"
}

parsed.monni = eval( "(" + parsed.monni + ")" )
console.log( parsed.monni() )  //30
console.log( new Date(obj.data) )  //Sun Dec 14 1986 01:00:00 GMT+0100

//An JSON.parse() array will return a js array, not an object.
let lista = `["12", 12, "baleno"]`
console.log( JSON.parse( lista ) )  //["12", 12, "baleno"]
```

The JSON.parse() method includes the **reviver** argument, a function that is called for each **key-value** pair in the parsed JSON, allowing you to transform the parsed data before it is returned as a JavaScript object.

```jsx
//We filter the reviver to avoid the root object being called
//We convert the data string into an object during the parsing.
let basic = `{
  "primo": 12999, "monni": "function () {return 30;}", "data": "1986-12-14"
}`

let flusso = JSON.parse( basic, function(key, value){
  if(key){
    console.log( `The key/value pairs are ${key}: ${value}` )
    if(key == "data"){
      return new Date(value)
    }
  }
  return value
})

console.log( flusso )
//The key/value pairs are primo: 12999, ...
//{primo: 12999,monni: 'function () {return 30;}', data: Sun Dec 14 1986 01:00:00
```

1

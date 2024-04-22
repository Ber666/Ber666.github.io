---
layout: post
title: "Notes on JavaScript"
date: 2023-09-22 09:56:00-0400
description: Notes on Javascript
tags: "web", "javascript"
categories: "notes"
giscus_comments: false
related_posts: false
related_publications: 
---
# Notes on Javascript

## An example
The most common HTML methods in JavaScript in `getElementById()`:
```javascript
document.getElementById("demo").innerHTML = "Hello JavaScript";
document.getElementById("demo").style.fontSize = "35px";
document.getElementById("demo").style.display = "none"; // change to block to show it on click.
```
Embedded in HTML:
```html
<button onclick="document.getElementById('myImage').src='pic_bulbon.gif'">Turn on the light</button>
```
JavaScript accepts both double and single quotes.

## Where to go
JavaScript code can be inserted between `<script>` and `</script>` tags. It can be defined in either `<head>` or `<body>` in an HTML, or both. It can be saved in an external file, e.g., `<script scr="file.js"></script>`. The path can be a remote URL. It's equivalent to writing the code in the tag pairs. Besides better readability, **cached JavaScript files can speed up page loads**.

## Output
- Writing into an HTML element, using `innerHTML`.
    ```html
    <p id="demo"></p>
    <script>
    document.getElementById("demo").innerHTML = 5 + 6;
    </script>
    ```
- Writing into the HTML output using `document.write()`.
    ```javascript
    document.write(5 + 6);
    ```
    **Note**: If it's called (e.g., on click) after the document is loaded, the document will be overwritten.
    ```html
    <html>
    <body>
    <h2>My First Web Page</h2>
    <p>My first paragraph.</p>
    <button type="button" onclick="document.write(5 + 6)">Try it</button>
    </body>
    </html> 
    ```
- Writing into an alert box, using `window.alert()`.
    ```javascript
    window.alert(5 + 6); \\ or alert(5 + 6)
    ```
    In JavaScript, the window object is the global scope object. This means that **variables, properties, and methods by default belong to the window object**. This also means that specifying the window keyword is optional.
- Writing into the browser console, using `console.log()`.
    ```javascript
    console.log(5 + 6);
    ```
## Best Practice
- Ending statements with semicolon is not required, but highly recommended.
- If a JavaScript statement does not fit on one line, the best place to break it is after an operator
- JavaScript programmers tend to use camel case that starts with a lowercase letter: `firstName`, `masterCard`, etc.

JavaScript Variables can be declared in 4 ways:
- Automatically **(Not recommended)**
- Using var
- Using let
- Using const
> The let and const keywords were added to JavaScript in 2015.
- Always use const if the type should not be changed (Arrays and Objects)
- Only use let if you can't use const
- **Only use var if you MUST support old browsers.**

- Use === Comparison, so that there won't be automatic type conversion.

## Let
JavaScript had **Global Scope** and **Function Scope**.

- Variables declared with let have **Block Scope**
- Variables declared with let **must be Declared before use**
- Variables declared with let **cannot be Redeclared in the same scope**
```javascript
{
  let x = 2;
}
// x can NOT be used here
```
JavaScript Declarations are Hoisted: a variable can be declared after it has been used. Should always avoid this.

## Const

For objects (dict) and arrays, it defines a `const` reference, instead of a value.

```javascript
// You can create a constant array:
const cars = ["Saab", "Volvo", "BMW"];
// You can change an element:
cars[0] = "Toyota";
// You can add an element:
cars.push("Audi");

// You can create a const object:
const car = {type:"Fiat", model:"500", color:"white"};
// You can change a property:
car.color = "red";
// You can add a property:
car.owner = "Johnson";
```

**Should declare objects/arrays with const**
## Datatype
JavaScript has 8 Datatypes
1. String
2. Number
3. Bigint
4. Boolean
5. Undefined
6. Null
7. Symbol
8. Object

The Object Datatype
The object data type can contain:

1. An object
2. An array
3. A date

JavaScript has dynamic types. This means that the same variable can be used to hold different data types.

All JavaScript numbers are stored as decimal numbers (float64).

Big int type:
```javascript
let x = BigInt("123456789012345678901234567890");
```
type of:
```javascript
typeof (3 + 4)        // Returns "number"
typeof "John"         // Returns "string"
```
## Function
```javascript
function myFunction(p1, p2) {
    return p1 * p2
}
```
It executes when:
- an event occurs (e.g., `onclick`)
- it's called in JS code
- automatically

## Object
Similar to class
```javascript
const person = {
  firstName: "John",
  lastName : "Doe",
  id       : 5566,
  fullName : function() {
    return this.firstName + " " + this.lastName;
  }
};
```

## Event
JS can change an element's own innerHTML with `self`
```html
<button onclick="document.getElementById('demo').innerHTML = Date()">The time is?</button>
<button onclick="this.innerHTML = Date()">The time is?</button>
```

## String
`Template` (starting and ending with \`) support multi-line, and placeholders:
```javascript
let header = "Template Strings";
let tags = ["template strings", "javascript", "es6"];

let html = `<h2>${header}</h2><ul>`;
for (const x of tags) {
  html += `<li>${x}</li>`;
}

html += `</ul>`;
```
## Errors
Try, catch. Except for built-in error objects (EvalError, RangeError, ReferenceError, SyntaxError, TypeError, and URIError), one can also throw errors.

```javascript
try {
    if(x.trim() == "") throw "empty";
    if(isNaN(x)) throw "not a number";
    x = Number(x);
    if(x < 5) throw "too low";
    if(x > 10) throw "too high";
}
catch(err) {
    message.innerHTML = "Input is " + err;
}
```


## Scope
JavaScript variables have 3 types of scope:
- Block scope: `{}` can restrict the access of a variable. Only works for `const` and `let`
- Function scope: Local variables defined in a function. Applies to `var`, `const`, and `let`.
- Global scope: Globally defined variables. Also applis to all types of variables.

If you assign a value to a variable that has not been declared, it will automatically become a GLOBAL variable (except in Strict Mode).

With JavaScript, the global scope is the JavaScript environment.
In HTML, the global scope is the window object.
**Global variables defined with the var keyword belong to the window object**:
```javascript
var carName1 = "Volvo";
// code here can use window.carName
let carName2 = "Volvo";
// code here can not use window.carName
```

In a web browser, global variables are deleted when you close the browser window (or tab).

## Arrow
These are equivalent:
```javascript
// a
hello = function() {
  return "Hello World!";
}
// b
function hellow2() {
  return "Hello World!";
}
// c
hello = () => {
  return "Hello World!";
}
// d
hello = () => "Hello World!";
```


With a regular function `this` represents the object that calls the function.
```javascript
// Regular Function:
hello = function() {
  document.getElementById("demo").innerHTML += this;
}

// The window object calls the function:
window.addEventListener("load", hello);

// A button object calls the function:
document.getElementById("btn").addEventListener("click", hello);
```
With an arrow function `this` represents the owner of the function:
```javascript
hello = () => {
  document.getElementById("demo").innerHTML += this;
}

// The window object calls the function:
window.addEventListener("load", hello);

// A button object calls the function:
document.getElementById("btn").addEventListener("click", hello);
```


## Modules
Need to declare `module`
```javascript
<script type="module">
import message from "./message.js";
</script>
```
Need to declare variables the to export, so that they can be imported.

**Named import**: Much be imported in curly braces, e.g., `import {name} from "./message.js"`
```javascript
export {name, age};
export const name = "Jesse"; // can do this inline
```
**Default import**: No curly braces. Only one default export in a file.
```javascript
export default message;
```
## Performance
Accessing the HTML DOM is very slow, compared to other JavaScript statements.

Putting your scripts at the bottom of the page body lets the browser load the page first. While a script is downloading, the browser will not start any other downloads. In addition all parsing and rendering activity might be blocked. An alternative is to use `defer="true"` in the script tag.

## Async

### Callbacks
A callback is a function passed as an argument to another function.
Where callbacks really shine are in asynchronous functions, where one function has to wait for another function (like waiting for a file to load).

### Asynchronous JavaScript
**Functions running in parallel with other functions are called asynchronous**


A typical example is JavaScript `setTimeout()`.
```javascript
setTimeout(myFunction, 3000); // myFunction will be called after 3 seconds

// setInterval: repeat the function every ...
function myFunction() {
  document.getElementById("demo").innerHTML = "I love You !!";
}
```
Modern asynchronous programming relies on promises instead of callbacks.
### JavaScript Promises
>"I Promise a Result!"

"Producing code" is code that can take some time; "Consuming code" is code that must wait for the result.

A Promise is an Object that links Producing code and Consuming code
```javascript
let myPromise = new Promise(function(myResolve, myReject) {
// "Producing Code" (May take some time)
  myResolve(); // when successful
  myReject();  // when error
});

// "Consuming Code" (Must wait for a fulfilled Promise)
myPromise.then(
  function(value) { /* code if successful */ },
  function(error) { /* code if some error */ }
);
```
Example 1:
```javascript
let myPromise = new Promise(function(myResolve, myReject) {
  setTimeout(function() { myResolve("I love You !!"); }, 3000);
});

myPromise.then(function(value) {
  document.getElementById("demo").innerHTML = value;
});
```


Example 2:
```javascript
let myPromise = new Promise(function(myResolve, myReject) {
  let req = new XMLHttpRequest();
  req.open('GET', "mycar.htm");
  req.onload = function() {
    if (req.status == 200) {
      myResolve(req.response);
    } else {
      myReject("File not Found");
    }
  };
  req.send();
});

myPromise.then(
  function(value) {myDisplayer(value);},
  function(error) {myDisplayer(error);}
);
```
### Async/Await
```javascript
async function myFunction() {
  return "Hello";
}
myFunction().then(
  function(value) {myDisplayer(value);}
); // 怎么直接能写个then上去的？
```
The await keyword can only be used inside an async function.

Await pauses the execution and wait for a resolved promise before it continues:
```javascript
let value = await promise;
```
The execution of async function will "finish" immediately. The next line of code will be executed. 
## Primitive Vs. Objects
A primitive (primitive value, primitive data type) is data that is not an object and has no methods or properties. There are 7 primitive data types:

string
number
bigint
boolean
undefined
symbol
null

All primitives are immutable; that is, they cannot be altered. It is important not to confuse a primitive itself with a variable assigned a primitive value. 

Primitives have no methods but still behave as if they do. When properties are accessed on primitives, JavaScript auto-boxes the value into a wrapper object and accesses the property on that object instead. When you call methods on primitive, it's treated like an object, and later on convrted back to primitive when the methods is done running.

Objects all have a prototype chain.
Primitives have an object counter type.


## Object.create()

The Object.create() method creates a new object, using an existing object as the prototype of the newly created object.

```js
const person = {
  isHuman: false,
  printIntroduction: function() {
    console.log(`My name is ${this.name}. Am I human? ${this.isHuman}`);
  }
};

const me = Object.create(person);

me.name = 'Matthew'; // "name" is a property set on "me", but not on "person"
me.isHuman = true; // inherited properties can be overwritten

me.printIntroduction();
// expected output: "My name is Matthew. Am I human? true"

```

## Function as first class citizens (whatever you can do with objects, you can also do with functions).
1. you can assign a function to a variable and turn a function declaration to a function expression.
2. we can store a function inside an array.
3. you can pass in a function as a argument to another function.
4. return a function from another function.

## Default Parameters, Rest Parameters, Implicit Parameters
There's no overloading in javaScript, no matter how many arguments we pass into the function, the same function will be invoked everytime. 

Default Parameter: if a paramter is not passed into the function, set a default value for the missing parameter. Used if you pass in too little paramters.

Rest Parameters: if you pass in too many parameters, more than the function specified, you can catch all of the parameters with ...theArgs.

-- access it like console.log(theArgs) inside of the function.
For ex: 
```js
function findLargest(x, y, ...more) {
  let largest = x;
  for (let i = 0; i < more.length; i++) {
    if (more[i] > largest) largest = more[i]
  }
  if (y > largest) largest = y
}
```

Implicit Parameter: 'this' is a implicit parameter for each function, also arguments. console.log(arguments) will give you the arguments passed into the function.


## Call vs Apply vs Bind 
you can write object methods separately and use call, apply, or bind to use the methods on a certain object. 


Call and apply both attach a function to an object, but the difference is call takes multiple parameters after the object parameter, while apply takes only 1 array as the second argument.
For ex: 

```js
var obj = {num: 2}
var addToThis = function(a, b) {
  return this.num + a + b;
}

// takes 2 arguments: 1: the object to want to apply this function to, 2: the argument you want to pass to this function. 
addToThis.call(obj, 3, 4); //9

```

bind actually attaches a method to a object, and returns back a function. You can call the returned back function and pass in the arguments. 

For ex: 
```js
var obj = {num: 2}
var addToThis = function(a, b) {
  return this.num + a + b;
}

var bound = addToThis.bind(obj);
console.log(bound(2,3)); //8
```

# Object Oriented Programming - JS
[[Prototype]]: In JavaScript, objects have a special hidden property [[Prototype]] (as named in the specification), that is either null or references another object. That object is called “a prototype”.
## Creating a constructor function
```js
function User(name, interests) {
  this.name = name;
  this.interests = interests;
}

let me = new User("Caleb", [cooking, drawing]);
let you = new User("Joe", [dancing, singing]);

```
We do not add methods onto constructor function, since that copies the method for every single object.
This wastes memory is you have a huge application that have many objects.

## Factory Functions
```js
function newUser(name, interests) {
  let person = {
    name: name, 
    interests: interests
   };
   return person;
}

let me = newUser("Caleb", [cooking, drawing]);
```
## Proper Way to give object methods when it comes to constructor functions
We mentioned before that we do not add methods onto constructor function, so the correct way is to add the methods to the parent object for the User.
```js
User.prototype.greet = function() {
  console.log("my name is " + this.name,  this.interests);

```
Here, only one method is created.


## Explain how prototypal inheritance works
When we read a property from object, and it’s missing, JavaScript automatically takes it from the prototype. In programming, this is called “prototypal inheritance”. 

inheritance: allows one object to take parts of another object and inherit them as their own. Prototype is just an object that another object could inherit from. 

There is a default prototype that things inherit from: Object.prototype, when we create an object, this will be availble to inherit from.

-- When you create an object with object literal, it will invoke a new Object() constrcutor, which is where the Object.prototype comes from. When you create an object with the literal, called x, the object.prototype will transfer to the new object you just created. You can check this because Object.getProtytypeOf(x) === Object.prototype. 


All JavaScript objects have a `__proto__` property with the exception of objects created with `__Object.create(null)__`, that is a reference to another object, which is called the object's "prototype". When a property is accessed on an object and if the property is not found on that object, the JavaScript engine looks at the object's `__proto__`, and the `__proto__`'s `__proto__` and so on, until it finds the property defined on one of the `__proto__`s or until it reaches the end of the prototype chain. This behavior simulates classical inheritance, but it is really more of [delegation than inheritance](https://davidwalsh.name/javascript-objects).

#### Example of Prototypal Inheritance

```js
function Parent() {
  this.name = 'Parent';
}

Parent.prototype.greet = function () {
  console.log('Hello from ' + this.name);
};

const child = Object.create(Parent.prototype);

child.cry = function () {
  console.log('waaaaaahhhh!');
};

child.cry();
// waaaaaahhhh!

child.greet();
// hello from Parent

child.constructor;
// ƒ Parent() {
//   this.name = 'Parent';
// }

child.constructor.name;
// 'Parent'
```
Things to note are:

- `.greet` is not defined on the _child_, so the engine goes up the prototype chain and finds `.greet` off the inherited from _Parent_.
- We need to call `Object.create` in one of following ways for the prototype methods to be inherited:
  - Object.create(Parent.prototype);
  - Object.create(new Parent(null));
  - Object.create(objLiteral);
  - Currently, `child.constructor` is pointing to the `Parent`:

If we'd like to correct this, one option would be to do:

```js
function Parent() {
  this.name = 'Parent';
}

Parent.prototype.greet = function () {
  console.log('Hello from ' + this.name);
};

function Child() {
  Parent.call(this);
  this.name = 'Child';
}

Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child;

const child = new Child();

child.greet();
// hello from Child

child.constructor.name;
// 'Child'
```

Ex2: 
```js
let animal = {
  eats: true
};
let rabbit = {
  jumps: true
};

rabbit.__proto__ = animal; // sets rabbit.[[Prototype]] = animal
```
Here we can say that "animal is the prototype of rabbit" or "rabbit prototypically inherits from animal".
So if animal has a lot of useful properties and methods, then they become automatically available in rabbit. Such properties are called “inherited”.


The prototype chain can be longer:
```js
let animal = {
  eats: true,
  walk() {
    alert("Animal walk");
  }
};

let rabbit = {
  jumps: true,
  __proto__: animal
};

let longEar = {
  earLength: 10,
  __proto__: rabbit
};

// walk is taken from the prototype chain
longEar.walk(); // Animal walk
alert(longEar.jumps); // true (from rabbit)
```

There are only two limitations:
- The references can’t go in circles. JavaScript will throw an error if we try to assign __proto__ in a circle.
- The value of __proto__ can be either an object or null. Other types are ignored.

- There can be only one [[Prototype]]. An object may not inherit from two others.
- Please note that __proto__ is not the same as the internal [[Prototype]] property. It’s a getter/setter for [[Prototype]].

- If we had other objects, like bird, snake, etc., inheriting from animal, they would also gain access to methods of animal. But this in each method call would be the corresponding object, evaluated at the call-time (before dot), not animal. So when we write data into this, it is stored into these objects.

- The for..in loop iterates over inherited properties too.

As a result, methods are shared, but the object state is not.
- this is not affected by prototypes at all: No matter where the method is found: in an object or its prototype. In a method call, this is always the object before the dot.
###### References

- http://dmitrysoshnikov.com/ecmascript/javascript-the-core/
- https://www.quora.com/What-is-prototypal-inheritance/answer/Kyle-Simpson
- https://davidwalsh.name/javascript-objects
- https://crockford.com/javascript/prototypal.html
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain


### Setting prototype of an object for object literals;
```js
let user = {
  active: true
};
 
let teacher = {
  teaching: ["math", "science"]
}

Object.setPrototypeOf(teacher, user);
```
You are saying "make teacher inherit from user";
### Override in Prototypal Inheritance
```js
let user = {
  active: true
};
 
let student = {
  major: "English"
};

let teacher = {
  teaching: ["math", "science"]
};

Object.setPrototypeOf(student, user);
Object.setPrototypeOf(teacher, user);

student.active = false;

console.log(teacher) // let teacher = {teaching: ["math", "science"]};
console.log(student) // let student = {major: "English", active: false};
```

When you look for a property, it first looks at the object itself, and then move up in the prototype chain until it finds it or until we run out of prototype. 

student.active is set directly on the student object.

## Use Constructor Function instead of Object Literal for prototype inheritance
```js
function User () {
  this.active: false;
};
 
User.prototype.sayHello = function() {
  return this.name + " say hi!";
};

function Student(name, major) {
  this.name = name;
  this.major = major;
};

function Teacher(name, teaching) {
  this.name = name;
  this.teaching = teaching;
};
  
// to make user the prototype of student and teacher:
Student.prototype = new User();
Teacher.prototype = new User();

let student = new Student("Ivy Yu", "English");
let teacher = new Teacher("Jonathan Cen", ["Com Sci"]);
```

### What do you think of AMD vs CommonJS??

Both are ways to implement a module system, which was not natively present in JavaScript until ES2015 came along. CommonJS is synchronous while AMD (Asynchronous Module Definition) is obviously asynchronous. CommonJS is designed with server-side development in mind while AMD, with its support for asynchronous loading of modules, is more intended for browsers.

I find AMD syntax to be quite verbose and CommonJS is closer to the style you would write import statements in other languages. Most of the time, I find AMD unnecessary, because if you served all your JavaScript into one concatenated bundle file, you wouldn't benefit from the async loading properties. Also, CommonJS syntax is closer to Node style of writing modules and there is less context-switching overhead when switching between client side and server side JavaScript development.

I'm glad that with ES2015 modules, that has support for both synchronous and asynchronous loading, we can finally just stick to one approach. Although it hasn't been fully rolled out in browsers and in Node, we can always use transpilers to convert our code.

###### References

- https://auth0.com/blog/javascript-module-systems-showdown/
- https://stackoverflow.com/questions/16521471/relation-between-commonjs-amd-and-requirejs


### Explain why the following doesn't work as an IIFE: `function foo(){ }();`. What needs to be changed to properly make it an IIFE?

IIFE stands for Immediately Invoked Function Expressions. The JavaScript parser reads `function foo(){ }();` as `function foo(){ }` and `();`, where the former is a _function declaration_ and the latter (a pair of parentheses) is an attempt at calling a function but there is no name specified, hence it throws `Uncaught SyntaxError: Unexpected token )`.

Here are two ways to fix it that involves adding more parentheses: `(function foo(){ })()` and `(function foo(){ }())`. Statements that begin with `function` are considered to be _function declarations_; by wrapping this function within `()`, it becomes a _function expression_ which can then be executed with the subsequent `()`. These functions are not exposed in the global scope and you can even omit its name if you do not need to reference itself within the body.

You might also use `void` operator: `void function foo(){ }();`. Unfortunately, there is one issue with such approach. The evaluation of given expression is always `undefined`, so if your IIFE function returns anything, you can't use it. An example:

```js
const foo = void (function bar() {
  return 'foo';
})();

console.log(foo); // undefined
```

###### References

- http://lucybain.com/blog/2014/immediately-invoked-function-expression/
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/void


### Can you describe the main difference between a `.forEach` loop and a `.map()` loop and why you would pick one versus the other?

To understand the differences between the two, let's look at what each function does.

**`forEach`**

- Iterates through the elements in an array.
- Executes a callback for each element.
- Does not return a value.

```js
const a = [1, 2, 3];
const doubled = a.forEach((num, index) => {
  // Do something with num and/or index.
});

// doubled = undefined
```

**`map`**

- Iterates through the elements in an array.
- "Maps" each element to a new element by calling the function on each element, creating a new array as a result.

```js
const a = [1, 2, 3];
const doubled = a.map((num) => {
  return num * 2;
});

// doubled = [2, 4, 6]
```

The main difference between `.forEach` and `.map()` is that `.map()` returns a new array. If you need the result, but do not wish to mutate the original array, `.map()` is the clear choice. If you simply need to iterate over an array, `forEach` is a fine choice.

###### References

- https://codeburst.io/javascript-map-vs-foreach-f38111822c0f


### How do you organize your code? (module pattern, classical inheritance?)

In the past, I've used Backbone for my models which encourages a more OOP approach, creating Backbone models and attaching methods to them.

The module pattern is still great, but these days, I use React/Redux which utilize a single-directional data flow based on Flux architecture. I would represent my app's models using plain objects and write utility pure functions to manipulate these objects. State is manipulated using actions and reducers like in any other Redux application.

I avoid using classical inheritance where possible. When and if I do, I stick to [these rules](https://medium.com/@dan_abramov/how-to-use-classes-and-sleep-at-night-9af8de78ccb4).


### What's the difference between host objects and native objects?

Native objects are objects that are part of the JavaScript language defined by the ECMAScript specification, such as `String`, `Math`, `RegExp`, `Object`, `Function`, etc.

Host objects are provided by the runtime environment (browser or Node), such as `window`, `XMLHTTPRequest`, etc.

###### References

- https://stackoverflow.com/questions/7614317/what-is-the-difference-between-native-objects-and-host-objects

### Difference between: `function Person(){}`, `var person = Person()`, and `var person = new Person()`?

This question is pretty vague. My best guess at its intention is that it is asking about constructors in JavaScript. Technically speaking, `function Person(){}` is just a normal function declaration. The convention is to use PascalCase for functions that are intended to be used as constructors.

`var person = Person()` invokes the `Person` as a function, and not as a constructor. Invoking as such is a common mistake if the function is intended to be used as a constructor. Typically, the constructor does not return anything, hence invoking the constructor like a normal function will return `undefined` and that gets assigned to the variable intended as the instance.

`var person = new Person()` creates an instance of the `Person` object using the `new` operator, which inherits from `Person.prototype`. An alternative would be to use `Object.create`, such as: `Object.create(Person.prototype)`.

```js
function Person(name) {
  this.name = name;
}

var person = Person('John');
console.log(person); // undefined
console.log(person.name); // Uncaught TypeError: Cannot read property 'name' of undefined

var person = new Person('John');
console.log(person); // Person { name: "John" }
console.log(person.name); // "john"
```

###### References

- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new


### What's the difference between `.call` and `.apply`?

Both `.call` and `.apply` are used to invoke functions and the first parameter will be used as the value of `this` within the function. However, `.call` takes in comma-separated arguments as the next arguments while `.apply` takes in an array of arguments as the next argument. An easy way to remember this is C for `call` and comma-separated and A for `apply` and an array of arguments.

```js
function add(a, b) {
  return a + b;
}

console.log(add.call(null, 1, 2)); // 3
console.log(add.apply(null, [1, 2])); // 3
```


### Explain `Function.prototype.bind`.

Taken word-for-word from [MDN](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind):

> The `bind()` method creates a new function that, when called, has its `this` keyword set to the provided value, with a given sequence of arguments preceding any provided when the new function is called.

In my experience, it is most useful for binding the value of `this` in methods of classes that you want to pass into other functions. This is frequently done in React components.

###### References

- https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind


### When would you use `document.write()`?

`document.write()` writes a string of text to a document stream opened by `document.open()`. When `document.write()` is executed after the page has loaded, it will call `document.open` which clears the whole document (`<head>` and `<body>` removed!) and replaces the contents with the given parameter value. Hence it is usually considered dangerous and prone to misuse.

There are some answers online that explain `document.write()` is being used in analytics code or [when you want to include styles that should only work if JavaScript is enabled](https://www.quirksmode.org/blog/archives/2005/06/three_javascrip_1.html). It is even being used in HTML5 boilerplate to [load scripts in parallel and preserve execution order](https://github.com/paulirish/html5-boilerplate/wiki/Script-Loading-Techniques#documentwrite-script-tag)! However, I suspect those reasons might be outdated and in the modern day, they can be achieved without using `document.write()`. Please do correct me if I'm wrong about this.

###### References

- https://www.quirksmode.org/blog/archives/2005/06/three_javascrip_1.html
- https://github.com/h5bp/html5-boilerplate/wiki/Script-Loading-Techniques#documentwrite-script-tag


### What's the difference between feature detection, feature inference, and using the UA string?

**Feature Detection**

Feature detection involves working out whether a browser supports a certain block of code, and running different code depending on whether it does (or doesn't), so that the browser can always provide a working experience rather crashing/erroring in some browsers. For example:

```js
if ('geolocation' in navigator) {
  // Can use navigator.geolocation
} else {
  // Handle lack of feature
}
```

[Modernizr](https://modernizr.com/) is a great library to handle feature detection.

**Feature Inference**

Feature inference checks for a feature just like feature detection, but uses another function because it assumes it will also exist, e.g.:

```js
if (document.getElementsByTagName) {
  element = document.getElementById(id);
}
```

This is not really recommended. Feature detection is more foolproof.

**UA String**

This is a browser-reported string that allows the network protocol peers to identify the application type, operating system, software vendor or software version of the requesting software user agent. It can be accessed via `navigator.userAgent`. However, the string is tricky to parse and can be spoofed. For example, Chrome reports both as Chrome and Safari. So to detect Safari you have to check for the Safari string and the absence of the Chrome string. Avoid this method.

###### References

- https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Feature_detection
- https://stackoverflow.com/questions/20104930/whats-the-difference-between-feature-detection-feature-inference-and-using-th
- https://developer.mozilla.org/en-US/docs/Web/HTTP/Browser_detection_using_the_user_agent


### Explain Ajax in as much detail as possible.

Ajax (asynchronous JavaScript and XML) is a set of web development techniques using many web technologies on the client side to create asynchronous web applications. With Ajax, web applications can send data to and retrieve from a server asynchronously (in the background) without interfering with the display and behavior of the existing page. By decoupling the data interchange layer from the presentation layer, Ajax allows for web pages, and by extension web applications, to change content dynamically without the need to reload the entire page. In practice, modern implementations commonly use JSON instead of XML, due to the advantages of JSON being native to JavaScript.

The `XMLHttpRequest` API is frequently used for the asynchronous communication or these days, the `fetch` API.

###### References

- https://en.wikipedia.org/wiki/Ajax_(programming)
- https://developer.mozilla.org/en-US/docs/AJAX


### What are the advantages and disadvantages of using Ajax?

**Advantages**

- Better interactivity. New content from the server can be changed dynamically without the need to reload the entire page.
- Reduce connections to the server since scripts and stylesheets only have to be requested once.
- State can be maintained on a page. JavaScript variables and DOM state will persist because the main container page was not reloaded.
- Basically most of the advantages of an SPA.

**Disadvantages**

- Dynamic webpages are harder to bookmark.
- Does not work if JavaScript has been disabled in the browser.
- Some webcrawlers do not execute JavaScript and would not see content that has been loaded by JavaScript.
- Webpages using Ajax to fetch data will likely have to combine the fetched remote data with client-side templates to update the DOM. For this to happen, JavaScript will have to be parsed and executed on the browser, and low-end mobile devices might struggle with this.
- Basically most of the disadvantages of an SPA.


### Explain how JSONP works (and how it's not really Ajax).

JSONP (JSON with Padding) is a method commonly used to bypass the cross-domain policies in web browsers because Ajax requests from the current page to a cross-origin domain is not allowed.

JSONP works by making a request to a cross-origin domain via a `<script>` tag and usually with a `callback` query parameter, for example: `https://example.com?callback=printData`. The server will then wrap the data within a function called `printData` and return it to the client.

```html
<!-- https://mydomain.com -->
<script>
  function printData(data) {
    console.log(`My name is ${data.name}!`);
  }
</script>

<script src="https://example.com?callback=printData"></script>
```

```js
// File loaded from https://example.com?callback=printData
printData({name: 'Yang Shun'});
```

The client has to have the `printData` function in its global scope and the function will be executed by the client when the response from the cross-origin domain is received.

JSONP can be unsafe and has some security implications. As JSONP is really JavaScript, it can do everything else JavaScript can do, so you need to trust the provider of the JSONP data.

These days, [CORS](http://en.wikipedia.org/wiki/Cross-origin_resource_sharing) is the recommended approach and JSONP is seen as a hack.

###### References

- https://stackoverflow.com/a/2067584/1751946


### Have you ever used JavaScript templating? If so, what libraries have you used?

Yes. Handlebars, Underscore, Lodash, AngularJS, and JSX. I disliked templating in AngularJS because it made heavy use of strings in the directives and typos would go uncaught. JSX is my new favorite as it is closer to JavaScript and there is barely any syntax to learn. Nowadays, you can even use ES2015 template string literals as a quick way for creating templates without relying on third-party code.

```js
const template = `<div>My name is: ${name}</div>`;
```

However, do be aware of a potential XSS in the above approach as the contents are not escaped for you, unlike in templating libraries.


### Describe event bubbling.

When an event triggers on a DOM element, it will attempt to handle the event if there is a listener attached, then the event is bubbled up to its parent and the same thing happens. This bubbling occurs up the element's ancestors all the way to the `document`. Event bubbling is the mechanism behind event delegation.

### What's the difference between an "attribute" and a "property"?

Attributes are defined on the HTML markup but properties are defined on the DOM. To illustrate the difference, imagine we have this text field in our HTML: `<input type="text" value="Hello">`.

```js
const input = document.querySelector('input');
console.log(input.getAttribute('value')); // Hello
console.log(input.value); // Hello
```

But after you change the value of the text field by adding "World!" to it, this becomes:

```js
console.log(input.getAttribute('value')); // Hello
console.log(input.value); // Hello World!
```

###### References

- https://stackoverflow.com/questions/6003819/properties-and-attributes-in-html


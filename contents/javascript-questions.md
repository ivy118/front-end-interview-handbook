## Phases of JavaScript Event
There are three different phases during lifecycle of an JavaScript event.

Capturing Phase
Target Phase
Bubbling Phase

Capturing Phase is when event goes down to the element. Target phase is when event reach the element and Bubbling phase is when the event bubbles up from the element.

- you can add multiple event handlers to one single element.

```html
<body>
	<div class = "grandparent">
		<div class = "parent">
			<div class = "child"></div>
		</div>
	</div>
</body>
```

```js
const grandparent = document.querySelector(".grandparent")
const parent = document.querySelector(".parent")
const child = document.querySelector(".child")

grandparent.addEventListener("click", e => {
	console.log("Grandparent 1")
})

parent.addEventListener("click", e => {
	console.log("parent 1")
})

child.addEventListener("click", e => {
	console.log("child 1")
})
```

Since the child is in the parent, when we click on the child, we technically clicked on all of the parent element too so all parent listeners will fire too: this is called event bubbling. 

Before the bubbling event happens, the events actually travels from outward to inward, then travels back from inward to outward. 

Event capturing is the opposite: it moves from the outward to most inward element.

-- if you want you event to be a capture event, you can pass in a third parameter to the event listner, like {capture: true}.

There are ways to stop the event from bubbling all the way up or capturing the way down: event propagation: e.stopPropagation() method. 

-- What if you just want to run th events once, and never run it again. 
for the third parameter, you can pass in {once: true}.

-- You can remove a event handler after certain time by the removeEventListener function. 
Syntax: parent.removeEventListener("click", printHi)


## Explain event delegation

Event delegation is a technique involving adding event listeners to a parent element instead of adding them to the descendant elements. Event Delegation is basically a pattern to handle events efficiently. That event listener analyzes bubbled events to find a match on child elements.

- Memory footprint goes down because only one single handler is needed on the parent element, rather than having to attach event handlers on each descendant.
- There is no need to unbind the handler from elements that are removed and to bind the event for new elements.

Example: if we want every div in the document to console.log "hi" when it's clicked. 
You can add a click handler to document itself, and specify that you want only all div tags to have this listener.

```js
document.addEventListener("click", e => {
	if (e.target.matches('div')) {
		console.log('hi')
	}
})
```

## What is `"use strict";`? What are the advantages and disadvantages to using it?

'use strict' changes previously accepted "bad syntax" into real errors. Strict mode is a way to opt into a restricted variant of JavaScript.

Advantages:

- Makes it impossible to accidentally create global variables.
- Makes assignments which would otherwise silently fail to throw an exception.
- Makes attempts to delete undeletable properties throw an exception (where before the attempt would simply have no effect).
- Requires that function parameter names be unique.
- `this` is undefined in the global context.
- It catches some common coding bloopers, throwing exceptions.
- It disables features that are confusing or poorly thought out.

Disadvantages:

- Many missing features that some developers might be used to.
- No more access to `function.caller` and `function.arguments`.
- Concatenation of scripts written in different strict modes might cause issues.

Overall, I think the benefits outweigh the disadvantages, and I never had to rely on the features that strict mode blocks. I would recommend using strict mode.

```js
x = 3.14;       // This will not cause an error.
myFunction();

function myFunction() {
  "use strict";
  y = 3.14;   // This will cause an error
}
```

## Can you give an example for destructuring an object or an array?

Destructuring is an expression available in ES6 which enables a succinct and convenient way to extract values of Objects or Arrays and place them into distinct variables.

**Array destructuring**

```js
// Variable assignment.
const foo = ['one', 'two', 'three'];

const [one, two, three] = foo;
console.log(one); // "one"
console.log(two); // "two"
console.log(three); // "three"
```

```js
// Swapping variables
let a = 1;
let b = 3;

[a, b] = [b, a];
console.log(a); // 3
console.log(b); // 1
```

**Object destructuring**

```js
// Variable assignment.
const o = {p: 42, q: true};
const {p, q} = o;

console.log(p); // 42
console.log(q); // true
```

## What is a closure, and how/why would you use one?

A closure is the combination of a function and the lexical environment within which that function was declared. Closures are functions that have access to the outer (enclosing) function's variables—scope chain even after the outer function has returned. 

For example:

```js
function outerFunction(outerVariable) {
	return function innerFunction(innerVariable) {
		console.log("outer Variable: " + outerVariable) 
		console.log("inner Variable: " + innerVariable)
	}
}

const newFunc = outerFunction('outside');
newFunc('inside'); // outer Variable: outside  inner Variable: inside
```

The inner function saves the variables that belongs to the outer function and have access to those variables at all times. 

Closures are important because they control what is and isn’t in scope in a particular function, along with which variables are shared between sibling functions in the same containing scope. 

A closure gives you access to an outer function’s scope from an inner function.
**Why would you use one?**

- Data privacy / emulating private methods with closures. Commonly used in the [module pattern](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#modulepatternjavascript).
When you use closures for data privacy, the enclosed variables are only in scope within the containing (outer) function. You can’t get at the data from an outside scope

## What's the difference between a variable that is: `null`, `undefined` or undeclared? How would you go about checking for any of these states?

**Undeclared** variables are created when you assign a value to an identifier that is not previously created using `var`, `let` or `const`. Undeclared variables will be defined globally, outside of the current scope. In strict mode, a `ReferenceError` will be thrown when you try to assign to an undeclared variable. Undeclared variables are bad just like how global variables are bad. Avoid them at all cost! To check for them, wrap its usage in a `try`/`catch` block.

```js
function foo() {
  x = 1; // Throws a ReferenceError in strict mode
}

foo();
console.log(x); // 1
```

A variable that is `undefined` is a variable that has been declared, but not assigned a value. It is of type `undefined`. If a function does not return any value as the result of executing it is assigned to a variable, the variable also has the value of `undefined`. To check for it, compare using the strict equality (`===`) operator or `typeof` which will give the `'undefined'` string. Note that you should not be using the abstract equality operator to check, as it will also return `true` if the value is `null`.

```js
var foo;
console.log(foo); // undefined
console.log(foo === undefined); // true
console.log(typeof foo === 'undefined'); // true

console.log(foo == null); // true. Wrong, don't use this to check!

function bar() {}
var baz = bar();
console.log(baz); // undefined
```

A variable that is `null` will have been explicitly assigned to the `null` value. It represents no value and is different from `undefined` in the sense that it has been explicitly assigned. To check for `null,` simply compare using the strict equality operator. Note that like the above, you should not be using the abstract equality operator (`==`) to check, as it will also return `true` if the value is `undefined`.

```js
var foo = null;
console.log(foo === null); // true
console.log(typeof foo === 'object'); // true

console.log(foo == undefined); // true. Wrong, don't use this to check!
```

** notice that null is an object in JS.

## What's a typical use case for anonymous functions?

They can be used in IIFEs to encapsulate some code within a local scope so that variables declared in it do not leak to the global scope.

```js
(function () {
  // Some code here.
})();
```

As a callback that is used once and does not need to be used anywhere else. The code will seem more self-contained and readable when handlers are defined right inside the code calling them, rather than having to search elsewhere to find the function body.

```js
setTimeout(function () {
  console.log('Hello world!');
}, 1000);
```

Arguments to functional programming constructs or Lodash (similar to callbacks).

```js
const arr = [1, 2, 3];
const double = arr.map(function (el) {
  return el * 2;
});
console.log(double); // [2, 4, 6]
```

### Explain how `this` works in JavaScript
'this' is the object that is executing the current function .

1. If a function is called as a method, such as `obj.method()` — `this` is the object that the function is a property of.
2. If the funtion is a regualr function, 'this' refers to the global object, which is the window object in the browser. If in strict mode (`'use strict'`), `this` will be `undefined` instead of the global object.
3. If the `new` keyword is used when calling the function, `this` inside the function is a brand new object.
Ex:
```js
function Video(title) {
	this.title = title;
	console.log(this)
}

const v = new Video('b'); // {}
```

4. If the function is an callback function, it's treated like a regular function, so 'this' would represent the window object.
```js
const video = {
	title: 'a',
	tags: ['a','b','c'],
	showTags() {
		this.tags.forEach(function(tag) {
			console.log(this, tag);
		});
	}
};

video.showTags(); //window
```

5. if the function is an arrow function, they don’t have their “own” this. If we reference this from such a function, it’s taken from the outer “normal” function. That’s a special feature of arrow functions, it’s useful when we actually do not want to have a separate this, but rather to take it from the outer context. 

For Ex: 
```js
const video = {
	title: 'a',
	tags: ['a','b','c'],
	showTags() {
		this.tags.forEach((tag) =>
            {console.log(this, tag)})  //{title: 'a', tags: Array(3), showTags: ƒ} 'a'...
	}
};

video.showTags(); 
```

How do we actually have access to the video object inside of the arrow function then? 
we can pass in the object we want to refer to as the second parameter of the showTags() method.

```js
const video = {
	title: 'a',
	tags: ['a','b','c'],
	showTags() {
		this.tags.forEach(function(tag) {
			console.log(this, tag);
		}, this);
	}
};

video.showTags(); //video
```

5. If `apply`, `call`, or `bind` are used to call/create a function, `this` inside the function is the object that is passed in as the argument.
6. If multiple of the above rules apply, the rule that is higher wins and will set the `this` value.

Exercise: What is the result of accessing its ref? Why?
```js
function makeUser() {
  return {
    name: "John",
    ref: this
  };
}

let user = makeUser();
alert( user.ref.name ); // What's the result?
```
Answer: an error. That’s because rules that set this do not look at object definition. Here the value of this inside makeUser() is undefined, because it is called as a function, not as a method with “dot” syntax.
The value of this is one for the whole function, code blocks and object literals do not affect it.

Here’s the opposite case:
```js
function makeUser() {
  return {
    name: "John",
    ref() {
      return this;
    }
  };
}

let user = makeUser();
alert( user.ref().name ); // John
```

#### Can you give an example of one of the ways that working with this has changed in ES6?

ES6 allows you to use [arrow functions](http://2ality.com/2017/12/alternate-this.html#arrow-functions) which uses the [enclosing lexical scope](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#No_separate_this). This is usually convenient, but does prevent the caller from controlling context via `.call` or `.apply`—the consequences being that a library such as `jQuery` will not properly bind `this` in your event handler functions. Thus, it's important to keep this in mind when refactoring large legacy applications.

## What are the differences between variables created using `let`, `var` or `const`?

Variables declared using the `var` keyword are scoped to the function in which they are created, or if created outside of any function, to the global object. `let` and `const` are _block scoped_, meaning they are only accessible within the nearest set of curly braces (function, if-else block, or for-loop).

```js
function foo() {
  // All variables are accessible within functions.
  var bar = 'bar';
  let baz = 'baz';
  const qux = 'qux';

  console.log(bar); // bar
  console.log(baz); // baz
  console.log(qux); // qux
}

console.log(bar); // ReferenceError: bar is not defined
console.log(baz); // ReferenceError: baz is not defined
console.log(qux); // ReferenceError: qux is not defined
```

```js
function print() {
	if (true) {
	  var bar = 'bar';
	  let baz = 'baz';
	  const qux = 'qux';
	}
}

// var declared variables are accessible anywhere in the function scope.
console.log(bar); // bar
// let and const defined variables are not accessible outside of the block they were defined in.
console.log(baz); // ReferenceError: baz is not defined
console.log(qux); // ReferenceError: qux is not defined
```

`var` allows variables to be hoisted, meaning they can be referenced in code before they are declared. `let` and `const` will not allow this, instead throwing an error.

```js
console.log(foo); // undefined

var foo = 'foo';

console.log(baz); // ReferenceError: can't access lexical declaration 'baz' before initialization

let baz = 'baz';

console.log(bar); // ReferenceError: can't access lexical declaration 'bar' before initialization

const bar = 'bar';
```

Redeclaring a variable with `var` will not throw an error, but `let` and `const` will.

```js
var foo = 'foo';
var foo = 'bar';
console.log(foo); // "bar"

let baz = 'baz';
let baz = 'qux'; // Uncaught SyntaxError: Identifier 'baz' has already been declared
```

`let` and `const` differ in that `let` allows reassigning the variable's value while `const` does not.

```js
// This is fine.
let foo = 'foo';
foo = 'bar';

// This causes an exception.
const baz = 'baz';
baz = 'qux';
```

## Explain "hoisting".

Hoisting is a term used to explain the behavior of variable declarations in your code. Variables declared or initialized with the `var` keyword will have their declaration "moved" up to the top of their module/function-level scope, which we refer to as hoisting. However, only the declaration is hoisted, the assignment (if there is one), will stay where it is.

Note that the declaration is not actually moved - the JavaScript engine parses the declarations during compilation and becomes aware of declarations and their scopes. It is just easier to understand this behavior by visualizing the declarations as being hoisted to the top of their scope. Let's explain with a few examples.

```js
console.log(foo); // undefined
var foo = 1;
console.log(foo); // 1
```

Function declarations have the body hoisted while the function expressions (written in the form of variable declarations) only has the variable declaration hoisted.

```js
// Function Declaration
console.log(foo); // [Function: foo]
foo(); // 'FOOOOO'
function foo() {
  console.log('FOOOOO');
}
console.log(foo); // [Function: foo]

// Function Expression
console.log(bar); // undefined
bar(); // Uncaught TypeError: bar is not a function
var bar = function () {
  console.log('BARRRR');
};
console.log(bar); // [Function: bar]
```

Variables declared via `let` and `const` are hoisted as well. However, unlike `var` and `function`, they are not initialized and accessing them before the declaration will result in a `ReferenceError` exception. The variable is in a "temporal dead zone" from the start of the block until the declaration is processed.

```js
x; // undefined
y; // Reference error: y is not defined

var x = 'local';
let y = 'local';
```


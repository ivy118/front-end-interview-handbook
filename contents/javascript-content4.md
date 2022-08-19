## Arrow Functions Revisited

Not having this naturally means another limitation: arrow functions can’t be used as constructors. They can’t be called with new.

There’s a subtle difference between an arrow function => and a regular function called with .bind(this):

.bind(this) creates a “bound version” of the function.
The arrow => doesn’t create any binding. The function simply doesn’t have this. The lookup of this is made exactly the same way as a regular variable search: in the outer lexical environment.


## Class
The “class” syntax
```js
class User {
  constructor(name) {
    this.name = name;
  }
  sayHi() {
    alert(this.name);
  }
}
// Usage:
let user = new User("John");
user.sayHi();
```
In JavaScript, a class is a kind of function.
```js
class User {
  constructor(name) { this.name = name; }
  sayHi() { alert(this.name); }
}

// proof: User is a function
alert(typeof User); // function
```
- Class methods are non-enumerable. A class definition sets enumerable flag to false for all methods in the "prototype".
- That’s good, because if we for..in over an object, we usually don’t want its class methods.

“Class fields” is a syntax that allows to add any properties.

For instance, let’s add name property to class User:
```js
class User {
  name = "John";

  sayHi() {
    alert(`Hello, ${this.name}!`);
  }
}
new User().sayHi(); // Hello, John!
```
Making bound methods with class fields: 
```js
class Button {
  constructor(value) {
    this.value = value;
  }

  click() {
    alert(this.value);
  }
}

let button = new Button("hello");
setTimeout(button.click, 1000); // undefined
```
To fix this, Pass a wrapper-function, such as setTimeout(() => button.click(), 1000).

### Class Inheritances:
```js
//parent class:
class Animal {
  constructor(name) {
    this.speed = 0;
    this.name = name;
  }
  run(speed) {
    this.speed = speed;
    alert(`${this.name} runs with speed ${this.speed}.`);
  }
  stop() {
    this.speed = 0;
    alert(`${this.name} stands still.`);
  }
}

let animal = new Animal("My animal");
```
```js 
// child class
class Rabbit extends Animal {
  hide() {
    alert(`${this.name} hides!`);
  }
}

let rabbit = new Rabbit("White Rabbit");

rabbit.run(5); // White Rabbit runs with speed 5.
```
rabbit.hide(); // White Rabbit hides!

### Overriding a method
Usually, we don’t want to totally replace a parent method, but rather to build on top of it to tweak or extend its functionality.
Classes provide "super" keyword for that.

super.method(...) to call a parent method.
super(...) to call a parent constructor (inside our constructor only).

```js
class Animal {
  constructor(name) {
    this.speed = 0;
    this.name = name;
  }
  run(speed) {
    this.speed = speed;
    alert(`${this.name} runs with speed ${this.speed}.`);
  }
  stop() {
    this.speed = 0;
    alert(`${this.name} stands still.`);
  }
}

class Rabbit extends Animal {
  hide() {
    alert(`${this.name} hides!`);
  }

  stop() {
    super.stop(); // call parent stop
    this.hide(); // and then hide
  }
}

let rabbit = new Rabbit("White Rabbit");
rabbit.run(5); // White Rabbit runs with speed 5.
rabbit.stop(); // White Rabbit stands still. White Rabbit hides!
```
Arrow functions have no super.

### Overriding constructor
Until now, Rabbit did not have its own constructor.
According to the specification, if a class extends another class and has no constructor, then the following “empty” constructor is generated:
```js
class Rabbit extends Animal {
  // generated for extending classes without own constructors
  constructor(...args) {
    super(...args);
  }
}
```
As we can see, it basically calls the parent constructor passing it all the arguments. That happens if we don’t write a constructor of our own.

Now let’s add a custom constructor to Rabbit. It will specify the earLength in addition to name:
```js
class Animal {
  constructor(name) {
    this.speed = 0;
    this.name = name;
  }
  // ...
}
class Rabbit extends Animal {
  constructor(name, earLength) {
    this.speed = 0;
    this.name = name;
    this.earLength = earLength;
  }
  // ...
}
// Doesn't work!
let rabbit = new Rabbit("White Rabbit", 10); // Error: this is not defined.
```
This is wrong because constructors in inheriting classes must call super(...), and (!) do it before using this.
In JavaScript, there’s a distinction between a constructor function of an inheriting class (so-called “derived constructor”) and other functions. 
- When a regular function is executed with new, it creates an empty object and assigns it to this.
- But when a derived constructor runs, it doesn’t do this. It expects the parent constructor to do this job.
- So a derived constructor must call super in order to execute its parent (base) constructor, otherwise the object for this won’t be created. And we’ll get an error.

  
Correct Code:
 ```js
class Animal {
  constructor(name) {
    this.speed = 0;
    this.name = name;
  }
  // ...
}

class Rabbit extends Animal {
  constructor(name, earLength) {
    super(name);
    this.earLength = earLength;
  }
  // ...
}
// now fine
let rabbit = new Rabbit("White Rabbit", 10);
alert(rabbit.name); // White Rabbit
alert(rabbit.earLength); // 10
```
We can override not only methods, but not class fields.
```js
class Animal {
  name = 'animal';
  constructor() {
    alert(this.name); // (*)
  }
}

class Rabbit extends Animal {
  name = 'rabbit';
}
new Animal(); // animal
new Rabbit(); // animal
```
Here, class Rabbit extends Animal and overrides the name field with its own value.
In other words, the parent constructor always uses its own field value, not the overridden one.

And that’s what we naturally expect. When the parent constructor is called in the derived class, it uses the overridden method. But for class fields it’s not so. As said, the parent constructor always uses the parent field.

Why is there a difference?
Well, the reason is the field initialization order. The class field is initialized:

Before constructor for the base class (that doesn’t extend anything),
Immediately after super() for the derived class.
In our case, Rabbit is the derived class. There’s no constructor() in it. As said previously, that’s the same as if there was an empty constructor with only super(...args).

So, new Rabbit() calls super(), thus executing the parent constructor, and (per the rule for derived classes) only after that its class fields are initialized. At the time of the parent constructor execution, there are no Rabbit class fields yet, that’s why Animal fields are used.

### Summary
- To extend a class: class Child extends Parent:
That means Child.prototype.__proto__ will be Parent.prototype, so methods are inherited.
- When overriding a constructor:
We must call parent constructor as super() in Child constructor before using this.
- When overriding another method:
We can use super.method() in a Child method to call Parent method.

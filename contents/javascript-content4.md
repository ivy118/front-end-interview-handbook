## Arrow Functions Revisited

Not having this naturally means another limitation: arrow functions can’t be used as constructors. They can’t be called with new.

There’s a subtle difference between an arrow function => and a regular function called with .bind(this):

.bind(this) creates a “bound version” of the function.
The arrow => doesn’t create any binding. The function simply doesn’t have this. The lookup of this is made exactly the same way as a regular variable search: in the outer lexical environment.

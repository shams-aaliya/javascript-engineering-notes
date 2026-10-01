# `this` in JavaScript
What is `this`?

`this` is a reference whose value is determined by how a function is called.

For regular functions, the call site determines `this`.

A useful mental model:

`this` is a dynamic reference to the object associated with the current function call.

This is different from lexical scope.

## `this` in a method call

```js
const person = {
  name: "Shams",

  greet: function () {
    console.log(this.name);
  }
};

person.greet(); // Shams
```

Here:

```text
person.greet()
      ↓
this → person
      ↓
this.name → person.name
      ↓
"Shams"
```
`this.name` is property lookup, not variable lookup.

## `this` is not lexical scope
Lexical scope answers:
```text
Where was this code written, and which variables can it access?
```
`this` answers a different question:
```text
What does this refer to for this particular function call?
```
For regular functions, moving the same function to another call site can change its `this`.

```js
const person = {
  name: "Shams",

  greet: function () {
    console.log(this.name);
  }
};

person.greet(); // Shams

const greetFunction = person.greet;

greetFunction();
```
`greetFunction` contains the same function value, but the `person` relationship is no longer part of the call.

```text
person.greet()
      ↓
this → person

greetFunction()
      ↓
standalone call
      ↓
different this
```
In a non-strict browser script, a standalone regular-function call gets the global object as `this`.

In strict mode, it gets `undefined`.

## Strict mode

```js
"use strict";

function greet() {
  console.log(this);
}

greet(); // undefined
```
Strict mode does not remove `this`.

It changes what happens in certain situations, including standalone regular-function calls.

```text
Non-strict:
greet()
  ↓
this → global object

Strict:
greet()
  ↓
this → undefined
```

But a method call still works normally:
```js
"use strict";

const person = {
  name: "Shams",

  greet() {
    console.log(this.name);
  }
};

person.greet(); // Shams
```

## Arrow functions and `this`
Arrow functions are different.
```text
Arrow functions do not have their own `this`.
```
They use the `this` from their surrounding lexical context.
```js
const person = {
  name: "Shams",

  greet: function () {
    const inner = () => {
      console.log(this.name);
    };

    inner();
  }
};

person.greet(); // Shams
```

Here:
```text
person.greet()
      ↓
greet's this → person
      ↓
inner is an arrow function
      ↓
arrow has no own this
      ↓
uses surrounding this
      ↓
person
```

Compare that with a regular inner function:

```js
const person = {
  name: "Shams",

  greet: function () {
    const inner = function () {
      console.log(this.name);
    };

    inner();
  }
};
```
`inner()` is a standalone regular-function call, so it determines its own `this`.

This is the important distinction:

```text
Regular function
→ own/dynamic this
→ determined by how it is called

Arrow function
→ no own this
→ uses surrounding this
```

### `call()`

`call()` lets us explicitly choose the `this` value and immediately invoke the function.

```js
const greet = function () {
  console.log(this.name);
};

const person = {
  name: "Shams"
};

greet.call(person); // Shams
```

Mental model:
```text
Call this function now, using this object as this.
```
Arguments are supplied individually:
```js
greet.call(person, arg1, arg2);
```

### `apply()`

`apply()` does essentially the same thing as `call()`.

The difference is how arguments are supplied.

```js
greet.call(person, "Hello", "Developer");

greet.apply(person, ["Hello", "Developer"]);
```

Mental model:

```text
call()
→ this, arg1, arg2, arg3

apply()
→ this, [arg1, arg2, arg3]
```

Both invoke the function immediately.

### `bind()`

`bind()` is different.

It doesn't immediately call the function.

Instead, it creates a new function whose `this` is bound to the supplied value.
```js
const introduce = function (greeting) {
  console.log(greeting + ", " + this.name);
};

const person = {
  name: "Shams"
};

const boundIntroduce = introduce.bind(person);

boundIntroduce("Hello");
boundIntroduce("Good morning");
```

Both calls use:

```text
this → person
```

Mental model:

```text
Give me a new function that remembers this object as this.
```

So:

```text
call()
→ set this + invoke now

apply()
→ set this + invoke now

bind()
→ set this + return a new function
→ invoke later
```

## Callbacks and "losing" `this`

Passing a method as a callback can separate the function from the object it came from.

```js
const person = {
  name: "Shams",

  greet() {
    console.log("Hello, " + this.name);
  }
};

setTimeout(person.greet, 1000);
```

We're passing the function itself:

`person.greet`

rather than calling:

`person.greet()`

When the callback is eventually invoked, it is no longer being called through `person`.

We can preserve the intended `this`  with:
```js
setTimeout(person.greet.bind(person), 1000);
```

## `this` in older React class components

This connects directly to the older React code we used to encounter.

```js
class Counter extends React.Component {
  handleClick() {
    console.log(this);
  }

  render() {
    return <button onClick={this.handleClick}>Click</button>;
  }
}
```
`handleClick` is being passed as a callback.

Older React class components commonly used:

```js
this.handleClick = this.handleClick.bind(this);
```

to ensure that the method's `this` remained associated with the component instance.

Another common pattern was an arrow class field:

```js
handleClick = () => {
  console.log(this);
};
```
Because the arrow function doesn't create its own `this`, it captures the surrounding `this`.

This is specifically a class-component pattern. Modern React function components and hooks generally don't use `this`.

## Common Misconceptions

❌ "`this` means the object where the function was created."

Not for regular functions.

❌ "`this` is the same as lexical scope."

No.

❌ "`this` always refers to the object before the dot."

That's a useful starting model for method calls, but not a universal rule.

❌ "Arrow functions are just modern regular functions."

No. Their this behavior is fundamentally different.

❌ "`bind()` changes the original function."

No.
```js
const bound = greet.bind(person);
```
creates a new function. The original `greet` remains unchanged.

## Final mental model

The simplest version I want to remember is:

```text
REGULAR FUNCTION

How was I called?
        ↓
That determines my this.
```
```text
ARROW FUNCTION

I don't have my own this.
        ↓
I use the surrounding this.
```
And:
```text
call()
→ explicitly choose this and run now

apply()
→ explicitly choose this + arguments array and run now

bind()
→ explicitly choose this and create a new function for later
```
Interview version

If someone asks "What is `this` in JavaScript?", your natural answer can eventually be:
```text
"this is a context reference whose value depends on how a function is invoked. For regular functions, the call site determines this; arrow functions don't have their own this and instead capture it lexically from their surrounding scope."
```
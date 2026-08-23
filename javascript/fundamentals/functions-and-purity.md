# Functions, Purity and Side Effects

## Pure Functions

A pure function has two important properties:

1. Given the same inputs, it always produces the same output.
2. It does not produce observable side effects.

```js
function add(a, b) {
    return a + b;
}
```
For the same inputs:
```js
add(2, 3); // 5
add(2, 3); // 5
```
The function does not change anything outside itself.

External State
A function can access a value outside its own scope and still be pure if that value is stable.
```js
const taxRate = 0.18;

function calculateTax(price) {
    return price * taxRate;
}
```
However, if the external value can change, the function's output can change even when its input stays the same.
```js
let taxRate = 0.18;

function calculateTax(price) {
    return price * taxRate;
}

calculateTax(100); // 18

taxRate = 0.20;

calculateTax(100); // 20
```

The same input produced different outputs because the function depended on mutable external state.

Therefore, a better mental model is:

A pure function should behave as if its output is determined entirely by its inputs and should not produce observable side effects.

## Side Effects

A side effect is an observable effect produced by a function outside its own local calculation.

Mutating External Data
```js
const numbers = [1, 2, 3];

function addNumber() {
    numbers.push(4);
}
```
The function changes an array that exists outside itself.

This is a side effect.
```js
function greet() {
    console.log("Hello");
}
```
The function affects the console rather than simply producing a return value.
`console.log()` is therefore a side effect.

### DOM Changes

```js
function changeHeading() {
    document.querySelector("h1").textContent = "Hello";
}
```
The function changes the webpage, which is a side effect.

### Local Mutation

Mutation inside a function is not necessarily an external side effect.
```js
function calculate() {
    const numbers = [10, 20, 30];

    numbers.push(40);

    return numbers.length;
}
```

The array was created inside the function and is not observable outside it, so the local mutation itself is not an external side effect.

### Pure vs Impure
```js
function multiply(a, b) {
    return a * b;
}
```

Pure:
Same inputs produce the same output.
Nothing outside the function is changed.

```js
let count = 0;

function increment() {
    count++;
    return count;
}
```
Impure:
The result depends on external mutable state.
The function changes that external state.

### Important Distinction

Mutation and side effects are related but not identical.

Mutation means changing existing data.

A side effect means producing an observable effect outside the function's local calculation.

A function can have a side effect without mutating an object, such as logging to the console or making a network request.

### Examples with Array Methods

`map()` does not mutate the original array:
```js
const numbers = [1, 2, 3];

const doubled = numbers.map(n => n * 2);
```
The original array remains unchanged and a new array is returned.

However, the callback passed to `map()` can still produce side effects:
```js
numbers.map(n => {
    console.log(n);
    return n * 2;
});
```
The `map()` operation does not mutate the original array, but the callback produces a console side effect.
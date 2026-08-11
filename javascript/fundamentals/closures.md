# Closures

## What is a closure?

### Textbook definition

JavaScript uses lexical scoping, which means a function resolves variables according to the scope that existed when the function was defined.

A closure is the combination of a function object and a reference to the scope (set of variable bindings) in which that function was defined. This allows the function to resolve variables from its surrounding lexical environment even when it is invoked from a different scope.

### My understanding

A closure is the behaviour of a function where, even after the outer function finishes its execution, the lexical environment containing the bindings required by the inner function remains accessible to it. Technically, all JavaScript functions are closures. We don't usually notice this because most functions are called from the same scope in which they were defined, so the closure doesn't make a noticeable difference.

## Example
```js
function createCounter() {
    let count = 0;

    return function () {
        count++;
        return count;
    };
}

const counter = createCounter();

console.log(counter()); // 1
console.log(counter()); // 2
console.log(counter()); // 3
```

### What is happening?
When the variable `counter` is initialized with the result of calling `createCounter()`, the function runs immediately and returns the inner function. Although the variable `count` is created while `createCounter()` is running, the inner function uses `count`, so the lexical environment containing the `count` binding remains accessible to the inner function through its closure.

`counter` now refers to the returned inner function, which has access to `createCounter()`'s lexical environment. When we call `counter()`, `count++` updates the existing `count` binding in that lexical environment. This happens each time `counter()` is called, so the value becomes `1`, then `2`, then `3`.

## Why are closures useful?

Closures are useful when a function needs to remember and access values from its surrounding lexical environment after the surrounding function has finished executing.

### 1. Maintaining private state

A closure can be used to keep a value private while allowing a returned function to read or modify it.

```js
function createCounter() {
    let count = 0;

    return function () {
        count++;
        return count;
    };
}

const counter = createCounter();

console.log(counter()); // 1
console.log(counter()); // 2
console.log(counter()); // 3
```
The `count` binding cannot be accessed directly from outside `createCounter()`, but the returned function can access and modify it through its closure.
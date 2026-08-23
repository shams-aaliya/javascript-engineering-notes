# Lexical Scope and Execution Context

## Lexical Scope

Lexical scope means that where a variable or function is written in the source code determines which surrounding scopes it can access.

JavaScript determines this based on the structure of the code, not on where a function is called.

```js
let global = "global";

function outer() {
    let outerVariable = "outer";

    function inner() {
        console.log(global);
        console.log(outerVariable);
    }

    inner();
}
```
`inner()` can access `outerVariable` because it is lexically nested inside `outer()`. It can also access `global` through the outer scope chain.

### Lexical Environment

A lexical environment is the structure that holds variable bindings and a reference to its outer lexical environment.

A binding connects a name to a value.

For example:
```js
let a = 10;
const b = 20;

function greet() {
    let message = "Hello";
}

```
The lexical environments contain bindings such as:
```text
a       → 10
b       → 20
greet   → function
```
When `greet()` executes, its own lexical environment contains the bindings created inside it, such as:
```text
message → "Hello"
```
The environment also has a connection to its outer environment, allowing JavaScript to look up variables through the scope chain.

## Execution Context

An execution context is the environment in which JavaScript executes code.

When a function is called, JavaScript creates a new execution context for that function.

The execution context keeps track of things needed while that code is executing, including its variables, parameters, and the current execution state.

Conceptually:
```text
Global Execution Context
        ↓
   calls outer()
        ↓
Outer Execution Context
        ↓
   calls inner()
        ↓
Inner Execution Context
```

When a function finishes executing, its execution context is removed from the call stack.

### Call Stack

The call stack keeps track of which functions are currently executing.

For example:
```js
function first() {
    second();
}

function second() {
    console.log("Hello");
}

first();
```

The stack changes conceptually like this:
```text
1. first() is called

┌─────────────┐
│ first()     │
└─────────────┘


2. first() calls second()

┌─────────────┐
│ second()    │
├─────────────┤
│ first()     │
└─────────────┘


3. second() finishes

┌─────────────┐
│ first()     │
└─────────────┘


4. first() finishes

┌─────────────┐
│             │
└─────────────┘
```
The most recently added execution context is the first one to be removed.

This is called LIFO (Last In, First Out).

Variable Lookup

When JavaScript encounters a variable such as:
```js
console.log(a);
```
it looks for the binding for `a` in the current lexical environment.

If it is not found, JavaScript looks in the outer lexical environment and continues outward through the scope chain.
```text
Current Lexical Environment
        ↓
Outer Lexical Environment
        ↓
Global Lexical Environment
```
This is why a nested function can access variables from its surrounding scopes.
Important Distinction

The lexical environment and the execution context are related but are not the same thing.

The lexical environment is concerned with bindings and scope.

The execution context represents the broader state in which code is currently being executed.

The call stack keeps track of the currently active execution contexts.
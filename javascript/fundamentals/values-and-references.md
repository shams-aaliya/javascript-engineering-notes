# Values and References

## Primitive values

With primitive values, a variable's binding contains the value itself. When we create another variable using the first variable, the value is copied into the new binding.

```js
let a = 10;
let b = a;

b = 20;

console.log(a); // 10
console.log(b); // 20
```

Changing b does not affect a because the two bindings contain separate primitive values.

## Object values

With object values, a variable's binding contains a reference to the object. When we create another variable using the first variable, the reference is copied.
```js
let a = { value: 10 };
let b = a;

b.value = 20;

console.log(a.value); // 20
console.log(b.value); // 20
```

Both variables refer to the same object, so mutating the object through `b` is also observable through `a`.

## Mutation vs. Reassignment

Mutation changes the existing object, while reassignment changes what a variable's binding refers to.

```js
let a = { value: 10 };
let b = a;

b.value = 20; // Mutation

console.log(a.value); // 20
console.log(b.value); // 20
```
Here, the object itself was mutated, so both `a` and `b` observe the change because they still refer to the same object.

```js
let a = { value: 10 };
let b = a;

b = { value: 20 }; // Reassignment

console.log(a.value); // 10
console.log(b.value); // 20
```
Here, `b` was reassigned to a new object. The original object was not changed, so a still refers to the original object.

```text
Mutation
b.value = 20
       ↓
same object changes


Reassignment
b = { value: 20 }
       ↓
b now refers to a different object
```

## `const` with objects

`const` prevents reassignment of the binding, but it does not make the object itself immutable.

```js
const user = {
    name: "Ali"
};

user.name = "Sara"; // Allowed: mutating the object

console.log(user.name); // Sara

user = { name: "Sara" }; // TypeError: reassignment is not allowed
```

In the first case, we mutate a property of the existing object. The `user` binding still refers to the same object.

In the second case, we try to reassign `user` so that it refers to a different object. Since `user` was declared with `const`, reassignment is not allowed.

### Diagram understanding for const

```text
const user
     │
     │ binding cannot change
     ▼
  ┌─────────────┐
  │ name: "Ali" │
  └─────────────┘
```
const user ─────X────> new object ---------------- Not Allowed

const user
     │
     ▼
  ┌─────────────┐
  │ name: "Sara"│  ← contents changed  ----------- Allowed
  └─────────────┘

## Shallow copying

A shallow copy creates a new outer object or array, but nested objects or arrays inside it still share their references with the original.

For example:

```js
const a = {
    name: "Ali",
    address: {
        city: "Mumbai"
    }
};

const b = { ...a };

b.name = "Sara";
b.address.city = "Delhi";

console.log(a.name); // Ali
console.log(b.name); // Sara

console.log(a.address.city); // Delhi
console.log(b.address.city); // Delhi
```

The outer objects are different, but the nested address object is the same object.

### Diagram understanding for shallow copying in objects

a ────────► { name: "Ali", address ──────┐ }
                                         │
b ────────► { name: "Sara", address ─────┘ }
                                         ▼
                                  { city: "Delhi" }

The spread operator copied the top-level properties, but for the nested `address` property it copied the reference rather than creating a new nested object.

### Array.from()
```js
const a = ['a', 'b', 'c'];

const b = Array.from(a);
```

`b` is a new array, so:
```js 
a === b; // false
```

But Array.from() doesn't magically deep-copy objects inside an array.

For example:
```js
const a = [{ name: "Ali" }];

const b = Array.from(a);

b[0].name = "Sara";

console.log(a[0].name); // Sara
```

Why?

### Diagram understanding for array 

a ──────► [ ──► { name: "Ali" } ]
                ▲
b ──────► [ ────┘]

The arrays are different, but the object inside them is shared.

## Equality and references

When comparing primitive values with `===`, JavaScript compares their values.

When comparing objects with `===`, JavaScript checks whether both operands refer to the same object.

```js
let a = 10;
let b = 10;

console.log(a === b); // true
```
Both primitive values are equal.

With objects:
```js
const a = { value: 10 };
const b = { value: 10 };
const c = a;

console.log(a === b); // false
console.log(a === c); // true
```
`a` and `b` contain objects with the same properties and values, but they are two different objects.
`c`, however, contains the same reference as `a`, so `a === c` is true.

### Diagram Representation of equality and refrences
a ──────┐
        ▼
     Object A
        ▲
        │
c ──────┘

b ──────► Object B

Therefore:
```js
a === c; // true
a === b; // false
```

`===` does not compare the contents of two separate objects. It compares whether the references point to the same object.
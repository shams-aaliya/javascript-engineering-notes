# Array Methods

JavaScript provides many methods for working with arrays. They can be understood more easily by grouping them according to the problem they solve.

## 1. Iterate / Perform an Action

### `forEach()`

`forEach()` is used when we want to perform an action for each element in an array.

```js
const numbers = [1, 2, 3];

numbers.forEach((number) => {
	console.log(number);
});
```

The callback is invoked once for each applicable element.

`forEach()` does not create a new array and returns `undefined`.

The purpose of `forEach()` is generally to perform an action rather than produce a new collection.

```text
array
  ↓
forEach()
  ↓
action on each element
```

For example:

```js
const users = ["Ali", "Sara", "John"];

users.forEach((user) => {
	console.log(`Hello ${user}`);
});
```

The action could also produce a side effect:

```js
users.forEach((user) => {
	saveUser(user);
});
```

`forEach()` itself does not mutate the original array, although the callback can cause side effects or mutate objects contained in the array.

## 2. Transform

### `map()`

`map()` is used to transform every element of an array and returns a new array containing the callback's return values.

```js
const numbers = [1, 2, 3];

const doubled = numbers.map((number) => number * 2);

console.log(doubled); // [2, 4, 6]
console.log(numbers); // [1, 2, 3]
```

The callback is invoked for each element.

The callback's return value becomes an element in the new array.

Conceptually:

```text
[1, 2, 3]
    ↓
map(callback)
    ↓
callback(1) → 2
callback(2) → 4
callback(3) → 6
    ↓
[2, 4, 6]
```

`map()` does not mutate the original array by itself.

## 3. Select

### `filter()`

`filter()` is used to select elements that satisfy a condition and returns a new array containing those elements.

```js
const numbers = [1, 2, 3, 4, 5, 6];

const evenNumbers = numbers.filter((number) => number % 2 === 0);

console.log(evenNumbers); // [2, 4, 6]
```

The callback's return value is treated as a condition:

```text
true  → keep the element
false → discard the element
```

For example:

```js
const users = [
	{ name: "Ali", age: 30 },
	{ name: "Sara", age: 17 },
	{ name: "John", age: 25 },
];

const adults = users.filter((user) => user.age >= 18);
```

The result contains the first and third objects.
`filter()` does not mutate the original array.

## 4. Accumulate

### `reduce()`

`reduce()` progressively processes an array and reduces its elements into one accumulated result.

The accumulator is the value carried from one iteration to the next.

```js
const numbers = [1, 2, 3, 4];

const total = numbers.reduce((accumulator, number) => {
	return accumulator + number;
}, 0);
```

The `0` is the initial value of the accumulator.
Conceptually:

```text
Initial accumulator = 0

0 + 1 = 1
1 + 2 = 3
3 + 3 = 6
6 + 4 = 10

Final result = 10
```

The callback's return value becomes the accumulator for the next iteration.
The accumulator does not have to be a number. It can be an object, array, string, or another value depending on what we are building.

Object as an accumulator

```js
const fruits = ["apple", "banana", "apple", "orange", "banana", "apple"];

const counts = fruits.reduce(
	(acc, fruit) => {
		if (fruit === "apple") {
			acc.apple++;
		} else if (fruit === "banana") {
			acc.banana++;
		} else {
			acc.orange++;
		}

		return acc;
	},
	{
		apple: 0,
		banana: 0,
		orange: 0,
	},
);
```

The result is:

```js
{
    apple: 3,
    banana: 2,
    orange: 1
}
```

Here the accumulator is an object that is progressively built.

Array as an accumulator

```js
const products = [
	{ name: "Laptop", price: 50000 },
	{ name: "Mouse", price: 1500 },
	{ name: "Keyboard", price: 3000 },
];

const expensiveProducts = products.reduce((acc, product) => {
	if (product.price > 2000) {
		acc.push(product.name);
	}

	return acc;
}, []);
```

The result is:
`["Laptop", "Keyboard"]`
Here the accumulator is an array.

Choosing the Appropriate Method
The method should communicate the intention of the code.

```text
forEach()
→ Iterate through the elements and perform an action.

map()
→ Transform every element into a new array.

filter()
→ Select elements that satisfy a condition.

reduce()
→ Accumulate elements into one result.
```

For example, if we want the names of products costing more than 2000:

```js
const expensiveProductNames = products
	.filter((product) => product.price > 2000)
	.map((product) => product.name);
```

This communicates the intention clearly:

```text
filter → select the products
map    → get their names
```

Although `reduce()` can perform both operations, it should not automatically be used simply because it is capable of doing so.
Use the method that most clearly expresses what the code is trying to accomplish.

## 5. Find / Check

### `find()`

`find()` is used when we want to find the **first element** in an array that satisfies a condition.

```js
const numbers = [4, 7, 12, 15, 20];

const result = numbers.find(number => number > 10);

console.log(result); // 12
```
`find()` checks the elements from left to right and stops as soon as the callback returns true.

```text
4  → false
7  → false
12 → true → STOP
```

It returns the element itself, not an array.

If no element satisfies the condition, it returns undefined.

```js
const result = numbers.find(number => number > 100);

console.log(result); // undefined
```
```js
find() vs filter()
```

The key difference is what we are asking for:

`find()`
→ "Give me the FIRST element that matches."

`filter()`
→ "Give me ALL elements that match."

For example:

```js
const numbers = [4, 7, 12, 15, 20];

numbers.find(number => number > 10);
// 12

numbers.filter(number => number > 10);
// [12, 15, 20]
```
`find()` is useful when we are looking for one particular item, such as finding a user by their ID:
```js
const user = users.find(user => user.id === 2);
```

Here, we don't need an array of matching users. We want the user itself.

Because `find()` stops after finding the first match, it can avoid traversing the rest of the array when the match occurs early.

### `findIndex()`

`findIndex()` is used when we want to find the index of the first element that satisfies a condition.
```js
const numbers = [4, 7, 12, 15, 20];

const result = numbers.findIndex((number) => number > 10);

console.log(result); // 2
```
The array is:
```text
Value:  4   7   12   15   20
Index:  0   1    2    3    4
```
`findIndex()` checks the elements from left to right and stops when the callback returns true.
```text
4  → false
7  → false
12 → true → STOP
```
It returns the index, not the element itself.

If no element satisfies the condition, it returns `-1`.
```js
const result = numbers.findIndex((number) => number > 100);

console.log(result); // -1
```
The mental model:
```text
find()
→ "Give me the FIRST element that matches."

findIndex()
→ "Give me the POSITION of the FIRST element that matches."
```
A practical use case is when we need to locate an item in an array so that we can use its position to modify or remove it.

### `some()`

`some()` is used when we want to check whether at least one element in an array satisfies a condition.
```js
const numbers = [4, 7, 12, 15, 20];

const result = numbers.some((number) => number > 18);

console.log(result); // true
```
It checks the elements until the callback returns true.
```text
4  → false
7  → false
12 → false
15 → false
20 → true → STOP
```
It returns a boolean:
```text
true  → at least one element matches
false → no elements match
```
The mental model:

"I don't care which one. Just tell me if there is at least one."

For example, we might use it to check whether a list of users contains at least one active user:
```js
const hasActiveUser = users.some((user) => user.active);
```

### `every()`

`every()` is used when we want to check whether all elements in an array satisfy a condition.
```js
const numbers = [4, 7, 12, 15, 20];

const result = numbers.every((number) => number > 3);

console.log(result); // true
```
It returns false as soon as one element fails the condition.
```text
4  → true
7  → true
12 → true
15 → true
20 → true
```
The mental model:

"Do ALL the elements satisfy this condition?"

So `some()` and `every()` form a useful pair:
```text
some()
→ "Does AT LEAST ONE match?"

every()
→ "Do ALL match?"
```
Both return a boolean.

## 6. Check for a value
### `includes()`

`includes()` is used when we want to check whether an array contains a specific value.

Unlike `some()`, it does not require a callback.
```js
const fruits = ["apple", "banana", "mango", "orange"];

const result = fruits.includes("mango");

console.log(result); // true
```
If the value does not exist:
```js
fruits.includes("grapes"); // false
```
It returns a boolean:
```text
true  → value exists
false → value does not exist
```
The mental model:
```text
"Does this specific value exist in the array?"
```
For example:
```js
const allowedRoles = ["admin", "editor", "manager"];

if (allowedRoles.includes(userRole)) {
    // allow access
}
```
`includes()` is useful for simple membership checks, while `some()` is useful when the condition needs to be evaluated against each element.

## 7. Sort/Arrange
### `sort()`

`sort()` is used to arrange elements according to an ordering rule.

By default, JavaScript sorts values as strings rather than performing numerical sorting.
```js
const numbers = [10, 2, 30, 4];

numbers.sort();

console.log(numbers); // [10, 2, 30, 4]
```
This happens because the values are compared as strings:
```text
"10"
"2"
"30"
"4"
```
So we should not assume that `sort()` automatically means numerical ascending order.

For numerical ascending order, we provide a comparison function:
```js
numbers.sort((a, b) => a - b);
```
The comparison function tells `sort()` how two elements should be ordered.
```text
negative → a should come before b
positive → b should come before a
zero     → no ordering difference
```
For example:
```js
const numbers = [10, 2, 30, 4];

numbers.sort((a, b) => a - b);

console.log(numbers); // [2, 4, 10, 30]
```
For descending order:
```js
numbers.sort((a, b) => b - a);
result:
[30, 10, 4, 2]
```
Important: `sort()` mutates the original array

Unlike `map()` and `filter()`, which return new arrays without mutating the original array themselves, `sort()` changes the original array.

So:
```js
const numbers = [10, 2, 30, 4];

numbers.sort((a, b) => a - b);

console.log(numbers); // [2, 4, 10, 30]
```
numbers itself is now sorted.

The mental model:
```text
"Arrange the elements according to this ordering rule."
```

```text 
forEach()
→ Iterate through the elements and perform an action.

map()
→ Transform every element into a new array.

filter()
→ Select ALL elements that satisfy a condition.

reduce()
→ Accumulate elements into one result.

find()
→ Get the FIRST element that satisfies a condition.

findIndex()
→ Get the POSITION of the FIRST element that satisfies a condition.

some()
→ Check if AT LEAST ONE element satisfies a condition.

every()
→ Check if ALL elements satisfy a condition.

includes()
→ Check if a SPECIFIC VALUE exists in the array.

sort()
→ Arrange elements according to an ordering rule.
```
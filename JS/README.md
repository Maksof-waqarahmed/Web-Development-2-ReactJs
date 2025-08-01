# JavaScript Core Concepts for Beginners

---

## 📌 1. Data Types in JavaScript

### 🟩 Primitive Data Types

These store actual values in the **Stack**:

* **String** – text (`"hello"`)
* **Number** – numbers (`10`, `5.6`)
* **Boolean** – `true` or `false`
* **Undefined** – variable declared but not assigned
* **Null** – empty value
* **Symbol** – unique values
* **BigInt** – large integers

```js
const name = "Ali";
const age = 20;
const isStudent = true;
let address; // undefined
const score = null;
```

### 🟦 Non-Primitive Data Types

Stored in the **Heap**, and referenced from the Stack:

* **Object**
* **Array**
* **Function**

```js
const person = { name: "Ali", age: 20 };
const fruits = ["apple", "banana"];
function greet() { console.log("Hello") }
```

## 🧠 Stack vs Heap

* **Stack** stores **primitive values** (fixed, fast memory)
* **Heap** stores **reference values** (dynamic, flexible)

### ✅ Advantage of Heap:

* Can store complex and large data.

### ❌ Disadvantage:

* Slower than stack.
* Needs more memory management.

---

## 🔐 const with Primitive vs Non-Primitive

```js
const num = 5;
num = 6; // ❌ Error: can't reassign primitive constant

const arr = [1, 2, 3];
arr.push(4); // ✅ allowed
arr[0] = 10; // ✅ allowed
```

* `const` with **primitive** = cannot reassign.
* `const` with **non-primitive** = reference is fixed, but values can be modified.

---

## 📦 Objects, Arrays, and Functions

### 🔸 Object

```js
const user = {
  name: "Ali",
  age: 25,
};

// Add
user.email = "ali@example.com";

// Update
user.age = 26;

// Delete
delete user.name;
```

### 🔹 Array

```js
const colors = ["red", "blue"];

// Add
colors.push("green");

// Update
colors[0] = "yellow";

// Remove
colors.pop(); // removes last element
```

### 🔻 Function

```js
function add(a, b) {
  return a + b;
}

const sum = add(5, 3); // 8
```

---

## 🧾 Object & Array Methods

### Object Methods:

* `Object.keys(obj)`
* `Object.values(obj)`
* `Object.entries(obj)`

```js
const car = { brand: "Toyota", year: 2020 };
console.log(Object.keys(car));   // ["brand", "year"]
```

### Array Methods:

* `push()`, `pop()`, `shift()`, `unshift()`
* `slice()`, `splice()`
* `map()`, `filter()`, `reduce()`

```js
const nums = [1, 2, 3, 4];
const doubled = nums.map(n => n * 2);
```

---

## 🔁 Loops

### for loop

```js
for (let i = 0; i < 5; i++) {
  console.log(i);
}
```

### for...of (for arrays)

```js
for (let fruit of ["apple", "banana"]) {
  console.log(fruit);
}
```

### for...in (for objects)

```js
const obj = { a: 1, b: 2 };
for (let key in obj) {
  console.log(key, obj[key]);
}
```

### while loop

```js
let i = 0;
while (i < 3) {
  console.log(i);
  i++;
}
```

### Higher-Order Functions

* A function that takes another function as an argument or returns a function

```js
function greet(name) {
  return `Hello ${name}`;
}

function processUser(userName, callback) {
  console.log(callback(userName));
}

processUser("Ali", greet); // Hello Ali
```

---

## 🧩 Parameters vs Arguments

```js
function sayHi(name) { // name is a parameter
  console.log("Hi", name);
}

sayHi("Ali"); // "Ali" is an argument
```

### Difference:

* **Parameter** is the placeholder in function definition.
* **Argument** is the real value passed when calling the function.

---

## 🔄 Callback Functions

A function passed to another function to be executed later.

```js
function greet(name, callback) {
  console.log("Hello", name);
  callback();
}

function sayBye() {
  console.log("Goodbye!");
}

greet("Ali", sayBye);
```

---

## 🧠 Summary

| Concept                | Key Point                                           |
| ---------------------- | --------------------------------------------------- |
| Data Types             | Primitive (value) vs Non-Primitive (reference)      |
| const behavior         | Cannot reassign primitive; Can modify non-primitive |
| Object/Array           | Add, update, delete using dot or index notation     |
| Loops                  | for, for...of, for...in, while                      |
| Higher Order Functions | Accept other functions as parameters                |
| Callback               | Function passed to another function                 |

---

This file is meant to help you **explain step-by-step** to students with real-world relatable examples and easy words.

> 💡 Teach with visuals and live coding for best results!

---

Happy Coding! 👨‍💻

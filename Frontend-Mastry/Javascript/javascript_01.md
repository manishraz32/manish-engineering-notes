# Execution Context

## What is an Execution Context?

An **Execution Context** is the environment in which JavaScript code is executed. It contains everything required to run the code, including variables, functions, and the value of `this`.

JavaScript executes code inside execution contexts rather than running code directly.

---

## Types of Execution Context

### 1. Global Execution Context (GEC)

The Global Execution Context is created when a JavaScript program starts.

There is only **one Global Execution Context** in a program.

It:

* Creates the global object (`window` in browsers, `global` in Node.js).
* Sets the value of `this`.
* Allocates memory for global variables and functions.
* Executes global code line by line.

```javascript
var name = "Manish";

function greet() {
    console.log("Hello");
}

greet();
```

Before execution begins, memory is allocated for `name` and `greet`.

---

### 2. Function Execution Context (FEC)

Whenever a function is called, JavaScript creates a new Function Execution Context.

Each function invocation gets its own:

* Variables
* Parameters
* `this` value
* Scope chain

```javascript
function add(a, b) {
    return a + b;
}

add(10, 20);
```

Calling `add()` creates a new execution context that exists until the function finishes executing.

---

### 3. Eval Execution Context

The `eval()` function creates its own execution context.

```javascript
eval("console.log('Hello')");
```

This is rarely used in modern applications because of performance and security concerns.

---

# Phases of Execution Context

Every execution context goes through two phases:

## 1. Memory Creation Phase (Creation Phase)

During this phase, JavaScript scans the code and allocates memory.

Rules:

* Variables declared with `var` are initialized with `undefined`.
* Function declarations are stored entirely in memory.
* `let` and `const` remain in the **Temporal Dead Zone (TDZ)** until execution reaches their declaration.

Example:

```javascript
console.log(a); // undefined
console.log(sum); // Function reference

var a = 10;

function sum() {
    return 5 + 5;
}
```

Memory representation:

```text
a     -> undefined
sum   -> function sum() {...}
```

---

## 2. Execution Phase

In this phase, JavaScript executes the code line by line.

Values are assigned to variables, functions are invoked, and expressions are evaluated.

Example:

```javascript
var a = 10;

function greet() {
    console.log("Hello");
}

greet();
```

Execution steps:

1. Assign `10` to `a`.
2. Define `greet`.
3. Call `greet()`.
4. Print `"Hello"`.

---

# Execution Context Stack (Call Stack)

JavaScript uses a **Call Stack** to manage execution contexts.

The stack follows the **Last In, First Out (LIFO)** principle.

Example:

```javascript
function one() {
    two();
}

function two() {
    three();
}

function three() {
    console.log("Inside three");
}

one();
```

Execution flow:

```text
Global Execution Context
        ↓
Function one()
        ↓
Function two()
        ↓
Function three()
```

Call Stack:

```text
| three() |
| two()   |
| one()   |
| Global  |
```

After `three()` completes:

```text
| two()   |
| one()   |
| Global  |
```

Eventually, only the Global Execution Context remains.

---

# Visual Representation

```text
┌─────────────────────────┐
│ Global Execution Context│
└───────────┬─────────────┘
            │
            ▼
┌─────────────────────────┐
│ Function one() Context  │
└───────────┬─────────────┘
            │
            ▼
┌─────────────────────────┐
│ Function two() Context  │
└───────────┬─────────────┘
            │
            ▼
┌─────────────────────────┐
│ Function three() Context│
└─────────────────────────┘
```

---

# Key Points

* JavaScript runs code inside execution contexts.
* There is only one Global Execution Context.
* Every function call creates a new Function Execution Context.
* Each context has a Creation Phase and an Execution Phase.
* Function declarations are fully hoisted.
* Variables declared with `var` are initialized with `undefined`.
* `let` and `const` exist in the Temporal Dead Zone until initialized.
* Execution contexts are managed using the Call Stack.

---

# Interview Questions

### What is an Execution Context?

An execution context is the environment in which JavaScript code executes, containing variables, functions, scope information, and the `this` value.

### How many phases does an execution context have?

Two phases:

1. Memory Creation Phase
2. Execution Phase

### What is the difference between `var` and function hoisting?

* `var` is hoisted and initialized with `undefined`.
* Function declarations are hoisted with their complete implementation.

### What data structure manages execution contexts?

The **Call Stack**, which follows the **LIFO (Last In, First Out)** principle.

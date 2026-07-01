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


# Lexical Environment

## Intuition

Think of a lexical environment as a **box that stores variables and functions**.

Every time JavaScript creates an execution context, it also creates a new lexical environment.

Each box knows two things:

1. What variables and functions it owns.
2. Where its parent box is.

Because of this parent reference, JavaScript can search for variables outside the current function.

---

## Internal Structure

A lexical environment consists of two parts:

```text
Lexical Environment
│
├── Environment Record
│   ├── name = "Manish"
│   ├── age = 30
│   └── greet = function
│
└── Outer Environment Reference
    └── Global Environment
```

### Environment Record

Stores:

* Variables
* Function declarations
* Function parameters

### Outer Environment Reference

Points to the parent scope.

This allows JavaScript to access variables from outer functions.

---

## Example

```javascript
const country = "India";

function person() {
    const name = "Manish";

    function greet() {
        console.log(name);
        console.log(country);
    }

    greet();
}

person();
```

---

## How JavaScript Searches for Variables

When `greet()` executes:

### Step 1: Search inside `greet`

```text
greet()
```

```javascript
console.log(name);
```

`name` does not exist here.

---

### Step 2: Move to the parent environment

```text
person()
```

```javascript
const name = "Manish";
```

Found!

---

### Step 3: Search for `country`

```javascript
console.log(country);
```

Not found in `greet()`.

Not found in `person()`.

Move to:

```text
Global Environment
```

```javascript
const country = "India";
```

Found!

---

## Visual Representation

```text
Global Environment
│
├── country = "India"
│
└── person()
    │
    ├── name = "Manish"
    │
    └── greet()
```

Every environment knows who its parent is.

---

## Important Point

JavaScript uses **where functions are written**, not where they are called.

This is called **lexical scoping**.

The word *lexical* means:

> Based on the physical structure of the code.

---

## Example

```javascript
const x = 10;

function printX() {
    console.log(x);
}

function test() {
    const x = 20;

    printX();
}

test();
```

Output:

```text
10
```

Many beginners expect:

```text
20
```

But `printX()` was created in the global scope.

Therefore, it uses the global `x`.

---

## Relationship with Closures

Closures exist because functions remember their lexical environment.

```javascript
function counter() {
    let count = 0;

    return function () {
        count++;
        console.log(count);
    };
}

const increment = counter();

increment(); // 1
increment(); // 2
```

Even after `counter()` finishes, the inner function still remembers:

```javascript
let count = 0;
```

That memory comes from the lexical environment.

---

## Interview Questions

### What is a lexical environment?

A lexical environment is an internal JavaScript structure that stores variables, functions, and a reference to its parent environment.

---

### When is a lexical environment created?

Whenever an execution context is created.

---

### Why is it called lexical?

Because variable lookup depends on where code is written (its lexical structure), not where functions are called.

---

### How are closures related to lexical environments?

Closures work because functions maintain a reference to the lexical environment in which they were created.

---

## Key Takeaways

* Every execution context creates a lexical environment.
* A lexical environment stores variables and functions.
* It also stores a reference to its parent environment.
* Variable lookup happens through these parent references.
* Closures depend on lexical environments.
* JavaScript uses lexical (static) scoping.

# Scope Chain

## What is a Scope Chain?

A **Scope Chain** is the mechanism JavaScript uses to find variables.

If a variable is not found in the current scope, JavaScript searches in the parent scope, continuing until it reaches the global scope.

---

## Example

```javascript
const a = 10;

function outer() {
    const b = 20;

    function inner() {
        console.log(a, b);
    }

    inner();
}

outer();
```

### Variable Lookup

```text
inner() → outer() → Global
```

JavaScript checks each scope one by one until it finds the variable.

---

## Lexical Scoping

JavaScript uses **lexical (static) scoping**.

Variables are resolved based on **where a function is defined**, not where it is called.

```javascript
const name = "Global";

function printName() {
    console.log(name);
}

function test() {
    const name = "Local";
    printName();
}

test(); // Global
```

---

## Interview Points

* Scope chain is formed using lexical environments.
* Variable lookup always starts from the current scope.
* JavaScript follows lexical (static) scoping.
* The search stops once the variable is found.
* If the variable is not found anywhere, a `ReferenceError` is thrown.

# Hoisting

## What is Hoisting?

Hoisting is JavaScript's behavior of processing declarations before code execution begins.

Only declarations are hoisted, not assignments.

---

## `var` Hoisting

```javascript
console.log(a);

var a = 10;
```

Output:

```text
undefined
```

JavaScript internally treats it as:

```javascript
var a;

console.log(a);

a = 10;
```

---

## Function Hoisting

Function declarations are fully hoisted.

```javascript
greet();

function greet() {
    console.log("Hello");
}
```

Output:

```text
Hello
```

---

## Function Expression

```javascript
sayHi();

var sayHi = function () {
    console.log("Hi");
};
```

Output:

```text
TypeError: sayHi is not a function
```

---

## `let` and `const`

```javascript
console.log(x);

let x = 10;
```

Output:

```text
ReferenceError
```

They are hoisted but remain inside the **Temporal Dead Zone (TDZ)** until initialization.

---

## Interview Points

* `var` is hoisted and initialized with `undefined`.
* Function declarations are completely hoisted.
* Function expressions behave like variables.
* `let` and `const` are hoisted but cannot be accessed before initialization.
* Hoisting happens during the creation phase of the execution context.

# Temporal Dead Zone (TDZ)

## What is TDZ?

The **Temporal Dead Zone (TDZ)** is the time between entering a scope and initializing a `let` or `const` variable.

Accessing the variable during this period throws a `ReferenceError`.

---

## Example

```javascript
console.log(age);

let age = 25;
```

Output:

```text
ReferenceError: Cannot access 'age' before initialization
```

---

## Timeline

```text
Scope Starts
    ↓
TDZ
    ↓
let age = 25
    ↓
Variable Accessible
```

---

## Why Does TDZ Exist?

TDZ prevents accidental access to variables before they are initialized, making code safer and easier to debug.

---

## `const` and TDZ

```javascript
const PI = 3.14;
```

Rules:

* Must be initialized during declaration.
* Cannot be reassigned.
* Remains in TDZ until initialization.

---

## Interview Points

* `let` and `const` are hoisted.
* They are not initialized during the creation phase.
* Accessing them before initialization throws a `ReferenceError`.
* TDZ improves code safety and predictability.
* TDZ exists only for `let` and `const`, not for `var`.


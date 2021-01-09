# Javascript - The Real Guide

## Execution context

In JavaScript, execution context is an abstract concept that holds information about the environment within which the current code is being executed.

The JavaScript engine creates an execution context before any code is executed. This is the global execution context. From that point on, a new execution context gets created *every time* a function is executed, as the engine parses through your code.

Whenever a function is executed, a new execution context gets created.

For this reason, scope and execution context are closely related, but not the same.

Execution Context Creation

When a new execution context is created on a function call, the JavaScript engine needs to spend a little bit of time to configure it, in preparation for the execution.

The following happens during this phase.

* Creation of a scope
* Creation of a scope chain
* Determination of the value of this

### Scope

An execution context needs to know about its own scope — in other words, it needs to decide which variables and functions it has access to. Hoisting is performed at this stage, as the engine scans through your code for variable and function declarations, and puts them into memory.

In addition to its own scope, every execution context has a reference to its outer scopes (if any), all the way up to the global scope. This chain of reference is what we call a scope chain.

Important: A scope chain of a given execution context does not contain any information on its sibling scopes (those sit within the same outer function), nor on its children scopes (those sit within it). This is why a) you can access the global variable from local scopes, but not vice versa, and b) you cannot access local variable from other local scopes.

### Value of “this”

Every execution context also has a special variable this. Read more about `this` in the section below.

## Block scope

`var` declarations, unlike `let`, have no block scope:

```javascript
var babyAge = 1;
var isBirthday = true;

if (isBirthday) {
	var babyAge = 2; 
}

console.log(babyAge); // Ah! This prints 2
```

## this

In javascript, you have access to the `this` keyword at **any** time. Unless there are certain conditions met this will **always refer to the global object Window** (or global in Node).

```javascript
function printThis() {
  console.log(this)
}

printThis() // Prints window
```

## Strict mode

When in strict mode, you will more often see that `this` evaluates to `undefined` This is to avoid having an unexpected scope. Rarely will someone want to refer to the `window` object using `this`.

## So when does this change?

### In object methods

This changes in object methods. A *method* is a function stored as an object property.

```javascript
const country = {
  name: 'USA',

  describe() {
    console.log(this.name)
  },
}

country.describe() // Prints "USA"
```

In this cae, `this` becomes the object that owns the method (america)

#### In a nested scope

In a nested object, this refers to the current object scope of the method. In the following example, this.symbol within the details object refers to details.symbol.

```javascript
const america = {
  name: 'USA',
  details: {
    symbol: 'eagle',
    printDetails() {
      console.log(`The symbol is the ${this.symbol}.`)
    },
  },
}

america.details.printDetails() // Prints The Symbol i the eagle.
```

### When using the new keyword

When you use the `new` keyword, it creates an instance of a constructor function or class.

#### Function constructor

```javascript
function Country(name, yearFounded) {
  this.name = name
  this.yearFounded = yearFounded

  this.describe = function() {
    console.log(`${this.name} was founded in ${this.yearFounded}.`)
  }
}

const america = new Country('The United States of America', 1776)

america.describe()
```

#### Class constructor

A constructor on a class acts the same as a constructor on a function.

```javascript
class Country {
  constructor(name, yearFounded) {
    this.name = name
    this.yearFounded = yearFounded
  }

  describe() {
    console.log(`${this.name} was founded in ${this.yearFounded}.`)
  }
}

const america = new Country('The United States of America', 1776)

america.describe()
```

### A DOM Event Handler

In the browser, there is a special this context for event handlers. In an event handler called by addEventListener, this will refer to event.currentTarget.

```javascript
const button = document.createElement('button')
button.textContent = 'Click me'
document.body.append(button)

button.addEventListener('click', function(event) {
  console.log(this) // Prints <button>Click me</button>
})
```

### Explicit Context

Using `call`, `apply`, or `bind`, you can explicitly determine what this should refer to.

### Arrow Functions

Arrow functions do not have their own this binding. Instead, they go up to the next level of execution.

In this example, you’ll create and append button to the DOM like before, but the class will have an event listener that will change the text value of the button when clicked.

This tactic is often used on class methods in frameworks like React.

```javascript
const button = document.createElement('button')
button.textContent = 'Click me'
document.body.append(button)

class Display {
  constructor() {
    this.buttonText = 'New text'

    button.addEventListener('click', event => {
      event.target.textContent = this.buttonText
    })
  }
}

new Display()
```

## Sources

https://dev.to/digitalocean/understanding-this-bind-call-and-apply-in-javascript-dla (great article)

https://codeburst.io/javascript-demystified-variable-hoisting-c3c4d2e8fd40

https://codeburst.io/js-demystified-04-execution-context-97dea52c8ac6
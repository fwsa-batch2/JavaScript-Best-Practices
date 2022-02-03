# JavaScript Best Practices

Original Repository - [ryanmcdermott/clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript)

This is a simple guide to help you in producing a readable, reusable, and refactorable software in JavaScript.

# Variables

## Naming Variables

Use of meaningful and pronounceable variable names

BAD:

```javascript
const yyyymmdstr = "2021-01-30"
```

GOOD:

```javascript
const currentDate = "2021-01-30"
```

## Same Vocabulary

use same vocabulary for the same type of variable

BAD:

```javascript
const userInfo = getUserInfo();
const clientData = getClientData();
const customerRecord = getCustomerRecord();
```

GOOD:

```javascript
const user = getUser();
```

## Searchable Names

We will read more code then we will ever write. It's important that the code we do write is readable and searchable.

BAD:

```javascript
// What the heck is 86400000 for?
setTimeout(blastOff, 86400000);
```

GOOD:

```javascript
// Declare them as capitalized named constants.
const MILLISECONDS_PER_DAY = 60 * 60 * 24 * 1000; //86400000;

setTimeout(blastOff, MILLISECONDS_PER_DAY);
```

## Avoid Mental Mapping

be explicit

BAD:

```javascript
const cities= ["Chennai", "Madurai", "Coimbature"];
cities.forEach( function (c) {
    console.log(c);
});
```

GOOD:

```javascript
const cities= ["Chennai", "Madurai", "Coimbature"];
cities.forEach( function(city) {
  console.log(city);
});
```

## Un**needed Context**

If your object name tells you something, don't repeat that in your variable name.

BAD:

```javascript
const car = {
  carMake: "Honda",
  carModel: "Accord",
  carColor: "Blue"
};
```

GOOD:

```javascript
const car = {
  make: "Honda",
  model: "Accord",
  color: "Blue"
};
```

# Functions

## Arguments

Limiting the amount of function parameters is incredibly important. Having more than three leads to a combinatorial explosion where you have to test tons of different cases with each separate argument.

BAD:

```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}

createMenu("Foo", "Bar", "Baz", true);
```

GOOD:

```javascript
var 

function createMenu( menuArgument ) {
  // ...
}

  const menuObj = {
    title: "Foo",
    body: "Bar",
    buttonText: "Baz",
    cancellable: true
  };

createMenu(menuObj);
```

## Do only one thing

This is by far the **most important rule in software engineering**.

When functions do more than one thing, they are harder to compose, test, and reason about.

When you can isolate a function to just one action, it can be refactored easily and your code will read much cleaner.

BAD:

```javascript
/** Here, the function is doing two things.
* 1. It is creating a `li` Element.
* 2. It is appending the Element into another Element . 
*/

function displayTaskList(taskList) {
    const ul = document.getElementById("taskList");
    taskList.forEach(function (taskItem) {
        const li = document.createElement("li");
        li.classList.add("list-group-item");
        li.innerHTML = taskItem.task;
        ul.appendChild(li);
    });
}
```

GOOD:

```javascript

// This function create a `li` Element
function generateTaskElement(taskItem) {
    const li = document.createElement("li");
    li.classList.add("list-group-item");
    li.innerHTML = taskItem.task;
    return li;
}

// This function appends the `li` Element into another Element
function displayTaskList(taskList) {
    const ul = document.getElementById("taskList");
    taskList.forEach(function (taskItem) {
        const li = generateTaskElement(taskItem);
        ul.appendChild(li);
    });
}
```

## Tell Me

Function names should say what they do

BAD:

```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// It's hard to tell from the function name what is added
addToDate(date, 1);
```

GOOD:

```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```

## Functions should only be one level of abstraction

When you have more than one level of abstraction your function is usually doing too much. Splitting up functions leads to reusability and easier testing.

BAD:

```javascript
function validation(value, validations) {
    const value = value.trim();
    const minLength = validations.minLength
    const maxLength = validations.maxLength;

    // check if value meets minimum & maximum length
    if (minLength) {
        if (value.length > minLength) {
            if (maxLength) {
                if (value.length < maxLength) {
                    return true;
                } else {
                    console.error(`Value must be at most ${maxLength} characters long`);
                    return false;
                }
            }
        } else {
            console.error(`Value must be at least ${minLength} characters long`);
            return false;
        }
    }
}

const validationsObj = {
    minLength: 3,
    maxLength: 10
};

validation("foo", validationsObj);

```

GOOD:

```javascript
function validation(value, validations) {
    const value = value.trim();
    const minLength = validations.minLength
    const maxLength = validations.maxLength;

    // check if value meets minimum length
    if (minLength && value.length < minLength) {
        console.error(`Value must be at least ${minLength} characters long`);
        return false;
    }

    // check if value meets maximum length
    if (maxLength && value.length > maxLength) {
        console.error(`Value must be at most ${maxLength} characters long`);
        return false;
    }

    return true;
}

const validationsObj = {
    minLength: 3,
    maxLength: 10
};

validation("foo", validationsObj);
```

## **Avoid Side Effects**

A function produces a side effect if it does anything other than take a value in and return another value or values.

A side effect could be modifying some global variable, writing to a file, or accidentally transferring all your money to a stranger.

BAD:

```javascript
// Global variable referenced by following function.
// If we had another function that used this name, now it'd be an array and it could break it.
let name = "Ryan McDermott";

function splitIntoFirstAndLastName() {
  name = name.split(" ");
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

GOOD:

```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(" ");
}

const name = "Ryan McDermott";
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```

## **Remove dead code**

Dead code is just as bad as duplicate code. There's no reason to keep it in your codebase. If it's not being called, get rid of it! It will still be safe in your version history if you still need it.

BAD:

```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

GOOD:

```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

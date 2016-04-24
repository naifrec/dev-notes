# Front-end development

This document holds a summary of necessary knowledge to understand and use
javascript together with Ember.js framework.

This follows the ember [guideline]('https://guides.emberjs.com/v2.5.0/') and
sums up the information.

## 1. Prerequisites
### 1.1 Promises
Promises are the native way to deal with asynchrony in your JavaScript code.

A [Promise]('https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise') represents a proxy for a value not necessarily known when the promise is created. It allows you to associate handlers to an asynchronous action's eventual success value or failure reason. This lets asynchronous methods return values like synchronous methods: instead of the final value, the asynchronous method returns a promise of having a value at some point in the future.

A Promise is in one of these states:
- **pending**: initial state, not fulfilled or rejected.
- **fulfilled**: meaning that the operation completed successfully.
- **rejected**: meaning that the operation failed.

A pending promise can become either fulfilled with a value, or rejected with a reason (error). When either of these happens, the associated handlers queued up by a promise's then method are called. (If the promise has already been fulfilled or rejected when a corresponding handler is attached, the handler will be called, so there is no race condition between an asynchronous operation completing and its handlers being attached.)

As the Promise.prototype.then() and Promise.prototype.catch() methods return promises, they can be chained—an operation called composition.

### 1.2 JavaScript modules
See the JavaScript module export and import structure succintely presented
[here]('http://jsmodules.io/')

## 2. ECMAScript 6

### 2.1 Block bindings
#### 2.1.1 Global overview of variable declaration
There are three main ways to declare a variable in js, `var`, `let` and `const`.
All these declaration declare in terms of scope.

`var` is **hoisted** which means it will be brought at the top of the function
it was declared on. This leads to this behavior:

```
function getValue(condition) {

    if (condition) {
        var value = "blue";

        // other code

        return value;
    } else {

        // value exists here with a value of undefined

        return null;
    }

    // value exists here with a value of undefined
}
```

To avoid the hoisting behavior where a variable is accessible everywhere in the
scope it was defined, you can use `let` which will instantiate a variable **only
accessible in the scope it was declared**. We call it **block-level declaration**.
 See next example:

```
function getValue(condition) {

    if (condition) {
        let value = "blue";

        // other code

        return value;
    } else {

        // value doesn't exist here

        return null;
    }

    // value doesn't exist here
}
```

Trying to **redeclare a variable will throw an error** as follow:
```
var count = 30;

// Syntax error
let count = 40;
```
However if you redeclare the variable with let in a block inside the global
scope then the new declaration will obscure the global one for the time of
the block, see below:

```
var count = 30;

// Does not throw an error
if (condition) {

    let count = 40;

    // more code
}
```
`const` is also a **block-level declaration**, and has similar behavior to `let`,
 however it **requires to be initialized on declaration**.
```
// Valid constant
const maxItems = 30;

// Syntax error: missing initialization
const name;
```
#### 2.1.2 Block bindings in loops
An intended behavior of for loops is to throw away its counter variable, which was
meant to be only used inside the scope of the loop. That is why you need to write
the next example not with `var` but with `let` to get intended behavior.
```
for (let i = 0; i < 10; i++) {
    process(items[i]);
}

// i is not accessible here - throws an error, whereas using var would have
// enabled the printing of the value of i
console.log(i);
```

#### 2.1.3 Best practice
The current best practice for block bindings is to use **`const` by default** and only
use `let` **when you know a variable’s value needs to change**. This ensures a basic
level of immutability in code that can help prevent certain types of errors.

You may still want to use `var` in the global scope if you have a code that should be available from the global object. This is most common in a browser when you want to access code across frames or windows.

### 2.2 Strings and regular expressions
#### 2.2.1 Native support of Unicode encoding
Limiting character length to 16 bits wasn’t possible for Unicode’s stated goal of providing a globally unique identifier to every character in the world. These globally unique identifiers, called code points, are simply numbers starting at 0. Code points are what you may think of as character codes, where a number represents a character. A character encoding must encode code points into code units that are internally consistent. For UTF-16, code points can be made up of many code units.

The single Unicode character "𠮷" is represented using surrogate pairs, and as such, the JavaScript string operations above treat the string as having two 16-bit characters. That means:

- The length of text is 2, when it should be 1.
- A regular expression trying to match a single character fails because it thinks there are two characters.
- The charAt() method is unable to return a valid character string, because neither set of 16 bits corresponds to a printable character.

That is why there are natively two distinct methods for strings in JavaScript to access
a character at a given coordinate in the string, see below.
```
var text = "𠮷a";

console.log(text.charCodeAt(0));    // 55362
console.log(text.charCodeAt(1));    // 57271
console.log(text.charCodeAt(2));    // 97

console.log(text.codePointAt(0));   // 134071
console.log(text.codePointAt(1));   // 57271
console.log(text.codePointAt(2));   // 97
```
To compare two strings in an international environment, one should call the `.normalize()` method each string. This is required because
two strings representing fundamentally the same text can contain different code point sequences. For example, the character “æ” and the two-character string “ae” may be used interchangeably but are strictly not equivalent unless normalized in some way.

#### 2.2.2 Methods on strings
ECMAScript 6 includes the following three methods to search substrings in strings:

- The `includes()` method returns true if the given text is found anywhere within the string. It returns false if not.
- The `startsWith()` method returns true if the given text is found at the beginning of the string. It returns false if not.
- The `endsWith()` method returns true if the given text is found at the end of the string. It returns false if not.

Each methods accept two arguments: the text to search for and an optional index from which to start the search.

#### 2.2.3 Regular expressions flags
You can parse easily a regular expression using both `.source` and `.flags` properties
of patterns that represent a regex.
```
var re = /ab/g;

console.log(re.source);     // "ab"
console.log(re.flags);      // "g"
```

You can also directly check if a regex is sticky by calling the property `.sticky`.

These flags (g, u, i, y) are not supported by every versions of ECMAScript engines,
and to check their support a simple function can be (here done for flag y, but easily
transposable to any flag):
```
function hasRegExpY() {
    try {
        var pattern = new RegExp(".", "y");
        return true;
    } catch (ex) {
        return false;
    }
}
```
#### 2.2.4 Template literals basic use
Template literals, unlike usual strings, are contained in \`. The 3 main differences
between strings and template literals are:
- **Substitution**
- **Multiline strings handling**
- **Tags** (not detailed here)

Substitution can be easily done as follow:
```
let count = 10,
    price = 0.25,
    message = `${count} items cost $${(count * price).toFixed(2)}.`;

console.log(message);       // "10 items cost $2.50."
```
A **template literal can access any variable accessible in the scope in which it is defined**. Attempting to use an undeclared variable in a template literal throws an error in both strict and non-strict modes.

To write multiline strings you can go as follow, note the use of `.trim()` methodto remove trailing first line.
```
let html = `
<div>
    <h1>Title</h1>
</div>`.trim();
```

### 2.3 Functions
#### 2.3.1 Default parameters values
ECMAScript 6 makes it easier to provide default values for parameters by providing initializations that are used when the parameter isn’t formally passed. For example:
```
function makeRequest(url, timeout = 2000, callback = function() {}) {

    // the rest of the function

}
```
This function only expects the first parameter to always be passed. The other two parameters have default values, which makes the body of the function much smaller because you don’t need to add any code to check for a missing value.

When makeRequest() is called with all three parameters, the defaults are not used. For example:
```
// uses default timeout and callback
makeRequest("/foo");

// uses default callback
makeRequest("/foo", 500);

// doesn't use defaults
makeRequest("/foo", 500, function(body) {
    doSomething(body);
});
```
ECMAScript 6 considers url to be required, which is why "/foo" is passed in all three calls to makeRequest(). The two parameters with a default value are considered optional.

It’s possible to specify default values for any arguments, including those that appear before arguments without default values in the function declaration. For example, this is fine:
```
function makeRequest(url, timeout = 2000, callback) {

    // the rest of the function

}
```
In this case, the default value for timeout will only be used if there is no second argument passed in or if the second argument is explicitly passed in as undefined, as in this example:
```
// uses default timeout
makeRequest("/foo", undefined, function(body) {
    doSomething(body);
});

// uses default timeout
makeRequest("/foo");

// doesn't use default timeout
makeRequest("/foo", null, function(body) {
    doSomething(body);
});
```
#### 2.3.2 Rest parameters (equivalent to python kwargs)
A rest parameter is indicated by three dots (...) preceding a named parameter. That named parameter becomes **an Array containing the rest of the parameters** passed to the function, which is where the name “rest” parameters originates. For example, a method mimicking `pick()` can be written using rest parameters, like this:
```
function pick(object, ...keys) {
    let result = Object.create(null);

    for (let i = 0, len = keys.length; i < len; i++) {
        result[keys[i]] = object[keys[i]];
    }

    return result;
}
```

*note*: Rest parameters do not affect a function’s length property, which indicates the number of named parameters for the function. The value of length for pick() in this example is 1 because only object counts towards this value.

#### 2.3.3 Spread operator
Closely related to rest parameters is the spread operator. While rest parameters allow you to specify that multiple independent arguments should be combined into an array, the **spread operator allows you to specify an array that should be split and have its items passed in as separate arguments to a function**. The JavaScript engine then splits the array into individual arguments and passes them in, like this:
```
let values = [25, 50, 75, 100]

// equivalent to
// console.log(Math.max(25, 50, 75, 100));
console.log(Math.max(...values));           // 100
```

#### 2.3.4 Arrow Functions
Arrow functions are, as the name suggests, functions defined with a new syntax that uses an “arrow” (=>).
But arrow functions behave differently than traditional JavaScript functions in a number of important ways:

- **No `this`, `super`, `arguments`, and `new.target` bindings** - The value of this, super, arguments, and new.target inside of the function is by the closest containing nonarrow function. (super is covered in Chapter 4.)
- **Cannot be called with `new`** - Arrow functions do not have a [[Construct]] method and therefore cannot be used as constructors. Arrow functions throw an error when used with new.
- **No `prototype`** - since you can’t use new on an arrow function, there’s no need for a prototype. The prototype property of an arrow function doesn’t exist.
- **Can’t change `this`** - The value of this inside of the function can’t be changed. It remains the same throughout the entire lifecycle of the function.
- **No arguments `object`** - Since arrow functions have no arguments binding, you must rely on named and rest parameters to access function arguments..
- **No duplicate named `arguments`** - arrow functions cannot have duplicate named arguments in strict or nonstrict mode, as opposed to nonarrow functions that cannot have duplicate named arguments only in strict mode.

The syntax differs slightly depending on input/output, here are the regular cases:

Single argument, simple output, no brackets whatsoever
```
var reflect = value => value;

// effectively equivalent to:

var reflect = function(value) {
    return value;
};
```
Multiple parentheses are put between brackets:
```
var sum = (num1, num2) => num1 + num2;

// effectively equivalent to:

var sum = function(num1, num2) {
    return num1 + num2;
};
```
No arguments lead to empty parentheses:
```
var getName = () => "Nicholas";

// effectively equivalent to:

var getName = function() {
    return "Nicholas";
};
```
A function that returns nothing need empty curly braces:
```
var doNothing = () => {};

// effectively equivalent to:

var doNothing = function() {};
```
If you want to return an object literal (itself between curly braces) then you need to wrap the braces into parentheses:
```
var getTempItem = id => ({ id: id, name: "Temp" });

// effectively equivalent to:

var getTempItem = function(id) {

    return {
        id: id,
        name: "Temp"
    };
};
```
If you have a multiline body for your arrow function then you need to explicitely use `return`.
The main difference of this body to the body of regular function is that `arguments` does not exist and
is hence not accessible.
```
var sum = (num1, num2) => {
    return num1 + num2;
};
```
Arrow functions **cannot be used with `new`**. Knowing that arrow functions cannot be used with new allows JavaScript engines to further optimize their behavior.
The biggest change to functions in ECMAScript 6 was the addition of arrow functions. Arrow functions are **designed to be used in place of anonymous function expressions**. Arrow functions have a more concise syntax, lexical this binding, and no arguments object. Additionally, arrow functions can’t change their this binding, and so can’t be used as constructors.

### 2.4 Expanded object functionality
#### 2.4.1 Concise syntax
Concise syntax was introduced in ECMAScript 6. For example,  you can **eliminate the duplication that exists around property names and local variables** by using the property initializer shorthand.

```
// Former syntax
function createPerson(name, age) {
    return {
        name: name,
        age: age
    };
}

// Concise syntax
function createPerson(name, age) {
    return {
        name,
        age
    };
}
```
There is also a new way to **assign methods to object literals**:
```
// Former syntax
var person = {
    name: "Nicholas",
    sayName: function() {
        console.log(this.name);
    }
};

// New syntax
var person = {
    name: "Nicholas",
    sayName() {
        console.log(this.name);
    }
};
```
The one difference is that **concise methods may use `super`**, while the nonconcise methods may not.

#### 2.4.2 Computed property names
The square brackets inside the object literal indicate that the property name is computed,
so its contents are evaluated as a string. That means you can also include expressions such as:
```
var suffix = " name";

var person = {
    ["first" + suffix]: "Nicholas",
    ["last" + suffix]: "Zakas"
};

console.log(person["first name"]);      // "Nicholas"
console.log(person["last name"]);       // "Zakas"
```

#### 2.4.3 The Object.is() method
To compare two values in JavaScript, we usually use either the equals operator (`==`) or the identically equals operator (`===`).
Many developers prefer the latter, to avoid type coercion during comparison.
But **even the identically equals operator isn’t entirely accurate**. For example, the values +0 and -0 are considered equal by === even though they are represented differently in the JavaScript engine. Also `NaN === NaN` returns false, which necessitates using `isNaN()` to detect NaN properly.

ECMAScript 6 introduces the **`Object.is()` method to make up for the remaining quirks of the identically equals operator**. This method accepts two arguments and returns true if the values are equivalent. Two values are considered equivalent when they are of the same type and have the same value. Here are some examples:

```
console.log(+0 == -0);              // true
console.log(+0 === -0);             // true
console.log(Object.is(+0, -0));     // false

console.log(NaN == NaN);            // false
console.log(NaN === NaN);           // false
console.log(Object.is(NaN, NaN));   // true

console.log(5 == 5);                // true
console.log(5 == "5");              // true
console.log(5 === 5);               // true
console.log(5 === "5");             // false
console.log(Object.is(5, 5));       // true
console.log(Object.is(5, "5"));     // false
```
In many cases, `Object.is()` works the same as the `===` operator. The only differences are that +0 and -0 are considered not equivalent and NaN is considered equivalent to NaN. But there’s no need to stop using equality operators altogether. **Choose whether to use `Object.is()` instead of `==` or `===` based on how those special cases affect your code.**

#### 2.4.4 The Object.assign() method
Mixins are among the most popular patterns for object composition in JavaScript. In a mixin, one object receives properties and methods from another object. Many JavaScript libraries have a mixin method similar to this:

```
function mixin(receiver, supplier) {
    Object.keys(supplier).forEach(function(key) {
        receiver[key] = supplier[key];
    });

    return receiver;
}
```
The `mixin()` function iterates over the own properties of supplier and copies them onto receiver (a shallow copy, where object references are shared when property values are objects). This allows the receiver to gain new properties without inheritance, as in this code:

```
function EventTarget() { /*...*/ }
EventTarget.prototype = {
    constructor: EventTarget,
    emit: function() { /*...*/ },
    on: function() { /*...*/ }
};

var myObject = {};
mixin(myObject, EventTarget.prototype);

myObject.emit("somethingChanged");
```
Here, `myObject` receives behavior from the `EventTarget.prototype` object. This gives `myObject` the ability to publish events and subscribe to them using the `emit()` and `on()` methods, respectively.

This pattern became popular enough that ECMAScript 6 added the **`Object.assign()` method, which behaves the same way**. The name change from `mixin()` to `assign()` reflects the actual operation that occurs. Since the `mixin()` method uses the assignment operator (=), it cannot copy accessor properties to the receiver as accessor properties. The name Object.assign() was chosen to reflect this distinction. Example of use:

```
function EventTarget() { /*...*/ }
EventTarget.prototype = {
    constructor: EventTarget,
    emit: function() { /*...*/ },
    on: function() { /*...*/ }
}

var myObject = {}
Object.assign(myObject, EventTarget.prototype);

myObject.emit("somethingChanged");
```

#### 2.4.5 Object prototype
**Prototypes are the foundation of inheritance in JavaScript**, and ECMAScript 6 continues to make prototypes
 more powerful. Early versions of JavaScript severely limited what could be done with prototypes.

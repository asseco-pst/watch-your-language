# Scopes
Perhaps one of the most fundamental things you'll need to quickly come to writing bulletproof code and being a better developer.

If you do understand scopes, you do understand where variables/functions are accessible, you are able to change the scope of your code’s context and be able to write faster and more maintainable code, as well as debug much faster.

JavaScript is in fact a *compiled* language instead of  a *"dynamic"* or *"interpreted"* language, like it is described most of the times. In this chapter we'll see how the JavaScript engine affects how variables and functions are defined and how these are accessed.

## What it is
Scope is what we call a *set of rules* that determines where and how a variable (identifier) can be looked-up. It refers to the current context of your code and can be *globally* or *locally* defined.

There are two kinds of look-ups. The LHS (left-hand-side) reference and the RHS (right-hand-side) look-up.

### LHS and RHS

**LHS** (left-hand-side) reference occurs every time you are trying to assign a value to a variable. Think of it like **"who's the target of the assignment"**

**RHS** (right-hand-side) look-up occurs every time you are trying to access to the value from a variable. Think of it like **"who's the source of the assignment"**

**LHS example**

Since JavaScript is compiled, its Engine first compiles code before it executes, spliting up statements like ```var a = 2;``` (*VariableDeclaration*) into two separate steps:

 1. First, ```var a``` (*Identifier*)  to declare it in that Scope. This is performed at the beginning, before code execution.
 2. Later, ```a = 2``` (*AssignmentExpression*) to look up the variable (LHS reference) and assign to it if found.

**RHS example**
LHS and RHS doesn't necessarily mean "left/right side of the = assignment operator". For example ```console.log(a);``` is a RHS reference to the variable ```a```. Here we are looking up to retrieve the value of ```a```so that can be  passed to the ```console.log(...)```.

**LHS && RHS example**

```javascript
function foo(a) {
    console.log(a); // 2
}

foo(2);
```

1. Here there's a LHS look-up for ```foo``` which is executed
2. There's a implied ```a = 2```, performing a LHS look-up for ```a```
3. There's a RHS look-up for the ```console``` object, and a property resolution for the method ```log```
4. There's a RHS look-up for the ```a``` which value is passed as an argument to ```console.log(...)```

### Global scope
```javascript
// global scope
var name = 'John';
```

When you are writting your first JavaScript line, you're on the *global scope*!! **May day may day!**.

Many people say that global scope is bad, but in fact it is your friend. You need it to create modules/APIs that are available across scopes.

With global scope and proper namespacing you can develop really cool and well structured applications.

### Local scope
Local scope is any scope that is created past the global scope. Typically there is only a global scope and each function defined has its own (nested) local scope.

If I create a function and declare variables in it, these become locally scoped. I.E, these are only available inside that function.

```javascript 
// Scope A: Global scope out here
var myFunction = function () {
  // Scope B: Local scope in here
  var foo = 1;
};
```

### Block scope
**New block = New scope**, that's the rule! But only when we are talking about Ecmascript 5 or below.

Example:
```javascript
// Scope A
var myFunction = function () {
  // Scope B
  
  if (true) {
    // Scope C
  } else {
    // Scope D
  }

  while(true) {
    // Scope F
  };
};
```

### Function scope
**New function = New scope**, that's the rule! But only when we are talking about **Ecmascript 5** or below.
Only functions create new scopes, they aren’t created by for or ```while``` *loops* or *expression statements* like ```if``` or ```switch```.
Example:
```javascript
// Scope A
var myFunction = function () {
  // Scope B
  
  if (true) {
    // Scope B
  } else {
    // Scope B
  }

  var myOtherFunction = function () {
    // Scope C
  };
};
```

Anyway, this rule does not always apply.

Since **Ecmascript 6** is not yet support in every browser, we'll stick with **Ecmascript 5**.

### JS namespacing
Like mentioned before, with proper namespacing and global scope you can build awesome things.

Namespacing safes the chance of having unintended collision between two different identifiers with the same name but different intended usages.
Here's and example of a variable ```calculator``` that is globally available and offers two methods that are only available when using the *"calculator"* namespace.

Here the *sum* and *subtract* functions are only available under the *calculator's* scope, keeping the global scope clean.

**Using Object Literal Notation**
```javascript
var calculator = {
  sum: function(a, b) {
    return a + b;
  },
  subtract: function(a, b) {
    return a - b;
  }
};

calculator.sum(1, 1); // 2
```

**Using Module Pattern**
```javascript
var calculator = (function(){
  function sum(a, b) {
    return a + b;
  };

  function subtract(a, b) {
    return a - b;
  }

  return {
    sum: sum,
    subtract: subtract,
  }
}());
```

### Nested Scope
Generally, your code is full on scopes inside each other. This happens when you have a function nested inside another function.

If a function can not be found in the immediate scope, the next outer containing scope is consulted, continuing until found or until the outermost (global) scope has been reached.

```javascript
function foo(a) {
    console.log(a + b); // 2
}

var bar = 2;
foo(2);
```

```b``` cannot be resolved inside the function ```foo```, but it can be resolved in the Scope surrounding it (in this case, the global).

To visualize the process of nested Scope resolution, I want you to think of this tall building.

<img src="https://github.com/getify/You-Dont-Know-JS/raw/master/scope%20%26%20closures/fig1.png" width="400"/>

**Example**
```javascript
function foo(a) {

    var b = a * 2;

    function bar(c) {
        console.log( a, b, c );
    }

    bar(b * 3);
}

foo( 2 ); // 2 4 12
```

There are 3 nestes scopes in the above code snippet. Which are identified in the following image.

<img src="https://github.com/getify/You-Dont-Know-JS/raw/master/scope%20%26%20closures/fig2.png" width="400"/>

* **Bubble 1** encompasses the global scope, and has just one identifier in it: *foo*.
* **Bubble 2** encompasses the scope of foo, which includes the three identifiers: *a*, *bar* and *b*.
* **Bubble 3** encompasses the scope of bar, and it includes just one identifier: *c*.


## Hoisting
There's a temptation to think that all of the code you see in a JavaScript program is interpreted line-by-line, top-down in order, as the program executes. While that is substantially true, there's one part of that assumption which can lead to incorrect thinking about your program.

Code snippet 1:
```javascript
a = 2;

var a;

console.log(a); // output?
```

Some would expect undefined, since the var a statement comes after the a = 2, and it would seem natural to assume that the variable is re-defined, and thus assigned the default undefined. However, the output will be 2.

Code snippet 2:
```javascript
console.log(a); // output?

var a = 2;
```

From the code snippet 1, it may be tenting to awnser ```2``` or to awnser that *"ReferenceError"* will be thrown.

In fact, neither the awnsers is correct. ```undefined``` is the output because of **Hoisting**.

## References
[Everything you wanted to know about JavaScript scope](https://toddmotto.com/everything-you-wanted-to-know-about-javascript-scope/)

[You Don't Know JS: Scope & Closures](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20&%20closures/README.md#you-dont-know-js-scope--closures)

[Explaining JavaScript Scope And Closures](https://robertnyman.com/2008/10/09/explaining-javascript-scope-and-closures/)

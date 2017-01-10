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

### JS namespacing example
Like mentioned before, with proper namespacing and global scope you can build awesome things.
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
Generally, your code is full on scopes inside each other. This happens when you have a function or block nested inside another function or block.

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

## Lexical scope

## References
[Everything you wanted to know about JavaScript scope](https://toddmotto.com/everything-you-wanted-to-know-about-javascript-scope/)

[You Don't Know JS: Scope & Closures](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20&%20closures/README.md#you-dont-know-js-scope--closures)

[Explaining JavaScript Scope And Closures](https://robertnyman.com/2008/10/09/explaining-javascript-scope-and-closures/)

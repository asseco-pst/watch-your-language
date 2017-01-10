# Scopes
Perhaps one of the most fundamental things you'll need to quickly come to writing bulletproof code and being a better developer.

If you do understand scopes, you do understand where variables/functions are accessible, you are able to change the scope of your code’s context and be able to write faster and more maintainable code, as well as debug much faster.

JavaScript is in fact a *compiled* language instead of  a *"dynamic"* or *"interpreted"* language, like it is described most of the times. In this chapter we'll see how the JavaScript engine affects how variables and functions are defined and how these are accessed.

## What it is
Scope is what we call a *set of rules* that determines where and how a variable (identifier) can be looked-up.

There are two kinds of look-ups. The LHS (left-hand-side) reference and the RHS (right-hand-side) look-up.

## LHS and RHS

**LHS** (left-hand-side) reference occurs every time you are trying to assign a value to a variable. Think of it like **"who's the target of the assignment"**

**RHS** (right-hand-side) look-up occurs every time you are trying to access to the value from a variable. Think of it like **"who's the source of the assignment"**

### LHS example

Since JavaScript is compiled, its Engine first compiles code before it executes, spliting up statements like ```var a = 2;``` (*VariableDeclaration*) into two separate steps:

 1. First, ```var a``` (*Identifier*)  to declare it in that Scope. This is performed at the beginning, before code execution.
 2. Later, ```a = 2``` (*AssignmentExpression*) to look up the variable (LHS reference) and assign to it if found.

### RHS example
LHS and RHS doesn't necessarily mean "left/right side of the = assignment operator". For example ```console.log( a );``` is a RHS reference to the variable ```a```. Here we are looking up to retrieve the value of ```a```so that can be  passed to the ```console.log(...)```.

### RHS && LHS example

```javascript
function foo(a) {
    console.log( a ); // 2
}

foo( 2 );
```

1. Here there's a LHS look-up for ```foo``` which is executed
2. There's a implied ```a = 2```, performing a LHS look-up for ```a```
3. There's a RHS look-up for the ```console``` object, and a property resolution for the method ```log```
4. There's a RHS look-up for the ```a``` which value is passed as an argument to ```console.log(...)```

## Global scope

## References
[Everything you wanted to know about JavaScript scope](https://toddmotto.com/everything-you-wanted-to-know-about-javascript-scope/)

[You Don't Know JS: Scope & Closures](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20&%20closures/README.md#you-dont-know-js-scope--closures)

```javascript
function() {
  console.log(foo); // undefined instead of "ReferenceError: foo is not defined"
  var foo = 'bar';
}
```
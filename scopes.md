# Scopes
Perhaps one of the most fundamental things you'll need to quickly come to writing bulletproof code and being a better developer.

If you do understand scopes, you do understand where variables/functions are accessible, you are able to change the scope of your code’s context and be able to write faster and more maintainable code, as well as debug much faster.

JavaScript is in fact a *compiled* language instead of  a *"dynamic"* or *"interpreted"* language, like it is described most of the times. In this chapter we'll see how the JavaScript engine affects how variables and functions are defined and how these are accessed.

## What it is
Scope is what we call a *set of rules* that determines where and how a variable (identifier) can be looked-up.

There are two kinds of look-ups. The LHS (left-hand-side) reference, when you are doing an assigning to a variable and, in the other hand, the RHS look-up (right-hand-side), when you are retrieving a variable value.

Since JavaScript is compiled, its Engine first compiles code before it executes, spliting up statements like ```var a = 2;``` into two separate steps:

 1. First, var a to declare it in that Scope. This is performed at the beginning, before code execution.
 2. Later, a = 2 to look up the variable (LHS reference) and assign to it if found.

## Global scope

## Compiler Theory

```javascript
function() {
  console.log(foo); // undefined instead of "ReferenceError: foo is not defined"
  var foo = 'bar';
}
```
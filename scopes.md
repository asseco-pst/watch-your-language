# Scopes
Perhaps one of the most fundamental things you'll need to quickly come to writing bulletproof code and being a better developer.
If you do understand scopes, you understand where variables/functions are accessible, be able to change the scope of your code’s context and be able to write faster and more maintainable code, as well as debug much faster.

Although JavaScript can much of the times be identified as a *"dynamic"* or *"interpreted"* , it is in fact a *compiled* language and this has all to have to how variables are defined and accessed.
The JS engine compiles your code right before (and sometimes during!) execution. 

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
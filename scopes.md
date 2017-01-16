 * [What it is](scopes#what-it-is)
 * [Function vs block scope](scopes#local-scope)
 * [Nested scope](scopes#nested-scope)
 * [Hoisting](scopes#hoisting)
 * [Closures](scopes#closures)

# Scopes
Perhaps one of the most fundamental things you'll need to quickly come to writing bulletproof code and being a better developer.

If you do understand scopes, you do understand where variables/functions are accessible, you are able to change the scope of your code’s context and be able to write faster and more maintainable code, as well as debug much faster.

JavaScript is in fact a *compiled* language instead of  a *"dynamic"* or *"interpreted"* language, like it is described most of the times. In this chapter we'll see how the JavaScript engine affects how variables and functions are defined and how these are accessed.

## What it is
Scope is what we call a *set of rules* that determines where and how a variable (identifier) can be looked-up. It refers to the current context of your code and can be *globally* or *locally* defined.

There are two kinds of look-ups. LHS (left-hand-side) and RHS (right-hand-side) look-ups.

### LHS and RHS

**LHS** (left-hand-side) look-up occurs every time you are trying to assign a value to a variable. Think of it like **"who's the target of the assignment"**

**RHS** (right-hand-side) look-up occurs every time you are trying to access to the value from a variable. Think of it like **"who's the source of the assignment"**

**LHS example**

Since JavaScript is compiled, its Engine first compiles code before it executes, spliting up statements like ```var a = 2;``` (*VariableDeclaration*) into two separate steps:

 1. First, ```var a``` (*Identifier*)  to declare it in that Scope. This is performed at the beginning, before code execution.
 2. Later, ```a = 2``` (*AssignmentExpression*) to look up the variable (LHS reference) and assign to it if found. If not found, the variable is declared in the outermost scope (the global scope).

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

There are two ways to create local scopes, **block scope** and **function scope**. 
In fact only function scope is supported by *Ecmascript 5* or lower, being the block scope only supported by *Ecmascript 6* or higher.

If I create a new scope and declare variables in it, these become *locally scoped*. I.E, these are only available inside that scope.

```javascript 
// Scope A: Global scope out here
var myFunction = function () {
  // Scope B: Local scope in here
  var foo = 1;
};
```

#### Block scope
**New block = New scope**, that's the rule! But only when we are talking about Ecmascript 6 or higher.

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

#### Function scope
**New function = New scope**, that's the rule!
In **Ecmascript 5** or lower "only" functions create new scopes, they aren’t created by for or ```while``` *loops* or *expression statements* like ```if``` or ```switch```.

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

Anyway, this rule does not always apply. That's why I said *"only"* in quotes. 
Because, even in *Ecmascript 5* there are two ways of creating block scope, these are ```with``` and ```try/catch```.

**with**
```javascript
var a, x, y;
var r = 10;

with (Math) {
  a = PI * r * r;
  x = r * cos(PI);
  y = r * sin(PI / 2);
}
```

**try/catch**
```javascript
try {
    undefined(); // illegal operation to force an exception!
}
catch (err) {
    console.log( err ); // works!
}

console.log( err ); // ReferenceError: `err` not found
```

**Note:** Since **Ecmascript 6** is not yet support in every browser, we'll stick with **Ecmascript 5**.

### JS namespacing
Like mentioned before, with proper namespacing and global scope you can build awesome things.

Namespacing safes the chance of having unintended collision between two different identifiers with the same name but different intended usages.
Here's an example of a variable ```calculator``` that is globally available and offers two methods that are only available when using the *"calculator"* namespace.

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
Generally, your code is full of scopes inside each other. This happens when you have a function nested inside another function.

If a function cannot be found in the immediate scope, the next outer containing scope is consulted, continuing until found or until the outermost (global) scope has been reached.

```javascript
function foo(a) {
  console.log(a + b);
}

var b = 2;

foo(2); // 4
```

```b``` cannot be resolved inside the function ```foo```, but it can be resolved in the Scope surrounding it (in this case, the global).

To visualize the process of nested Scope resolution look at this large onion.

<img src="https://cdn-images-1.medium.com/max/800/1*WIBH8HW9WzoKfBsQPGFHHg.png" width="400"/>

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

**Code snippet 1:**
```javascript
a = 2;

var a;

console.log(a); // output?
```

Some would expect ```undefined```, since the ```var a``` statement comes after the ```a = 2```, and it would seem natural to assume that the variable is re-defined, and thus assigned the default undefined. 
However, the output will be ```2```.

**Code snippet 2:**
```javascript
console.log(a); // output?

var a = 2;
```

From the code snippet 1, it may be tenting to awnser ```2``` or to awnser that ```ReferenceError``` will be thrown.
In fact, neither the awnsers is correct. ```undefined``` is the output because of **Hoisting**.

Remember that since JavaScript is compiled, its Engine first compiles code before it executes, spliting up statements like ```var a = 2;``` (VariableDeclaration) into two separate steps? 
That's what's happenning here.

Our *code snippet 1* should be thought of as being handled like this:
```javascript
var a;
```
```javascript
a = 2;

console.log( a );
```
...where the first part is the compilation and the second part is the execution.

Similarly, our *code snippet 2* is actually processed as:
```javascript
var a;
```
```javascript
console.log( a );

a = 2;
```

**Hoisting**: declaration comes before assignment.

**So Hoisting can be described as**: "is when variable and/or function declarations are "moved" from where they appear in the flow of the code to the top of the code".

**Code snippet 3:**
```javascript
foo();

function foo() {
    console.log( a ); // undefined

    var a = 2;
}
```

The ```foo```'s declaration is hoisted, such that the call on the first line is able to execute.

**Note**: It's also important to note that **hoisting is per-scope**.

Function declarations are hoisted, as we just saw. But function expressions are not.
```javascript
foo(); // not ReferenceError, but TypeError!

var foo = function bar() {
    // ...
};
```
The variable identifier ```foo``` is hoisted and attached to the enclosing scope (global) of this program, so ```foo()``` doesn't fail as a ```ReferenceError```. But ```foo``` has no value yet (as it would if it had been a true function declaration instead of expression). So, ```foo()``` is attempting to invoke the ```undefined``` value, which is a ```TypeError``` illegal operation.

### Functions First
Both function declarations and variable declarations are hoisted, but functions are hoisted first, and then variables.

```javascript
foo(); // 1

var foo;

function foo() {
    console.log( 1 );
}

foo = function() {
    console.log( 2 );
};
```

This snippet is interpreted by the Engine as:
```javascript
function foo() {
    console.log( 1 );
}
var foo;

foo(); // 1

foo = function() {
    console.log( 2 );
};
```

## Closures
> Closure is when a function is able to remember and access its lexical scope even when that function is executing outside its lexical scope.

> We have access to variables defined in enclosing function(s) even after the enclosing function which defines these variables has returned.

Let's see the light!

**Code snippet 1:**
```javascript
var x = 10;

function foo() {
  var y = 20; // free variable
  
  return function bar() {
    var z = 15; // free variable
    return x + y + z;
  }
}

var baz = foo();
baz(); // 45
```

As you can see, ```bar``` is nested within ```foo```. To help you visualize the nesting, see the diagram below:

<img src="https://cdn-images-1.medium.com/max/800/1*CwxZxltknV8DEEm_Y_ykfA.png" width="600"/>

This scope chain, or chain of environments associated with a function, is saved to the function object at the time of its creation. 
In other words, it’s defined statically by location within the source code. (This is also known as **"lexical scoping"**).

**Code snippet 2:**
```javascript
function hello() {
    var message = 'Hello, ';

    return function greet(name) {
      message += name + '!';
      console.log(message);
    }
}

var hey = hello();
hey('Rodolfo'); // Hello, Rodolfo! -- Whoa, closure was just observed, man.
```

The function ```greet()``` has scope access to the inner scope of ```hello()```. But then, we take ```greet()```, the function itself, and return it.

After we execute ```hello()```, we assign the value it returned (our inner ```greet()``` function) to a variable called ```hey```, and then we actually invoke ```hey()```, which of course is invoking our inner function ```greet()```, just by a different identifier reference.

```greet()``` is executed, for sure. But in this case, it's executed outside of its declared lexical scope.

```greet()``` has a lexical scope closure over that inner scope of ```hello()```, which keeps that scope alive for ```greet()``` to reference at any later time.

**```greet()``` still has a reference to that scope, and that reference is called closure.**

### Modules revisited
Remember of talking about JavaScript modules? These are the most poweful pattern who leverages the power of closure.

```javascript
var CoolModule = (function module() {
  var something = "cool";
  var another = [1, 2, 3];

  function doSomething() {
    console.log(something);
  }

  function doAnother() {
    console.log(another.join(" ! "));
  }

  return {
    doSomething: doSomething,
    doAnother: doAnother
  };
}());

CoolModule.doSomething(); // cool
CoolModule.doAnother(); // 1 ! 2 ! 3
```

The most common way of implementing the module pattern is often called "Revealing Module", and it's the variation we present here.

Firstly, ```CoolModule()``` is just a function, but it has to be invoked for there to be a module instance created. Without the execution of the outer function, the creation of the inner scope and the closures would not occur. 
That's why we defined as a **IIFE** (Immediately Invoked Function Expression). But it could be done like the following:

```javascript
function module() {...};
var CoolModule = module();
```

Secondly, the ```CoolModule()``` function returns an object, denoted by the object-literal syntax { key: value, ... }. The object we return has references on it to our inner functions, but not to our inner data variables. We keep those **hidden and private**. It's appropriate to think of this object return value as essentially a **public API** for our module.

This object return value is assigned to the variable ```CoolModule```, and then we can access those property methods on the API, like ```CoolModule.doSomething()```.

**Now we can see closures all around our existing code, and we have the ability to recognize and leverage them to our own benefit!**

## References
[Everything you wanted to know about JavaScript scope](https://toddmotto.com/everything-you-wanted-to-know-about-javascript-scope/)

[You Don't Know JS: Scope & Closures](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20&%20closures/README.md#you-dont-know-js-scope--closures)

[Explaining JavaScript Scope And Closures](https://robertnyman.com/2008/10/09/explaining-javascript-scope-and-closures/)

[Let’s Learn JavaScript Closures](https://medium.freecodecamp.com/lets-learn-javascript-closures-66feb44f6a44#.dl24ka110)

 * [What is `this`](this-and-object-prototypes#what-is-this)
 * [How to set `this`](this-and-object-prototypes#how-to-set-this)
 * [What's an object](this-and-object-prototypes#whats-an-object)
 * [Class vs Prototypes](this-and-object-prototypes#class-vs-prototypes)

# What is `this`

`this` is a binding that is made when a function is invoked and what it contains is based on the call-site (where the function is called).

**^ That** is a mouth full of an explanation, lets break it down and explain what's going on with that definition by showing you some examples.

**Keywords**:
- `call-site` is the technical name for "context" which is **the place where the function is being called**

```javascript
function foo() {
  console.log('what is ->', this)
}

foo()
```

- `foo()` was called on the `window` so the call is the same has having `window.foo()`.
- `this` by default uses the call-site of the function.
- In this case, `this` represents the `window`

```javascript
var newObj = {
  a: '1',
  foo: function foo() {
    console.log('what is ->', this.a)
  }
}

newObj.foo()
```

- Instead of having `window.foo()` we now have `newObj.foo()`
- `this` is no longer the `window`, now it has the `newObject`
- `this.a` inside `foo()` has the same reference as `newObject.a`

# How to set `this`

There are four ways to set the `this` of an object

## Default Binding

```javascript
function foo() {
  console.log( this.a );
}

var a = 2;

foo();
```
- The default binding sets the `this` to the global scope
- Because `a` is in the global scope the `this.a` will log `2`
- That means that when the other kind of binds that we will see in just a minute fail the fallback is to the default binding
- The 1st example of "what is `this`" is also a way to see default binding in action

## Implicit Binding

```javascript
function foo() {
  console.log( this.a );
}

var obj = {
  a: 2,
  foo: foo
};

var a = 4;

obj.foo();
```

- Implicit binding is when the `call-site` is different from the window
- In this case the `this` for `foo()` is the `obj` object
- For instance when you call `obj.foo()` it will log `2` instead of the global variable `a` which is set to `4`
- The 2nd example of "what is `this`" is also a way to see implicit binding in action

```javascript
function foo() {
  console.log( this.a );
}

var obj2 = {
  a: 42,
  foo: foo
};

var obj1 = {
  a: 2,
  obj2: obj2
};

obj1.obj2.foo(); // 42
```

- This example serves to show that `this` references only the last level of an object

## Explicit Binding

```javascript
var foo = {
  nativeAlert: window.alert,
  myAlert: function(val) {
    this.alert(val)
  }
}
```

- If you call `foo.nativeAlert('my message')` it will result in an error because `this` is implicit, instead of referring to the `window` it refers to `foo`
- Likewise, if you call `foo.myAlert('my message')` it will also return an error because `alert` is not defined in `foo`

To solve this problem we can use `.call` at the comment those properties are called to change the `this` to what we want, in this case `window`

```javascript
foo.nativeAlert.call(window, 'my message')
foo.myAlert.call(window, 'my message')
```

- Now you are explicitly telling the function you're calling what context (`this`) you intend for it to have

There are 3 properties that can be used for explicit binding. Bare in mind that if you don't want to change the `this` but still want to use any of those 3 properties you just have to send as the first parameter either `null` or `undefined`

### `bind`

```javascript
function sum() {
  console.log(this.a + this.b)
}

var obj = {
  a: 1,
  b: 2,
}

var result = sum.bind(obj)

result() // 3
```

- `bind` is used when you want to change the context or send some of the parameters but you don't want to call the function just yet

### `call`

```javascript
function sum() {
  console.log(this.a + this.b)
}

var obj = {
  a: 1,
  b: 2,
}

sum.call(obj) //3
```

- `call` is used when you want to change the context and call the function at the moment of execution 

### `apply`

```javascript
Math.max.apply(null, [1,4,3]) // 4
```

- `apply` is just like `call` but instead of sending the parameters one by one you can use an array
- Given the example above, it has the same output has `Math.max(1,4,3)`

## `new` Binding

```javascript
function foo(obj) {
  this.a = obj.a;
  this.b = obj.b;
}

var obj = {
  a: 2,
  b: 4,
}

var bar = new foo(obj);
console.log( bar.a, bar.b ); // 2 4
```

- `new`can be used in functions and sets the `this`
- When you use the new for a function you can call it a "constructor call"

## Priority/Order (higher to lower)
1. new
2. explicit
3. implicit
4. default

# What's an object

## Primitive types
  - `string`
  - `number`
  - `null`
  - `undefined`
  - `boolean`
  - `symbol` <- ES6

Primitive types cannot be changed except `null` and `undefined` each of this primitives has an object with base methods, like `String` object for the `string` primitive.
Anything that isn't a primitive value is considered an `object`, which includes `function` and `array`.
There are some ways to define an object being the most common ones:

```javascript
var obj = { newValue: 3 }

obj.newValue = 'new parameter set with literal declaration'

var myVariable = 'newValue'

obj[myVariable] = 'my new value'
```

## Reference assign

```javascript
var foo = {
  value: 3
}

var bar = foo;

console.log('before assign', foo.value) // 3

bar.value = 4

console.log('bar after assign', bar.value) // 4
console.log('foo after assign', foo.value) // 4
```

- When using objects you must be careful not to use "=" to copy an object
- Object maintain a reference to each other unless you use `Object.assign()` or `Object.create()`
- `Object.assign()` is normally used to merge various objects
- `Object.create()` is usually used to assign an already existing object to a new one without maintaining reference like the next example

```javascript
var foo = {
  value: 3
}

var bar = Object.create(foo)

console.log('before asssign', foo.value) // 3

bar.value = 4

console.log('bar after assign', bar.value) // 4
console.log('foo after assign', foo.value) // 3
```

# Class vs Prototypes

Unlike Java or other languages that implement the class/inheritance pattern, JavaScript implements prototypical inheritance.

Prototypes were a design choice made to try and emulate classical classes. 

You can see a function as a constructor (since only functions have the `.prototype` method) responsible for creating new instances while prototype would be any object to serve as a method. 

Beware that prototypes must be assigned using `Object.create()` or `new` so that they don't maintain reference has shown in the example before.

Sounds confusing? That's normal, this was not very well designed in JS which makes for an harder time to explain and to understand, let's make it easier by seeing how you can implement both both `class` and `prototype` in JS

Note: As of `ES6` the `class` keyword was implemented which is just "syntactic sugar", it's the same as saying that it's not the same as Java classes, it's just running `prototypes` "under the hood".

## Class `ES6`

```javascript
class car {
  constructor(engine, color) {
    this.engine = engine;
    this.color = color;
  }

  hasProperties() {
    console.log('Properties -->', this.engine, this.color)
  }
}

var myCar = new car('v8', 'blue')

myCar.hasProperties() // Properties --> v8 blue
```

## Prototype

```javascript
var car = function(engine, color) {
  this.engine = engine;
  this.color = color;
}

car.prototype.hasProperties = function() {
  console.log('Properties -->', this.engine, this.color)
}

var myCar = Object.create(car.prototype)

myCar.constructor('v8', 'blue')

myCar.hasProperties() // Properties --> v8 blue
```

Has you can see they are the same, is just different ways to be organize your code base. 

One way you can apply everything about objects and `this` is to use composition without risking polluting the `prototype` chain is to use object composition.

## Composition example
```javascript
var hasProperties = function (option) {
  return {
    hasProperties: function() {
      console.log('Properties -->', this.engine, this.color) 
    }
  }
}

var car = function(engine, color) {
  var details = {
    engine: engine,
    color: color,
  }

  return Object.assign( 
    {}, 
    details,
    hasProperties(details))
}

var myCar = car('v8', 'blue')

myCar.hasProperties() //  Properties --> v8 blue
```

# References

[Let's settle `this`](https://medium.com/@nashvail/lets-settle-this-part-one-ef36471c7d97#.eeywsza5b)

[You don't know JS - this & object prototypes](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/toc.md)

[Prototypes](http://raganwald.com/2013/02/10/prototypes.html)

[Class vs prototype](https://medium.com/javascript-scene/master-the-javascript-interview-what-s-the-difference-between-class-prototypal-inheritance-e4cd0a7562e9#.rl4lt19sv)
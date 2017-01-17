 * [What is `this`](this-and-object-prototypes#what-is-this)
 * [How to set `this`](this-and-object-prototypes#how-to-set-this)
 * [What's an object]()
 * [Class vs Objects]()
 * [Prototypes]()

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

- In this example serves to show that `this` references only the last level of an object

## Explicit Binding

## new Binding

 * [What is `this`](this-and-object-prototype#What-is-this)
 * [How to set `this`]()
 * [What's an object]()
 * [Class vs Objects]()
 * [Prototypes]()

# What is `this`

`this` is a binding that is made when a function is invoked and what it contains is based on the call-site where the function is called.

**^ That** is a mouth full of an explanation, lets break it down and explain what's going on with that definition by showing you some examples.

**Keywords**:
- `call-site` it's the technical name for "context" which is **the place were the function is being called**

```javascript
function foo() {
  console.log('what is ->', this)
}

foo()
```

- `foo()` was called on the `window`, it's the same has having `window.foo()`.
- `this` by default it's based on the call-site of the function.
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

- If we didn't want the `window` we would have to put our function inside an object
- Instead of having `window.foo()` we now have `newObj.foo()`
- `this` is no longer the `window`, now it only contains the `newObject` properties, like `this.a`

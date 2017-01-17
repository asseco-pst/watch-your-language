 * [What is `this`](this-and-object-prototype#What-is-this)
 * [How to set `this`]()
 * [What's an object]()
 * [Class vs Objects]()
 * [Prototypes]()

# What is `this`

Before the fancy explanation (and the simplest I can think of) let me clear some confusion about what `this` isn't. It isn't a reference to the function itself and it's not based on a function scope (lexical scope). Simple enough, let's move on...oh, examples? I guess I can give you some examples.

## Itself

To prove my point in saying that `this` is not itself look at the following scenario

```javascript
function foo(num) {
  console.log('my number ->', num)
  
  this.count = num
}

foo.count = 0

foo(10)

console.log('current state ->', foo.count)
```

- When we run the code above we notice that foo.count hasn't changed because `this.count` it's not the same as `foo.count`. 

- Since `foo()` was called on the `window`, it's the same has having `window.foo()`. 

- That means that at the time the function was called the `call-site` (context) was the `window` so, our `this.count` it's actually the same as `window.count`

Take your time to test out what the window, this and foo are and let it sink in, no rush.

## Scope

`this` ends up being is a binding that is made when a function is invoked and what it contains is based on the call-site where the function is called

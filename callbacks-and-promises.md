This page is a **WIP**
 * [Callbacks](callbacks-and-promises#callbacks)
 * [Callback Hell](callbacks-and-promises#callback-hell)
 * [Promises](callbacks-and-promises#promises)
 * [Native vs jQuery](callbacks-and-promises#native-vs-jquery)
 * [Leap into the future](callbacks-and-promises#leap-into-the-future)

# Callbacks

A callback is what the event loop will execute when an item is processed in the queue.
In other words, it's the way the JS engine has of saying "When this function finishes call this one!" which is usually necessary in async operations like ajax calls, read files, timeouts and stuff of the sort.

```javascript
setTimeout(function() {
  console.log('after 5 seconds I was called')
} , 5000)
```

  - This is a simple example of a callback being used, `setTimeout` after 5 seconds will call the functions we are using as a first parameter for the `setTimeout`.

```javascript
$.ajax( "myurl" ).done(function(response) {
  alert( "success", response );
}).fail(function(e) {
  alert( "error", e );
}).always(function() {
  alert( "complete" );
});
```

- This is a more complete example which takes advantage of the `$.ajax` from `jQuery`
- If the call is done successfully then the callback for success (`.done`) and `always` will execute
- If the call is done but an error happens the callback for `fail` is executed
- `jQuery` is being used here for sintax simplicity, this can also be done using `fetch` or `XMLHttpRequest`
- This code has a problem, if we wanted to use the response outside the call back we would have syncronism problems, this will be handled ahead in ["Promises"](callbacks-and-promises#promises).

```javascript
$.ajax( "myurl" )
  .done(successfully)
  .fail(alot)
  .always(doThis);

function successfully(response) {
  alert( "success", response );
}

function alot(e) {
  alert( "error", e );
}

function doThis() {
  alert( "complete" );
}
```

- It's good practice to separate the callbacks so it's easier to debug and navigate using the call stack in the browser developer tools

# Callback Hell
# Promises
# Native vs jQuery
# Leap into the future

# References

[Callback with minions](https://medium.freecodecamp.com/javascript-callbacks-explained-using-minions-da272f4d9bcd#.vwuhbz6vz)

[Callback Hell](http://callbackhell.com/)

[You don't know JS - Async and performance](https://github.com/getify/You-Dont-Know-JS/tree/master/async%20%26%20performance)

[Async and await](http://exploringjs.com/es2016-es2017/ch_async-functions.html)
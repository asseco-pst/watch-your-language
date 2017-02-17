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
}, 5000)
```

  - This is a simple example of a callback being used, `setTimeout` after 5 seconds will call the functions we are using as a first parameter for the `setTimeout`.

```javascript
function sum(val1, val2, callback) {
  var sum = val1 + val2;
  if (callback) callback(sum);
}

function myCallback(response) {
  console.log('Sum result: ', response)
}

sum(1, 2, myCallback)
```

  - You simple send a function as a parameter to be called inside the other function
  - `myCallback` to be called inside `sum` in this case

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

For simplicity (sanity) sake instead of explaining callback hell with request let's just say you want to do a countdown, something like "3...2...1...DONE!" but you can only use timeouts, one contrived way of doing so would be the next example

```javascript
setTimeout(function() {
  console.log('3...')
  setTimeout(function() {
    console.log('2...')
    setTimeout(function() {
      console.log('1...')
      setTimeout(function() {
        console.log('DONE!')
      }, 1000)
    }, 1000)
  }, 1000)
}, 0);
```

  - As you can see it's hard to get a hold of what is going on, even in this simple scenario

```javascript
setTimeout(countFromThree, 0);

function countFromThree() {
  console.log('3...')
  setTimeout(countFromTwo, 1000)
}

function countFromTwo() {
  console.log('2...')
  setTimeout(countFromOne, 1000)
}

function countFromOne() {
  console.log('1...')
  setTimeout(countDownDone, 1000)
}

function countDownDone() {
  console.log('DONE!')
}
```

  - The simplest thing to do it's to name every function and separate them
  - This allow for a better call stack and a more simple to read code
  - It is still contrived and not the best way to have synchronicity, that's when promises enter the frame

# Promises

WIP: Talk about what it is

# Native vs jQuery

**Using Jquery**

**Using Native**

# Leap into the future

```javascript
async function syncExample() {
  const a = await Promise.resolve('foo');
  console.log('and this waits for the await resolve', a);
}

syncExample();
console.log('this continues')
```

  - When using the latest `babel` and in Chrome/Firefox you have `async/await` from `ECMAScript2017`
  - This allows you to have a way more easier code to read while having synchronicity
  - In this case the program continues while `syncExample` is being interpreted
  - When the await is done resolving then the `syncExample` continues it's code

# References

[Callback with minions](https://medium.freecodecamp.com/javascript-callbacks-explained-using-minions-da272f4d9bcd#.vwuhbz6vz)

[Callback Hell](http://callbackhell.com/)

[You don't know JS - Async and performance](https://github.com/getify/You-Dont-Know-JS/tree/master/async%20%26%20performance)

[Async and await](http://exploringjs.com/es2016-es2017/ch_async-functions.html)
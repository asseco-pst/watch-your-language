 * [Mind the gap, Now & later](#mind-the-gap-now-later)
 * [Sync vs Async](#sync-vs-async)
 * [A Program in Chunks](#a-program-in-chunks)
 * [Event Loop](#event-loop)
  * [Call stack](#call-stack)
  * [Blowing the stack](#blowing-the-stack)
  * [Blocking operations](#blocking-operations)
  * [Async callbacks and the Call stack](#async-callbacks-and-the-call-stack)
  * [Is JS single-threaded?](#is-js-single-threaded)
  * [The big picture](#the-big-picture)
  * [Concurrency and the Event Loop](#concurrency-and-the-event-loop)
  * [Async callbacks and the Event Loop](#async-callbacks-and-the-event-loop)
  * [Render Queue](#render-queue)
 * [References](#references)

# Mind the gap, Now & later

There's a gap between a part of your program that runs *now* and another one that runs *later*, where your program isn't actively executing.

The problem most developers new to JS seem to have is that *later* doesn't happen strictly and immediately after *now*. 
The relationship between these two is at the heart of asynchronous programming.

Consider:

```javascript
// ajax(..) is some arbitrary Ajax function given by a library
var data = ajax('http://some.url.1');

console.log(data);
// Oops! `data` generally won't have the Ajax results
```

It is probable that you know that an usual Ajax request won't complete synchronously, which means `data`  won't have the Ajax result.
If `ajax(..)` could block until the response came back, then the `data = ..` assignment would work fine.

But that isn't how it rolls! We make an asynchronous Ajax request *now*, and we won't get the results back until *later*.

A way to handle the Ajax request is using [callback](async) functions or [promises](async). Which we'll see in more detail in the next chapter.

```javascript
// ajax(..) is some arbitrary Ajax function given by a library
ajax('http://some.url.1', function myCallbackFunction(data) {
	console.log( data ); // Yay, it gots me some `data`!
});
```

# Sync vs Async
**Synchronous**: It waits for each operation to complete, after that only it executes the next operation.

Example:
```javascript
var fileData = fs.readFileSync('temp.txt', 'utf8'); // synchronous operation
console.log('File data: ', fileData);
console.log('Foo');

// output:
/* 
 File data: some content
 Foo
*/
```

**Asynchronous**: It never waits for each operation to complete, rather it executes all operations in the first GO only. The result of each operation will be handled once the result is available.

Example:
```javascript
fs.readFile('temp.txt', (err, fileData) => {  // asynchronous operation
  if (err) throw err;
  console.log('File data: ', fileData);
});
console.log('Foo');

// output:
/* 
 Foo
 File data: some content
*/
```


# A Program in Chunks
> function = chunk


Any time you define a function and specify that it should be executed in response to some event (ex. Ajax response), you are creating a *later* chunk of your code, and thus introducing **asynchrony** to your program.

The JS engine itself has never done anything more than execute a single chunk of your program at any given moment, when **asked to**.

"**Asked to.**" By whom? That's the important part!

The JS engine runs inside a hosting environment, which is for most developers the typical web browser, that has handling the execution of multiple chunks of your program over time and it is called **"Event Loop"**.


# Event Loop

**Event Loop** is a JavaScript concurrency model that is quite different from models in other languages like C and Java.

It got its name because of how it's usually implemented, which usually resembles:
```javascript
while (queue.waitForMessage()) {
  queue.processNextMessage();
}
```

"There’s a process that constantly checks whether the call stack is empty, and whenever it’s empty, it checks if the event queue has any functions waiting to be invoked. If it does, then the first function in the queue gets invoked and moved over into the call stack. If the event queue is empty, then this monitoring process just keeps on running indefinitely. And voila — what I just described is the infamous Event Loop!" *- [What is the JavaScript Event Loop?](http://altitudelabs.com/blog/what-is-the-javascript-event-loop/)*

But before we get to know what the Event Loop really does, let's see the surrounding JavaScript environment. Follow me!

<br>

## Call stack

The call stack is pretty straightforward, **last in, first out**.

<img src="http://i.imgur.com/Cnds2ks.gif" width="400"/>

In the above image we have an example of some synchronous code being executed.

1. First we have the `main()` going into the stack. It is just the first thing that goes into the stack when your application starts executing.
2. We call `printSquare()`, which goes into the stack.
3. `printSquare()` calls `square()`, which goes into the stack.
4. `square()` calls `multiply()`, which goes into the stack.
5. `multiply()` returns, jumping out of the stack.
6. `square()` returns, jumping out of the stack.
7. `printSquare()` calls `console.log()`, which goes into the stack.
8. `printSquare()` returns, jumping out of the stack.
9. Code execution finishes and the event loop stays continuously waiting for new events.

<br>

## Blowing the stack

We use to hear "Don't blow the stack!". What does this actually mean? We usually know how to solve these, but do we actually understand why we're doing it?

<img src="http://i.imgur.com/zD2QEAK.gif" width="400"/>

For example, if we have a function `foo` that is recursive and has not stop condition, we'll have what is represented in the image above.

We'll have new tasks entering into the stack continuously and never jumping out of it, hiting the maximum call stack size.

<br>

## Blocking operations

You also must hear a lot "Whatch out, there's blocking operations there, use a callback or a promise instead".

This is really important and it is in fact one of the most powerful techinques to achieve good performance.

Let's see the next example where we have multiple synchronous Ajax requests. <br>I know, I know, they should be asynchronous but these are just synchronous!

<img src="http://i.imgur.com/lmsrfOe.gif" width="400"/>

1. We call `"//foo.com"` and then we wait... and then it returns
2. We call `"//bar.com"` and then we wait... and then it returns
3. We call `"//qux.com"` and then we wait and wait and wait and wait... This can even never end

Since they are synchronous, other tasks cannot enter into the stack because it isn't empty.
And remember **"a new task can only get into the stack when the stack is empty"**.

And why is this a problem?

Because we are in the browser.<br>The browser gets stuck and you can't do anything while there are pending operations on the call stack.<br>We'll see why in a moment.

<br>

## Async callbacks and the Call stack

The solutions for the problem of having blocking operation is asynchronous *callbacks* or *promises*, the `later` code!

A simple example would be:

```javascript
console.log('Hey');

setTimeout(function() {
  console.log('you rock');
}, 5000);

console.log('man');
```

What output could you expect?<br>Probably you would say something like
```javascript
Hey
you rock
man
```

But can you explain me why? If I had defined `0` instead of `5000`, the result would be different?

<br>
Let's see how it behaves with regards to the call stack and then we get our answer.

<img src="http://i.imgur.com/6GEov6g.gif" width="400"/>

In this example we have some asynchronous code, which means, it isn't blocking.

1. First `console.log('hi')` goes into the stack and returns
2. Then the `setTimeout(fn, 5000)` goes into the stack and simply disapears
3. Then `console.log('JSConfEU')` goes into the stack and returns
4. And then suddenly, `5` seconds later, `console.log('there')` goes into the stack and returns

What the heck is going on here? Let's split this bit by bit.

<br>

## Is JS single-threaded?
JS is a single-threaded, non-blocking assynchronous concurrent language. 

While this is not false, it isn't quite true too. If you consider JS by itself, then **YES**, only one thing happens at the time in the JS thread.
It is in the call stack where the magic happens.

Although, in your JS environment you have more than just the JS runtime.
You have the WebAPIs that process your asynchronous code in parallel in a separated thread. And voila, you have multiple threads executing your code.

Let's see this, first in the big picture and then in detail.

<br> 

## The big picture

In the big picture, the JS environment is composed by:
* The JS runtime, composed by:
	* A heap, that is where memory allocations happen
	* A call stack where our program actually executes
* The event loop which decides when a new code chunk must go to the stack
* The WebAPIs like the DOM, the Ajax requests, timers... Things that are handled out of the JS single thread
* A callback queue with all the messages resulting from our WebAPI processing

<br>

## Concurrency and the Event Loop

Now that we have the big picture in our minds let's analyse the asynchronous example given before.

<br>
<img src="http://i.imgur.com/qiZtnHv.gif" width="400"/>

1. First `console.log('hi')` goes into the stack and returns
2. Then the `setTimeout(fn, 5000)` goes into the stack and **redirects** to the WebAPI
3. The WebAPI processes the `timer` and sends the callback function back to the callback/task queue when the timer ends
4. In parallel, the `console.log('JSConfEU')` goes into the stack and returns
5. Finally, when the stack is empty, the callback function can go into the stack

And we're done.

### **This is the Event Loop**! 
**It has a very tiny and simple task. It looks at the callback queue and at the call stack, if the stack is empty it takes the first thing in the queue and pushes it on the stack**

## Async callbacks and the Event Loop
Remember the example back in the "[Async callbacks and the Call stack](#async-callbacks-and-the-call-stack)" section? Where we had `setTimeout(fn, 500)` and asked if the output for `setTimeout(fn, 0)` would be different?

As you can see, when you have a timer callback it is handled by the WebAPI. No mather if your timeout is `0` or higher, it will always have the same behaviour. 
Because:

<img src="http://i.imgur.com/f1wDlTR.gif" width="400"/>

1. First enters into the stack
2. Then goes to the WebAPI
3. Then the callback goes to the callback queue
4. And finally is pushed on the stack when it gets empty

<br>

## Render Queue

Remember I said that blocking operations were bad, specially on the browser? That's because blocking operations on the browser freezes it!

Like there is the **callback queue**, there's the render queue. 

The render queue fires an event every 16ms to render the view and therefore, refreshing the screen. This event, like the tasks on the callback queue can only be pushed on the stack when the stack is empty.
<br>So, If there are blocking operations happening on the call stack, it is not possible to render the screen and the entire page freezes, preventing the user from doing any other operation.


<table>
  <tr>
    <td>Blocking</td>
    <td>Non blocking</td>
  </tr>
  <tr>
    <td><img src="http://i.imgur.com/Sx52jLH.gif" width="400"/></td>
    <td><img src="http://i.imgur.com/xOZOPJz.gif" width="400"/></td>
  </tr>
</table>

<br>


# References
[What the heck is the event loop anyway?](http://2014.jsconf.eu/speakers/philip-roberts-what-the-heck-is-the-event-loop-anyway.html)

[YDKJS - Asynchrony: Now & Later](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch1.md#chapter-1-asynchrony-now--later)

[What is the JavaScript Event Loop?](http://altitudelabs.com/blog/what-is-the-javascript-event-loop/)

[Concurrency model and Event Loop](https://developer.mozilla.org/pt-PT/docs/Web/JavaScript/EventLoop)
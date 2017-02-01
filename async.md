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
	console.log( data ); // Yay, I gots me some `data`!
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

**Event Loop** is a JavaScript concurrency model that is  is quite different from models in other languages like C and Java.

It got its name because of how it's usually implemented, which usually resembles:
```javascript
while (queue.waitForMessage()) {
  queue.processNextMessage();
}
```

"There’s a process that constantly checks whether the call stack is empty, and whenever it’s empty, it checks if the event queue has any functions waiting to be invoked. If it does, then the first function in the queue gets invoked and moved over into the call stack. If the event queue is empty, then this monitoring process just keeps on running indefinitely. And voila — what I just described is the infamous Event Loop!" *- [What is the JavaScript Event Loop?](http://altitudelabs.com/blog/what-is-the-javascript-event-loop/)*

Let's see this in practice. Follow me!

<br>

## Call stack

The call stack is pretty straightforward, **last in, first out**.

<img src="/uploads/5c55fa14b4b00d40c05054a4907677c9/www.GIFCreator.me_D3KgvJ.gif" width="400"/>

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

<img src="/uploads/f03eff5ca5cd059d801e84379249d125/www.GIFCreator.me_CF6hUR.gif" width="400"/>

For example, if we have a function `foo` that is recursive and has not stop condition, we'll have what is represented in the image above.

We'll have new tasks entering into the stack continuously and never jumping out of it. Hiting the maximum call stack size.

<br>

## Blocking operations

You also must hear a lot "Whatch out, there's blocking operations there, use a callback or a promise instead".

This is really important and it is in fact one of the most powerful techinques to achieve good performance.

Let's see the next example where we have multiple synchronous Ajax requests. <br>I know, I know, they should be asynchronous but these are just synchronous!

<img src="/uploads/78f0030a4e830d6ab7cf3910dd01f9a1/www.GIFCreator.me_mtIJtE.gif" width="400"/>

1. We call `"//foo.com"` and then we wait... and then it returns
2. We call `"//bar.com"` and then we wait... and then it returns
3. We call `"//qux.com"` and then we wait and wait and wait and wait... This can even never end

Since they are synchronous, other tasks cannot enter into the stack because it isn't empty.
And remeber **"a new task can only get into the stack when the stack is empty"**.

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
}, 0);

console.log('man');
```

What output could you expect?<br>Probably you would say something like
```javascript
Hey
you rock
man
```

Because the timeout is `0`. But actually the output is:
```javascript
Hey
man
you rock
```

Let's see how it behaves with regards to the call stack.

<img src="/uploads/ff1e042365b8553a8a0180341a213698/www.GIFCreator.me_0hw8rn.gif" width="400"/>

In this example we have some asynchronous code, which means, it isn't blocking.

## Is JS single-threaded?
JS is a single-threaded, non-blocking assynchronous concurrent language. 

While this is not false, it isn't quite true too. If you consider JS by itself, then **YES**, only one thing happens at the time in the JS thread.
It is in the callstack where the magic happens.

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

<img src="/uploads/1e664b873ed845bbbefb5018ff38c081/javascript_event_loop.png" width="400"/>

Now in detail.

<br>
<img src="/uploads/dc5bb91043dc7fcf854e51f4b8ef3ce5/www.GIFCreator.me_vjHNc0.gif" width="400"/>

## Concurrency and the Event Loop

<img src="/uploads/12c7743570fd88e8c95ed26bfe7d08ac/www.GIFCreator.me_UU3q43.gif" width="400"/>

As you can see, when you have a timer callback this is handled by the WebAPI. No mather if your timeout is `0` or higher, it will always have the same behaviour. 
1. First enters into the stack
2. Then goes to the WebAPI
3. When it is resolved, goes to the callback queue
4. And finally goes into the stack to be printed in the console.

<br>

## Async call II
<img src="/uploads/21146bab33da66bb663417b92317ffe5/www.GIFCreator.me_448cr9.gif" width="400"/>

<br>

## Asynchronous III
<img src="/uploads/44cbb171d94f0c91cfece948527c392d/www.GIFCreator.me_FsVqLX.gif" width="400"/>

<br>

## Asynchronous IV
<img src="/uploads/35ea0909069d4c397fa32011b75e2201/www.GIFCreator.me_FsVqLX.gif" width="400"/>

<br>

## Render Queue I
<img src="/uploads/6e1989f87e8e81dbac522170d15ecad6/www.GIFCreator.me_xcjwOq.gif" width="400"/>

<br>

## Render Queue II
<img src="/uploads/59be802a9a473c34c5c1ae2bb6b0467c/www.GIFCreator.me_xxkOsW.gif" width="400"/>


# References
[What the heck is the event loop anyway?](http://2014.jsconf.eu/speakers/philip-roberts-what-the-heck-is-the-event-loop-anyway.html)

[YDKJS - Asynchrony: Now & Later](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch1.md#chapter-1-asynchrony-now--later)

[What is the JavaScript Event Loop?](http://altitudelabs.com/blog/what-is-the-javascript-event-loop/)

[Concurrency model and Event Loop](https://developer.mozilla.org/pt-PT/docs/Web/JavaScript/EventLoop)
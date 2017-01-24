# Mind the gap

There's a gap between a part of your program that runs *now* and another one that runs *later*, where your program isn't actively executing.
The relationship between these two is at the heart of asynchronous programming.


## A Program in Chunks
function = Chunk

```javascript
// ajax(..) is some arbitrary Ajax function given by a library
var data = ajax( "http://some.url.1" );

console.log( data );
// Oops! `data` generally won't have the Ajax results
```

```javascript
// ajax(..) is some arbitrary Ajax function given by a library
ajax( "http://some.url.1", function myCallbackFunction(data){

    console.log( data ); // Yay, I gots me some `data`!

} );
```

Any time you wrap a portion of code into a function and specify that it should be executed in response to some event (timer, mouse click, Ajax response, etc.), you are creating a later chunk of your code, and thus introducing asynchrony to your program.

The JS engine itself has never done anything more than execute a single chunk of your program at any given moment, when **asked to**.

"**Asked to.**" By whom? That's the important part!

The JS engine runs inside a hosting environment, which is for most developers the typical web browser.


# Sync
```javascript
var res = Math.trunc(4.2);
console.log(res);  // 4
```

# Async
```javascript
var res = fetch('http://google.com');
console.log(res);  // undefined
```
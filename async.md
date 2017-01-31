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

It is probable that you know that an usual Ajas request won't complete synchronously, which means `data`  won't have the Ajax result.
If ajax(..) could block until the response came back, then the `data = ..` assignment would work fine.

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

<a href="http://freegifmaker.me/images/2abLC/"><img src="http://i.freegifmaker.me/1/4/8/5/8/7/1485874829953580.gif?1485874842" alt="gifs website"/></a><br/><a href="http://www.freegifmaker.me/">Stack 01<a/>
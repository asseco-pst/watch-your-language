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

## call stack
![www.GIFCreator.me_D3KgvJ](/uploads/5c55fa14b4b00d40c05054a4907677c9/www.GIFCreator.me_D3KgvJ.gif)

## blowing the stack
![www.GIFCreator.me_CF6hUR](/uploads/f03eff5ca5cd059d801e84379249d125/www.GIFCreator.me_CF6hUR.gif)

## asynchronous I
![www.GIFCreator.me_mtIJtE](/uploads/78f0030a4e830d6ab7cf3910dd01f9a1/www.GIFCreator.me_mtIJtE.gif)

## asynchronous II
![www.GIFCreator.me_UU3q43](/uploads/12c7743570fd88e8c95ed26bfe7d08ac/www.GIFCreator.me_UU3q43.gif)

## asynchronous IV
![www.GIFCreator.me_UU3q43](/uploads/b8c367306031ed2814357f59a540f783/www.GIFCreator.me_UU3q43.gif)

![www.GIFCreator.me_vjHNc0](/uploads/fbf9a89f4a079306c5881aafebeb31e1/www.GIFCreator.me_vjHNc0.gif)

![www.GIFCreator.me_448cr9](/uploads/21146bab33da66bb663417b92317ffe5/www.GIFCreator.me_448cr9.gif)

![www.GIFCreator.me_FsVqLX](/uploads/44cbb171d94f0c91cfece948527c392d/www.GIFCreator.me_FsVqLX.gif)

## Render Queue I
![www.GIFCreator.me_xcjwOq](/uploads/6e1989f87e8e81dbac522170d15ecad6/www.GIFCreator.me_xcjwOq.gif)

## Render Queue II
![www.GIFCreator.me_xxkOsW](/uploads/59be802a9a473c34c5c1ae2bb6b0467c/www.GIFCreator.me_xxkOsW.gif)



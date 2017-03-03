* [Clean code](best-practices#clean-code)
 * [Variables](best-practices#variables)
 * [Functions](best-practices#functions)
 * [Classes](best-practices#classes)
 * [Concurrency](best-practices#concurrency)
 * [Comments](best-practices#comments)
* [References](best-practices#references)

# Clean code

This is not a style guide. It's a guide to producing readable, reusable, and refactorable software in JavaScript.

Not every principle herein has to be strictly followed, and even fewer will be universally agreed upon. These are guidelines and nothing more, but they are ones codified over many years of collective experience.

When software architecture is as old as architecture itself, maybe then we will have harder rules to follow. For now, let these guidelines serve as a touchstone by which to assess the quality of the JavaScript code that you and your team produce.

One more thing: knowing these won't immediately make you a better software developer, and working with them for many years doesn't mean you won't make mistakes. Every piece of code starts as a first draft, like wet clay getting shaped into its final form. Finally, we chisel away the imperfections when we review it with our peers. Don't beat yourself up for first drafts that need improvement. Beat up the code instead!

## Variables

### Use meaningful and pronounceable variable names

**Bad:**
```javascript
var yyyymmdstr = moment().format('YYYY/MM/DD');
```

**Good:**
```javascript
var currentDate = moment().format('YYYY/MM/DD');
```

### Use the same vocabulary for the same type of variable

**Bad:**
```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**Good:**
```javascript
getUser();
```

### Use searchable names
We will read more code than we will ever write. It's important that the code we
do write is readable and searchable. By *not* naming variables that end up
being meaningful for understanding our program, we hurt our readers.
Make your names searchable.

**Bad:**
```javascript
// What the heck is 86400000 for?
setTimeout(blastOff, 86400000);
```

**Good:**
```javascript
// Declare them as capitalized `var` globals.
var MILLISECONDS_IN_A_DAY = 86400000;
setTimeout(blastOff, MILLISECONDS_IN_A_DAY);
```

### Use default arguments instead of short circuiting or conditionals
Default arguments are often cleaner than short circuiting. Be aware that if you
use them, your function will only provide default values for `undefined`
arguments. Other "falsy" values such as `''`, `""`, `false`, `null`, `0`, and
`NaN`, will not be replaced by a default value.

**Bad:**
```javascript
function createMicrobrewery(name) {
  var breweryName = name || 'Hipster Brew Co.';
  // ...
}
```

**Good:**
```javascript
function defaultFor(arg, val) {
  return typeof arg !== 'undefined' ? arg : val;
}

function createMicrobrewery(name) {
  var breweryName = defaultFor(name, 'Hipster Brew Co.')
  // ...
}
```

### Avoid Mental Mapping
Explicit is better than implicit.

**Bad:**
```javascript
var locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach(function doLotsOfStuff(l) {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // Wait, what is `l` for again?
  dispatch(l);
});
```

**Good:**
```javascript
var locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach(function doLotsOfStuff(location) {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});
```

### Don't add unneeded context
If your class/object name tells you something, don't repeat that in your
variable name.

**Bad:**
```javascript
var Car = {
  carMake: 'Honda',
  carModel: 'Accord',
  carColor: 'Blue'
};

function paintCar(car) {
  car.carColor = 'Red';
}
```

**Good:**
```javascript
var Car = {
  make: 'Honda',
  model: 'Accord',
  color: 'Blue'
};

function paintCar(car) {
  car.color = 'Red';
}
```

## Functions
### Function arguments (2 or fewer ideally)
Limiting the amount of function parameters is incredibly important because it
makes testing your function easier. Having more than three leads to a
combinatorial explosion where you have to test tons of different cases with
each separate argument.

One or two arguments is the ideal case, and three should be avoided if possible.
Anything more than that should be consolidated. Usually, if you have
more than two arguments then your function is trying to do too much. In cases
where it's not, most of the time a higher-level object will suffice as an
argument.

Since JavaScript allows you to make objects on the fly, without a lot of class
boilerplate, you can use an object if you are finding yourself needing a
lot of arguments.

**Bad:**
```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}
```

**Good:**
```javascript
function createMenu(options) {
  // ...
}

var options = {
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
};
createMenu(options);
```

### Functions should do one thing
This is by far the most important rule in software engineering. When functions
do more than one thing, they are harder to compose, test, and reason about.
When you can isolate a function to just one action, they can be refactored
easily and your code will read much cleaner. If you take nothing else away from
this guide other than this, you'll be ahead of many developers.

**Bad:**
```javascript
function emailClients(clients) {
  clients.forEach(function emailClient(client) {
    var clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Good:**
```javascript
function emailClients(clients) {
  clients
    .filter(isClientActive)
    .forEach(email);
}

function isClientActive(client) {
  var clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```

### Function names should say what they do

**Bad:**
```javascript
function addToDate(date, month) {
  // ...
}

var date = new Date();

// It's hard to to tell from the function name what is added
addToDate(date, 1);
```

**Good:**
```javascript
function addMonthToDate(month, date) {
  // ...
}

var date = new Date();
addMonthToDate(1, date);
```

### Remove duplicate code
Do your absolute best to avoid duplicate code. Duplicate code is bad because it
means that there's more than one place to alter something if you need to change
some logic.

Imagine if you run a restaurant and you keep track of your inventory: all your
tomatoes, onions, garlic, spices, etc. If you have multiple lists that
you keep this on, then all have to be updated when you serve a dish with
tomatoes in them. If you only have one list, there's only one place to update!

Oftentimes you have duplicate code because you have two or more slightly
different things, that share a lot in common, but their differences force you
to have two or more separate functions that do much of the same things. Removing
duplicate code means creating an abstraction that can handle this set of
different things with just one function/module.

Getting the abstraction right is critical. Bad abstractions can be
worse than duplicate code, so be careful! Having said this, if you can make
a good abstraction, do it! Don't repeat yourself, otherwise you'll find yourself
updating multiple places anytime you want to change one thing.

**Bad:**
```javascript
function showDeveloperList(developers) {
  developers.forEach(function showDeveloper(developer) {
    var expectedSalary = developer.calculateExpectedSalary();
    var experience = developer.getExperience();
    var githubLink = developer.getGithubLink();
    var data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach(function showDeveloper(manager) {
    var expectedSalary = manager.calculateExpectedSalary();
    var experience = manager.getExperience();
    var portfolio = manager.getMBAProjects();
    var data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

**Good:**
```javascript
function showEmployeeList(employees) {
  employees.forEach(function showEmployee(employee) {
    var expectedSalary = employee.calculateExpectedSalary();
    var experience = employee.getExperience();

    var portfolio = employee.getGithubLink();

    if (employee.type === 'manager') {
      portfolio = employee.getMBAProjects();
    }

    var data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

### Don't use flags as function parameters
Flags tell your user that this function does more than one thing. Functions should do one thing. Split out your functions if they are following different code paths based on a boolean.

**Bad:**
```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Good:**
```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```

### Avoid Side Effects (part 1)
A function produces a side effect if it does anything other than take a value in
and return another value or values. A side effect could be writing to a file,
modifying some global variable, or accidentally wiring all your money to a
stranger.

Now, you do need to have side effects in a program on occasion. Like the previous
example, you might need to write to a file. What you want to do is to
centralize where you are doing this. Don't have several functions that write to a particular file. Have one service that does it. One and only one.

The main point is to avoid common pitfalls like sharing state between objects
without any structure, using mutable data types that can be written to by anything,
and not centralizing where your side effects occur. If you can do this, you will
be happier than the vast majority of other programmers.

**Bad:**
```javascript
// Global variable referenced by following function.
// If we had another function that used this name, now it'd be an array and it could break it.
var name = 'Ryan McDermott';

function splitIntoFirstAndLastName() {
  name = name.split(' ');
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**Good:**
```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(' ');
}

var name = 'Ryan McDermott';
var newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```

### Avoid Side Effects (part 2)
In JavaScript, primitives are passed by value and objects/arrays are passed by
reference. In the case of objects and arrays, if your function makes a change
in a shopping cart array, for example, by adding an item to purchase,
then any other function that uses that `cart` array will be affected by this
addition. That may be great, however it can be bad too. Let's imagine a bad
situation:

The user clicks the "Purchase", button which calls a `purchase` function that
spawns a network request and sends the `cart` array to the server. Because
of a bad network connection, the `purchase` function has to keep retrying the
request. Now, what if in the meantime the user accidentally clicks "Add to Cart"
button on an item they don't actually want before the network request begins?
If that happens and the network request begins, then that purchase function
will send the accidentally added item because it has a reference to a shopping
cart array that the `addItemToCart` function modified by adding an unwanted
item.

A great solution would be for the `addItemToCart` to always clone the `cart`,
edit it, and return the clone. This ensures that no other functions that are
holding onto a reference of the shopping cart will be affected by any changes.

Two caveats to mention to this approach:
  1. There might be cases where you actually want to modify the input object,
but when you adopt this programming practice you will find that those case
are pretty rare. Most things can be refactored to have no side effects!

  2. Cloning big objects can be very expensive in terms of performance. 

**Bad:**
```javascript
function addItemToCart(cart, item) {
  cart.push({ item, date: Date.now() });
};
```

**Good:**
```javascript
function addItemToCart(cart, item) {
  return [].concat(cart, { item, date : Date.now() });
};
```

### Favor functional programming over imperative programming
JavaScript isn't a functional language in the way that Haskell is, but it has
a functional flavor to it. Functional languages are cleaner and easier to test.
Favor this style of programming when you can.

**Bad:**
```javascript
var programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

var totalOutput = 0;

for (var i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Good:**
```javascript
var programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

var INITIAL_VALUE = 0;

var totalOutput = programmerOutput
  .map(function getLinesOfCode(programmer) { return programmer.linesOfCode; })
  .reduce(function sumLinesOfCode(acc, linesOfCode) { return acc + linesOfCode, INITIAL_VALUE; });
```

### Encapsulate conditionals

**Bad:**
```javascript
if (fsm.state === 'fetching' && isEmpty(listNode)) {
  // ...
}
```

**Good:**
```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === 'fetching' && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```

### Avoid negative conditionals

**Bad:**
```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**Good:**
```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```

### Don't over-optimize
Modern browsers do a lot of optimization under-the-hood at runtime. A lot of
times, if you are optimizing then you are just wasting your time. [There are good
resources](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)
for seeing where optimization is lacking. Target those in the meantime, until
they are fixed if they can be.

**Bad:**
```javascript

// On old browsers, each iteration with uncached `list.length` would be costly
// because of `list.length` recomputation. In modern browsers, this is optimized.
for (var i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Good:**
```javascript
for (var i = 0; i < list.length; i++) {
  // ...
}
```

### Remove dead code
Dead code is just as bad as duplicate code. There's no reason to keep it in
your codebase. If it's not being called, get rid of it! It will still be safe
in your version history if you still need it.

**Bad:**
```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

var req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');

```

**Good:**
```javascript
function newRequestModule(url) {
  // ...
}

var req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');
```

## Classes
### Use method chaining
This pattern is very useful in JavaScript and you see it in many libraries such
as jQuery and Lodash. It allows your code to be expressive, and less verbose.
For that reason, I say, use method chaining and take a look at how clean your code
will be. In your class functions, simply return `this` at the end of every function,
and you can chain further class methods onto it.

**Bad:**
```javascript
function Car() {
  this.make = 'Honda';
  this.model = 'Accord';
  this.color = 'white';
}

Car.prototype.setMake = function setMake(make) {
  this.make = make;
}

Car.prototype.setModel = function setModel(model) {
  this.model = model;
}

Car.prototype.setColor = function setColor(color) {
  this.color = color;
}

Car.prototype.save = function save() {
  console.log(this.make, this.model, this.color);
}

var car = new Car();
car.setColor('pink');
car.setMake('Ford');
car.setModel('F-150');
car.save();
```

**Good:**
```javascript
function Car() {
  this.make = 'Honda';
  this.model = 'Accord';
  this.color = 'white';
}

Car.prototype.setMake = function setMake(make) {
  this.make = make;
  // NOTE: Returning this for chaining
  return this;
}

Car.prototype.setModel = function setModel(model) {
  this.model = model;
  // NOTE: Returning this for chaining
  return this;
}

Car.prototype.setColor = function setColor(color) {
  this.color = color;
  // NOTE: Returning this for chaining
  return this;
}

Car.prototype.save = function save() {
  console.log(this.make, this.model, this.color);
  // NOTE: Returning this for chaining
  return this;
}

var car = new Car()
  .setColor('pink')
  .setMake('Ford')
  .setModel('F-150')
  .save();
```

### Prefer composition over inheritance
As stated famously in [*Design Patterns*](https://en.wikipedia.org/wiki/Design_Patterns) by the Gang of Four,
you should prefer composition over inheritance where you can. There are lots of
good reasons to use inheritance and lots of good reasons to use composition.
The main point for this maxim is that if your mind instinctively goes for
inheritance, try to think if composition could model your problem better. In some
cases it can.

**Bad:**
```javascript
Robot
 .drive()
 
    CleaningRobot
     .clean()
 
    MurderRobot
     .kill()

Animal
 .poop()
 
    Dog
     .bark()
 
    Cat
     .meow()

// Our costumer demands a robot murder dog! 
// The robot murder dog can kill, drive and bark but it doesn't have a digestive system, so it cannot poop.
```

**Good:**
```javascript
function barker(state) {
  function bark() {
    console.log('Woof, I am ' + state.name);
  }
  return {
    bark: bark;
  }
}

function driver(state) { //... }
function killer(state) { //... }

function murderRobotDog(name) {
  var state = {
    name,
    speed: 100,
    position: 0
  };
  return Object.assign(
    {},
    barker(state),
    driver(state),
    killer(state)
  )
}
var max =  murderRobotDog('Max')
max.bark() // 'Woof, I am Max'
```

## Concurrency
### Use Promises, not callbacks
Callbacks aren't clean, and they cause excessive amounts of nesting.

**Bad:**
```javascript
require('request').get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin', function handleRequest(requestErr, response) {
  if (requestErr) {
    console.error(requestErr);
  } else {
    require('fs').writeFile('article.html', response.body, function handleWrite(writeErr) {
      if (writeErr) {
        console.error(writeErr);
      } else {
        console.log('File written');
      }
    });
  }
});
```

**Good:**
```javascript
var Promise = require('es6-promise').Promise;

require('request-promise').get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
  .then(function handleRequest(response) {
    return require('fs-promise').writeFile('article.html', response);
  })
  .then(function handleWrite() {
    console.log('File written');
  })
  .catch(function handleError(err) {
    console.error(err);
  });

```

## Comments
### Only comment things that have business logic complexity.
Comments are an apology, not a requirement. Good code *mostly* documents itself.

**Bad:**
```javascript
function hashIt(data) {
  // The hash
  var hash = 0;

  // Length of string
  var length = data.length;

  // Loop through every character in data
  for (var i = 0; i < length; i++) {
    // Get character code.
    var char = data.charCodeAt(i);
    // Make the hash
    hash = ((hash << 5) - hash) + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**Good:**
```javascript

function hashIt(data) {
  var hash = 0;
  var length = data.length;

  for (var i = 0; i < length; i++) {
    var char = data.charCodeAt(i);
    hash = ((hash << 5) - hash) + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}

```

### Don't leave commented out code in your codebase
Version control exists for a reason. Leave old code in your history.

**Bad:**
```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Good:**
```javascript
doStuff();
```

### Don't have journal comments
Remember, use version control! There's no need for dead code, commented code,
and especially journal comments. Use `git log` to get history!

**Bad:**
```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**Good:**
```javascript
function combine(a, b) {
  return a + b;
}
```

### Avoid positional markers
They usually just add noise. Let the functions and variable names along with the
proper indentation and formatting give the visual structure to your code.

**Bad:**
```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: 'foo',
  nav: 'bar'
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
var actions = function() {
  // ...
};
```

**Good:**
```javascript
$scope.model = {
  menu: 'foo',
  nav: 'bar'
};

var actions = function() {
  // ...
};
```

# References
[Clean Code](https://github.com/ryanmcdermott/clean-code-javascript)

[Composition over Inheritance](https://www.youtube.com/watch?v=wfMtDGfHWpA)
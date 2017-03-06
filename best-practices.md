* [Clean code](best-practices#clean-code)
 * [Variables](best-practices#variables)
 * [Functions](best-practices#functions)
 * [Classes](best-practices#classes)
 * [Concurrency](best-practices#concurrency)
 * [Comments](best-practices#comments)
* [Test-Driven Development](best-practices#test-driven-development)
 * [Process](best-practices#process)
 * [Benefits](best-practices#benefits)
* [Code review](best-practices#code-review)
 * [Knowledge sharing](best-practices#knowledge-sharing)
 * [Better estimates](best-practices#better-estimates)
 * [Takes time](best-practices#takes-time)
* [Style guide](best-practices#style-guide)
* [Linter](best-practices#linter)
* [Compiler](best-practices#compiler)
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
    var portfolio = employee.type === 'developer' ? employee.getGithubLink() : employee.getMBAProjects();
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

var req = newRequestModule('www.inventory-awesome.io');
```

**Good:**
```javascript
function newRequestModule(url) {
  // ...
}

var req = newRequestModule('www.inventory-awesome.io');
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

# Test-Driven Development
Test-driven development (TDD) is an advanced technique of using automated unit tests to drive the design of software and force decoupling of dependencies. The result of using this practice is a comprehensive suite of unit tests that can be run at any time to provide feedback that the software is still working.

## Process
The motto of test-driven development is "Red, Green, Refactor."
* Red: Create a test and make it fail.
* Green: Make the test pass by any means necessary.
* Refactor: Change the code to remove duplication in your project and to improve the design while ensuring that all tests still pass.
![red-green-refactorFINAL2](/uploads/4bd32f005002cfed5b16deea99eb0c4e/red-green-refactorFINAL2.png)

## Benefits
* The suite of unit tests provides constant feedback that each component is still working.
* The unit tests act as documentation that cannot go out-of-date, unlike separate documentation, which can and frequently does.
* When the test passes and the production code is refactored to remove duplication, it is clear that the code is finished, and the developer can move on to a new test.
* Test-driven development forces critical analysis and design because the developer cannot create the production code without truly understanding what the desired result should be and how to test it.
* The software tends to be better designed, that is, loosely coupled and easily maintainable, because the developer is free to make design decisions and refactor at any time with confidence that the software is still working. This confidence is gained by running the tests. The need for a design pattern may emerge, and the code can be changed at that time.
* The test suite acts as a regression safety net on bugs: If a bug is found, the developer should create a test to reveal the bug and then modify the production code so that the bug goes away and all other tests still pass. On each successive test run, all previous bug fixes are verified.
* Reduced debugging time!

# Code review
![687474703a2f2f7777772e6f736e6577732e636f6d2f696d616765732f636f6d6963732f7774666d2e6a7067](/uploads/962ebb6c7e3437b65d384b1f43ccadda/687474703a2f2f7777772e6f736e6577732e636f6d2f696d616765732f636f6d6963732f7774666d2e6a7067.jpg)

When a developer is finished working on an issue, another developer looks over the code and considers questions like:

*  Are there any obvious logic errors in the code?
* Looking at the requirements, are all cases fully implemented?
* Are the new automated tests sufficient for the new code? Do existing automated tests need to be rewritten to account for changes in the code?
* Does the new code conform to existing style guidelines?

Code reviews should integrate with a team’s existing process. For example, if a team is using task branching workflows, initiate a code review after all the code has been written and automated tests have been run and passed–but before the code is merged upstream. This ensures the code reviewer’s time is spent checking for things machines miss, and prevents poor coding decisions from polluting the main line of development. 

## Knowledge sharing
No one is the only person who knows a specific part of the code base. Simply put, code reviews help facilitate knowledge sharing across the code base and across the team. 

> As code reviews expose developers to new ideas and technologies, they write better and better code. 

## Better estimates
Estimation is a team exercise, and the team makes better estimates as product knowledge is spread across the team. As new features are added to the existing code, the original developer can provide good feedback and estimation. In addition, any code reviewer is also exposed to the complexity, known issues, and concerns of that area of the code base. The code reviewer, then, shares in the knowledge of the original developer of that part of the code base. This practice creates multiple, informed inputs which, when used for a final estimate always makes that estimate stronger and reliable.

## Takes time
Sure, they take time. But that time isn't wasted–far from it.

> When done right, code reviews actually save time in the long run.

Here are three ways to optimize for that. 

### Share the load
When an author selects reviewers, they cast a wide net across the team. Any engineers can give input. This decentralizes the process so that no one is a bottleneck, and ensures good coverage for code review across the team.

### Review before merging
Requiring code review before merging upstream ensures that no code gets in unreviewed. Which means that the questionable architectural decisions made at 2am and the improper use of a factory pattern by the intern are caught before they have a chance to make a lasting (and regrettable) impact on your application.

### Use peer pressure to your advantage
When developers know their code will be reviewed by a teammate, they make an extra effort to ensure that all tests are passing and the code is as well-designed as they can make it so the review will go smoothly. That mindfulness also tends to make the coding process itself go smoother and, ultimately, faster.

Don’t wait for a code review if feedback is needed earlier in the development cycle. Feedback early and often makes for better code, so don't be shy about involving others–whenever that may be. It'll make your work better, but it also makes your teammates better code reviewers. And the virtuous cycle continues....!

# Style guide
![1-9nMBMt-OugnruBr_M-WuEQ](/uploads/0227a609241bf877374a5dca0b18dea2/1-9nMBMt-OugnruBr_M-WuEQ.png)

The most important thing, no matter what your preferred [javascript style](https://github.com/airbnb/javascript) is, is to be consistent when working with a team or a large codebase that will have to be maintained in the future.

# Linter
Code linting is a type of static analysis that is frequently used to find problematic patterns or code that doesn’t adhere to certain style guidelines. There are code linters for most programming languages, and compilers sometimes incorporate linting into the compilation process.

JavaScript, being a dynamic and loosely-typed language, is especially prone to developer error. Without the benefit of a compilation process, JavaScript code is typically executed in order to find syntax or other errors. Linting tools like [ESLint](http://eslint.org) allow developers to discover problems with their JavaScript code without executing it.

# Compiler
[Babel](https://babeljs.io) is essentially an ECMAScript 6 to ECMAScript 5 compiler. It allows you to use ES6 features in your projects and then compiles ES5 for you to use in production.

> Use next generation JavaScript, today.

# References
[Clean Code](https://github.com/ryanmcdermott/clean-code-javascript)

[Composition over Inheritance](https://www.youtube.com/watch?v=wfMtDGfHWpA)

[Code reviews](https://www.atlassian.com/agile/code-reviews)

[Guidelines for Test-Driven Development](https://msdn.microsoft.com/en-us/library/aa730844(v=vs.80).aspx)
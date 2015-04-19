### `var`, `let`, `const`

There is a cottage industry of people who right write on JavaScript's oddities. These books are typically written with the best of intentions, but they tend to just confuse people new to the language and convince them that they'll never be able to learn this sorcery known as JavaScript.

One of the popular topics is this idea of variable hoisting. The interpreter runs through your code and grabs all of the variables you define and hoists them up to the top of the function scope. It just sets them as undefined. This can be a little confusing because it doesn't throw an error if you haven't gotten around to defining it yet. It just returns undefined—which means it can fail silently. Silent failures are the worst failures.

Let's compare:

```rb
puts x
x = 2
```

Kaboom—and for good reason. On the other hand.

```js
console.log(x);
var x = 2;
```

This just quietly logs `undefined` and moves along.

One of the things people tend to hate most about JavaScript is that it stays pretty quiet about unexpected weird shit going wrong—and this is a good example of that gripe.

Hmm. What if we could make JavaScript behave more like Ruby.

```js
console.log(x);
let x = 2;
```

Now we get something similar to Ruby: `ReferenceError: x is not defined`.

`let` also keeps things where they belong.

```js
for (var i = 0; i < 10; i++) {
  // whatever
}

console.log(i); // 10, gross.

for (let j = 0; j < 10; j++) {
  // whatever
}

console.log(j); // ReferenceError.
```

As a real developer, sometimes, I want to make an array of functions that log out a value between one and ten.

```js
commands = [];

for (var i = 0; i < 10; i++) {
  commands.push(function (i) {
    console.log(i);
  });
}

commands.forEach(function (logger) {
  logger();
});
```

Sweet. Let's take it for a spin. (It's all `10`s.) Whoops. They're all referencing the same variable—and that variable ended up as ten.

Now, that we're living in the future, what if we try that with _future JavaScript_.

```js
commands = [];

for (let i = 0; i < 10; i++) {
  commands.push(function () {
    console.log(i);
  });
}

commands.forEach(function (logger) {
  logger();
});
```

Ah! It does what we think it out to have done because that let is scoped to the block itself. The world is now a better place.

### Arrow Functions: Implicit Returning

```js
[1,2,3].map(function (n) {
  n * 2;
}); // returns [undefined, undefined, undefined]

// ES6 Solution

[1,2,3].map( n => n * 2 ); // returns [2,4,6]
```

### Arrow Functions: Binding

```js
var person = {
  name: 'Steve',
  updateName: somethingAsynchronous(function () { this.name = 'Wes' })
};

person.updateName();

person.name === 'Steve'
name === 'Wes' // Whoops! We actually set a global variable.
```

Hmm… We can use arrow functions again here to make things better.


```js
var person = {
  name: 'Steve',
  sayHello: function () {
    somethingAsynchronous(() => { this.name = 'Wes' });
  }
};
```

That works as expected. But you may be saying to yourself: It's weird that we're mixing and matching Object syntaxes, right? Fair. You might be tempted to try something like this:

```js
var person = {
  name: 'Steve',
  sayHello: () => {
    somethingAsynchronous(() => { this.name = 'Wes' });
  }
};
```

It's not going to work. Sorry.

That sad, we can use enhanced object literals to make things little cleaner.

```js
var person = {
  name: 'Steve',
  sayHello() { somethingAsynchronous(() => { this.name = 'Wes' }) }
};
```

### Template Strings

It's a scientific fact that after Hacker News, nothing makes programmers angrier than string concatenation. Families have been ruined over a missing plus sign. It's especially painful when we're trying put together

```js
var post = {
  title: 'JavaScript: Not So Bad Anymore Says Rails Community',
  datePublished: 'Thursday, April 23, 2015'
  body: 'Lorem ipsum…'
};

$('<article><h2>' + post.title + '</h2><time>' + post.datePublished +
  '</time><p>' + post.body + '</p>').appendTo('#important-posts');
```

Versus.

```js
$(`<article><h2>${post.title}</h2><time>${post.datePublished}
   </time><p>${post.body}</p>`).appendTo('#important-posts');
```

Template strings also support new lines without concatenation—much like `%Q{}` in Ruby.

### Return of the Splat

JavaScript is pretty flexible about how many arguments you pass to a function. This leads to some really elegant APIs, like those found in jQuery. Regardless of how many arguments you say a function takes, all of the arguments you hand it will be stored in a `arguments` collection.

```js
let logArguments = function () {
  console.log(arguments);
};

logArguments(1,2,3) // Logs [1,2,3]
```

So, that's pretty cool. I didn't get a `ArgumentError: wrong number of arguments (3 for 0)`.

What does this return?

```js
let iterateArguments = function () {
  arguments.forEach(argument => console.log(argument));
};

iterateArguments(1,2,3);
```

Oh snap, it throws an `Uncaught TypeError: arguments.forEach is not a function`.

What the fuck?

It turns out that `arguments` isn't an array, it's like array-like—and being array-like means you don't inherit from `Array.prototype` and not inheriting from `Array.prototype`, means you don't have access to `Array.prototype.forEach`.

Typically, this has been handled by basically going over to `Array.prototype`, stealing it's `forEach` method and calling it with our bullshit array-like thing.

```js
function iterateArguments() {
  Array.prototype.forEach.call(arguments, function (argument) {
    console.log(argument);
  });
}

iterateArguments(1,2,3);
```

Alternatively, we could grab `Array.prototype.slice` and turn little Pinocchio over hear into a real array and go about our lives as normal.

```js
function iterateArguments() {
  var args = Array.prototype.slice.call(arguments);
  args.forEach(function (argument) {
    console.log(argument);
  });
}

iterateArguments(1,2,3);
```

It works. It's a hack, but it works. And, it's how we all have been going about our lives on the client-side.

ES6 gives us a spread operator a lot like the splat operator in Ruby. The splat operator is a real-deal array. No more monkey business with `Array.prototype`.

Now, we can get rid of all that silliness from earlier and work with our arguments like adults.

```js
let iterateArguments = function (...args) {
  args.forEach(argument => console.log(argument));
};

iterateArguments(1,2,3);
```

In what should come as no surprise, you can use have some arguments that come before it.

```js
let iterateArguments = (x, ...args) => {
  console.log('x is:', x);
  args.forEach(argument => console.log(argument));
};
```

Like Ruby's splats, it works the other way as well. Previously, if we had an array of arguments and we wanted to send them to a function, we had to do some black magic, like this:

```js
iterateArguments.apply(null, [1,2,3]);
```

In the future (also known as today), we'll be able to do this.

```js
iterateArguments(...[1,2,3]);
```

For Rubyists, it's pretty easy to see why this is useful, we've enjoyed the benefits of the splat operator for years. But as an added bonus, we can also pull just the properties we want from a given object.

You've probably seen something like this in JavaScript (and if you've been programming in Ruby long enough, you might have seen it there as well):

```js
function introducePerson(options) {
  opts = options || {};
  var name = opts.name;
  var age = opts.age;

  return 'Hello, my name is ' + name + ' and I am ' + age + ' years old.';
}
```

Going forward, you'll be able to cherry pick just the properties you want and then have them automatically assigned to local variables inside of the function.

```js
function introducePerson({ name, age }) {
  return `Hello, my name is ${name} and I am ${age} years old.`
}
```

It definitely seems like there is a lot of new syntax to learn, but I believe that the small amount of new syntax will make the language a lot more approachable than a series of weird tricks for turning `arguments` into a real array or pull out properties from an object. And, if you don't like it, then you don't have to use it. That's the benefit of the language being incredibly backwards compatible.

Lastly, JavaScript now supports default arguments. This means that we can refactor…

```js
function increment(base, step) {
  step = step || 1;
  return base + step;
}
```

…to…

```js
function increment(base, step = 1) {
  return base + step;
}
```

Fixing JavaScript, one small feature at a time.

### Classes and Enhanced Object Literals

There is an old saying in JavaScript that you can apparently build classical inheritance with prototypal inheritance, but you can't build prototypal inheritance with classical inheritance. For years, this meant absolutely nothing to me.

In JavaScript, we typically create objects with a constructor function. By convention—it's not a hard and fast requirement of the language—we usually use a capital letter when we name a constructor function.

```js
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}

var person = Person('Steve', 'Kinney');
```

Pop quiz: in the code sample above, what's the value of `person.firstName`?

It turns out that calling `person.firstName` throws a `TypeError`. Why? Well, just because we meant to use it as a constructor function, we didn't. We just called it as a regular function, and in that case, `this` is the current scope—which is the global scope right now. So, not only did we not construct a person object, we also polluted the global namespace with a `firstName` and a `lastName` variable.

The correct way to construct an object is the use the `new` operator.

```js
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}

var person = new Person('Steve', 'Kinney');
person.firstName // returns 'Steve'
person.lastName // returns 'Kinney'
```

So, what about methods? Well. We could define them as additional properties on the object we're constructing, but then we're duplicating functionality for every object we instantiate. If we create 1,000 people, we'll have 1,000 different `fullName` methods. The solution is to define them on the shared prototype of the new object.

```js
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}

Person.prototype.fullName = function () {
  return this.firstName + ' ' + this.lastName;
}

var person = new Person('Steve', 'Kinney');
```

Now, when the `Person` constructor builds a new object. It sets the prototype of the resulting object to `Person.prototype`. When we call `person.fullName()`, the instance doesn't have a `fullName` property, so it calls up to it's prototype—which does in fact have a `fullName` property—and we call the resulting function.

ECMAScript classes are just sweet, sweet syntactic sugar over the prototypal inheritance of the JavaScript of old.

```js
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  fullName() {
    return `${this.firstName} ${this.lastName}`
  }
}
```

We use a `constructor` method—which is similar to `initalize` in Ruby to, umm, construct our object. We can then sketch out our methods in the same place as we define the rest of the class, which is nice. Under the hood, everything is being wired up as it should, but we can just go about our business as usual.

There is also another nice little perk: using the class syntax will also protect use from accidentally polluting the global namespace. Just calling `Person(firstName, lastName)` will throw an error because the runtime is able to tell that this is not just a normal function.

Not classical inheritance system would be complete with—uh—inheritance.

```js
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  fullName() {
    return `${this.firstName} ${this.lastName}`
  }
}

class Developer extends Person {
  constructor(firstName, lastName, favoriteLanguage) {
    super(firstName, lastName);
    this.favoriteLanguage = favoriteLanguage;
  }

  fullName() {
    return `${super.fullName()}, ${this.favoriteLanguage} developer`
  }
}

let d = new Developer('Steve', 'Kinney', 'JavaScript');
developer.fullName();
```

Okay, let's talk for a minute about what's going on here. In ES classes, we have to call `super` in our constructor if it inherits from another class. We can also reference the superclass and it's methods at anytime using `super`.

### ECMAScript Modules

ES6 Modules can be a little confusing to Rubyists because it works a little differently than Ruby. In Ruby, when we require a file, we pull all of the classes, constants, and modules into the global scope. With JavaScript modules, we have to be specific about what we want to let out of a module, and what functionality we want to import when we load the module—everything else is wrapped in a closure.

JavaScript has never officially supported modules, but there have been some specifications that have enjoyed some popularity in recent years.

* Common.js is typically used in Node
* AMD (asynchronous module dependencies) has been a popular option in the browser.

The ECMAScript standard defines an alternative syntax.

Let's start with the very basics of importing a single function or object.

```js
// lib/super-high-level-math.js
export function sum(..addends) {
  return addends.reduce( (sum, add) => sum + add );
}

//app.js
import { sum } from 'lib/super-high-level-math';

sum(2, 2); // returns 4, shockingly
```

So, `export default` this is what we'd like to export if no other specifics are given. If you've ever used Node before, it's the equivalent to `module.exports = sum;`.

That's pretty cool, if your module is only exporting one piece of functionality. But what if you wanted a few things out of that module? That's totally doable.

```js
// lib/super-high-level-math.js
export function sum(..addends) {
  return addends.reduce( (sum, add) => sum + add );
}

export function square(n) {
  return n * n;
}

//app.js
import { sum, square } from 'lib/super-high-level-math';

sum(2, 2); // returns 4, shockingly
square(2); // also 4, amazingly
```

Maybe you just want all the things? You can use an asterisk to say you want all of the exported functionality and then hand it a name for an object to tack the properties on.

```js
import * as mathyMath from 'lib/super-high-level-math';

mathyMath.sum(2, 2); // returns 4, shockingly
mathyMath.square(2); // also 4, amazingly
```

Sometimes, you just want a sensible default.

```js
// lib/addition-is-awesome.js
export default function (x) {
  return x + 2;
}

// app.js
import addTwo from 'lib/addtion-is-awesome.js'

addTwo(2); // Can you even believe this returns 4?
```

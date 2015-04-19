* JavaScript is the colloquial name for a language called ECMAScript
	* ECMAScript 1: 1997
	* ECMAScript 2: 1998
	* ECMAScript 3: 1999 (regular expressions, better string handling, new control statements, try/catch exception handling, tighter definition of errors, formatting for numeric output and other enhancements)
	* ECMAScript 4: Abandoned
	* ECMAScript 5: 2009
* Show a brief timeline of the versions of JavaScript
	* Note that ES4 is completely missing
	* Note the long gap between versions
  * ES6, ES7, Harmony, ES.next
* ECMAScript is now on a yearly release cycle
  * Pulling from the harmony bucket
* There are a few libraries for converting
  * Babel, Tracuer
  * You can't get all of the features (i.e. proxies), but you can get a good set of them
  * Bringing the future into your Rails application: `sprockets-es6`
  * Installation issues:
    * Requires `sprockets 3.0.0.beta.3`
    * If you're working with a vanilla `rails new installation
  * We can now use the `.es6` suffix to spring the Asset Pipeline into action
* `var`, `let`, and `const`
* Arrow Functions
  * No more `var self = this`
  * No more `.bind(this)`
  * Say hello to `() => {}`
  * Potential pitfalls
* Template Strings
  * DOM creation in JavaScript sucks
  * Ugh to string concatenation
  * Yay for string interpolation
  * Yay for multiline strings
  * A nice replacement for EJS or Underscore's template strings
* ES6 Modules
  * Rely less on the asset pipeline and more on asynchronous loading
* Classes
  * You can do classical inheritance with prototypal inheritance, supposedly
  * Syntactic sugar
  * Subclassable Built-ins
* Tail Class
* Symbols
* Reflect API
* Promises

http://davidwalsh.name/for-and-against-let
https://hackhands.com/fast-rich-client-rails-development-webpack-es6-transpiler/
http://www.2ality.com/2015/02/es6-classes-final.html
http://javascriptplayground.com/blog/2014/10/es6-introduction/
https://github.com/sstephenson/sprockets/pull/682

Transpilation ultimately means that the issue could have been solved with just a little bit of syntactic sugar. This is something that Ruby got really, really right from the very beginning.

Legend has it that you can build classical inheritance from prototypal inheritance, but you can't build prototypal inheritance from classical. This might be true. But it hasn't stopped most Rubyists from being utterly confused about what prototypal inheritance actually is.

ES6 gives us a way to define classes. Ultimately—yea—it's syntactic sugar, but it's sweet, sweet syntactic sugar.

Go into what happens if you blow it and call a constructor as a function.

Questions:

- What are the difference between `let` and `var`?
- How do JavaScript array comprehension compare to Python?
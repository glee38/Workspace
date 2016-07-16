#Javascript Basics

JavaScript is the de facto language of the internet. It's a tool that, once mastered, will allow you to interact with your users via your app in fun and interesting ways. It is a dynamically typed, prototypical, functional programming language. What that exactly means will become more apparent in lessons and through exposure to the language. But here are the basics.

####OBJECTIVES
+ Print JavaScript to the console

+ Explain proper use of semicolons and other syntax basics

+ Explain truthy and falsely values

###SYNTAX BASICS
####VARIABLE DECLARATION

To make a variable called `greeting` and set it equal to the string `"hello world"` in Ruby, we could just write:

```ruby
greeting = "hello world"
```

In JavaScript, to declare a local variable (which is almost always what you want to do), you add the keyword `var` before the variable name, like so:

```javascript
var greeting = "hello world";
```

Notice that semicolons are used after variable declaration and after printing information to the console.

####WHITESPACE
As with Ruby, JavaScript gives whitespace no meaning outside of quotation marks.

```javascript
var greeting =         "hello world";
 
greeting == "hello world" // Returns true
```

As in Ruby, tabs enhance readability, but have no special meaning.

```javascript
var tvShows = {
    "ABC": [
        "The Bachelorette",
        "Grey's Anatomy"
    ],
    "FOX": [
        "Bones",
        "Empire"
    ]
};
```

####SEMICOLONS
Semicolons *must* be used in some cases, *should* be used in others, and other times not at all.

###Necessary
Semicolons must be used when two statements are on the same line (this is called statement chaining). In Ruby, you probably didn't see too much statement chaining so here's an example:

```javascript
fox = "XOF NWORB KCIUQ EHT"
fox.reverse!; fox.downcase!
# => "the quick brown fox"
```

Notice how that semicolon separates the reversal from the down-casing? JavaScript, like Ruby, also requires semicolons when executing two statements on the same line:

```javascript
var fox = "XOF NWORB KCIUQ EHT";
fox = fox.split("").reverse().join(""); fox = fox.toLowerCase();
// => "the quick brown fox"
```

Of course, there are exceptions. For instance, when we cover loops, you may notice that the `for` loop abides by this rule for the first two statements in its argument but not for the last. Not to worry, its syntax will get covered more fully later in the curriculum.

###Ideal
From [Codecademy's blog on semicolons](https://www.codecademy.com/blog/78):

> As we saw above, the semicolon in JavaScript is used to separate statements.
However, it can be omitted if the statement is followed by a line break (or
there’s only one statement in a {block}). A statement is a piece of code that
tells the computer to do something. Here are the most common types of
statements:

```javascript
var i;                        // variable declaration
i = 5;                        // value assignment
i = i + 1;                    // value assignment
i++;                          // same as above
var x = 9;                    // declaration & assignment
console.log("hi");            // function call
```

> All of these statements can end with a ; but none of them must. However, it
is a good habit to terminate each statement with a ;.

###Avoid
Semicolons don't follow the closing curly brackets of `if`, `for`, `while`, and
`switch` blocks. For instance, this if statement has no ending semicolon:

```javascript
var lunchtime = true;
 
if (lunchtime) {
  console.log("Go get lunch!");
}
```

The above-mentioned cases are pretty easy to remember. It's a bit trickier to remember that semicolons follow **function expressions** but not **function declarations**.

```javascript
// function declaration (no semicolon)
function foo() {
    return 'foo';
}
 
// function expression (semicolon)
var bar = function() {
    return 'bar';
};
```

Also avoid putting semicolons between an ending parenthesis and a beginning curly bracket. In other words, you shouldn't write `) ; {`.

###Hints
Should you get stuck with your semicolon usage (and don't worry, even professional JavaScript developers occasionally forget what goes where), paste your code into a JSLinter, such as [JSLint](http://www.jslint.com/) or even [JSFiddle](http://jsfiddle.net/). If you click `Lint`, you'll see a line-by-line evaluation of your semicolon usage and other syntax mistakes.

###A slightly more heretical take on semicolons
Semicolons don't really matter in JavaScript. If we're working on a large JavaScript application, chances are we're at least using a [minifier](https://developers.google.com/speed/docs/insights/MinifyResources) that will insert semicolons as appropriate when it processes our code. And lately, it's becoming more popular to compile ECMAScript 6 to ES5 (the JavaScript that most browsers work in) using a tool like [Babel](https://babeljs.io/). [TypeScript](https://www.typescriptlang.org/) is also growing in popularity.

All of this is to say that the long-running semicolon debate in JavaScript is largely overblown at this point. Remember to use semicolons to separate statements that occur on a single line; otherwise, follow the conventions of the team.

####TRUTHY AND FALSEY VALUES
We make a lot of assumptions when we write computer programs. In order to make the right assumptions, we need to understand what values in our program evaluate to `truthy` values and which evaluate to `falsey` values.

Truthy values:

```javascript
'all non-empty strings';
 
'all number strings'; // for example '0', '5', '0.2', '-3.14'
 
[]; // an empty array
 
{}; // an empty object
 
1; // any non-zero number
```

Falsey values:

```javascript
0; // the number zero
 
'';  // an empty string
 
NaN;
 
null;
 
undefined;
```

####PRINTING TO THE CONSOLE
One of the first methods you probably learned in Ruby was `puts`. JavaScript has an equivalent function called `console.log()`. Let's take the Ruby code below and turn it into JavaScript code:

```ruby
puts "JavaScript is also known as ECMAScript"
```

Well let's change that `puts` into a `console.log`:

```javascript
console.log "JavaScript is also known as ECMAScript"
```

Now we'll wrap the string in parentheses:

```javascript
console.log("JavaScript is also known as ECMAScript")
```

And the icing on the top of this printing-string cake (yum!) is to add a semicolon at the end:

```javascript
console.log("JavaScript is also known as ECMAScript");
```

Will the code execute and work without the semicolon? Yes. Is the semicolon best practice? Yes. Should you use semicolons? Also yes.

Well, this code is all fine and dandy but how do you run it? The best, possibly most powerful thing about JavaScript is that all modern browsers know how to run it. All you need to do is make sure you're reading this on either Chrome or Firefox (trust us, the developer tools of these browsers are way better than on Safari). Then open up your browser's console. That's right, just like your computer knows about running a Ruby sandbox when you type `irb`, your browser runs a JavaScript sandbox when you open up the console.

To open up your browser's console from Chrome, type `command` + `option` + `J`. From Firefox, type `command` + `option` + `K`. (If you love writing directly into your terminal, go ahead and install [Node](http://blog.teamtreehouse.com/install-node-js-npm-mac). Then type `node` in your terminal and you'll be in a JavaScript sandbox.)

From your console (or Node terminal), simply type the code above into the prompt and hit `return`. There, you've run your first JavaScript code!

Open up Chrome and on any tab (even a blank one) right click and select `Inspect` from the dropdown. From there, select the `console` tab:

![console](https://s3.amazonaws.com/learn-verified/console.png)

The white space below is your console, a sandbox where you can enter and execute any JavaScript code you want.

![Executing JavaScript in Console](https://s3.amazonaws.com/learn-verified/exectuing-js-in-console.png)

##Intro To Variables in JS
####OBJECTIVES

+ Declare a variable without assigning a value
+ Declare and define a variable
+ multi-line variable assignment
+ Explain how local and global variables differ

####ABOUT
As in Ruby, variables in JavaScript are used to store data that will be used in our program. A variable can point to almost any type of value including numbers, strings, arrays, and hashes.

Just like Ruby, variables are assigned values using the `=` operator. Variable names are typically all lower case, and in the case of multiple words, the words are joined together using [lowerCamelCase](http://c2.com/cgi/wiki?LowerCamelCase).

####DECLARING VARIABLES
Lets say I have the variable `word`. In Ruby, to assign a value to this variable, we would simply do

```ruby
word = "hey"
```

Ruby would understand automatically that we're creating a new variable and assigning it a value. If this variable was created in a method, it would only exist in the scope of the method. If it was created in a block, it would only exist in the scope of that block. Later in the method or block when you used that variable, Ruby wouldn't think to look outside the block for the variable definition.

JavaScript variables operates a little differently, and scope in JavaScript operates a lot differently. JavaScript variables must be declared before they can be assigned a value. If you don't declare your variable, JavaScript will bubble up through layers of scope (up out of the function you defined your variable in), till it finds a declared variable with that name. This means you could end up using different values than you thought you were.

Go ahead and open up a Chrome or Firefox browser window, and open up the Developer Tools. Feel free to code along with these examples.

Declaring a variable without defining a value looks like this:

```javascript
var word;
```

Now try entering `word` in console. You should see `Undefined` because we never defined a value for this variable. Now I can assign `word` a value:

```javascript
word = "hey";
```

When you enter `word` you should see `hey`. This works, but feels pretty tedious. Thankfully we can declare and define a variable all on one line:

```javascript
var word = "hey";
```

####MULTI-LINE VARIABLE ASSIGNMENT
Let's say I needed to declare and define multiple variables. It feels like a lot to have to repeat `var` over and over again. JavaScript allows us to do multi-line variable assignment to alleviate this pain. Every variable must be separated with a comma, and end the entire line must end with a semicolon.

Let's condense the below code into one line:

```javascript
var a = 5;
var b = 2;
var c = 3;
var d = {};
var e = [];
```

The above is equivalent to:

```javascript
var a = 5,
    b = 2,
    c = 3,
    d = {},
    e = [];
```

which can be converted to:

```javascript
var a = 5, b = 2, c = 3, d = {}, e = [];
```

Try returning each variable in the console. You should see the appropriate values returned for each one.

Some people prefer to keep new lines between each new variable assignment, other people like the look of the single line. Whichever way you swing, the important thing to remember is [to use commas to separate each variable](http://stackoverflow.com/a/4166789/2890716).

####REASSIGNING VARIABLE VALUE
Changing the value of a variable in JavaScript works just in the same way as it does in Ruby:

```javascript
var word = "hey";
word;
// returns "hey"
word = "javascript";
word;
// returns "javascript";
```

####LOCAL VS. GLOBAL VARIABLES
Just like Ruby, JavaScript also has local and global variables. In Ruby, a program is written within the scope `Main`. The JavaScript equivalent is `window` (the browser window).

We'll dive much deeper into scope in JavaScript, but for all intents and purposes, a global variable is any variable defined within the `window`. You can also think of it as any variable that exists outside of a function (or method) is a global variable.

```javascript
var firstNum = 10; // is defined in the window and is thus accessible by the entire program
window.firstNum;
// returns 10
```

But if we declare a variable inside a function:

```javascript
function myFunk(){
  var funky = true;
}
 
myFunk();
window.funky;
// returns undefined because funky is variable defined inside a function and is thus a variable local to that function.
```

These consequences are due to JavaScript's approach to scoping. In Ruby, we didn't have to worry too much about scoping because all variables assigned within a method are scoped to just that method. For instance:

```ruby
def make_variable
  cute_animal = "sugar glider"
  return cute_animal
end
 
make_variable
# => "sugar glider"
 
cute_animal
# => NameError: undefined local variable or method `cute_animal' for main:Object
```

Unlike Ruby, JavaScript will make a variable have local scope only if you use the keyword `var`. For instance:

```javascript
function makeVariable() {
  cuteAnimal = "sugar glider";
  return cuteAnimal;
}
 
makeVariable();
// "sugar glider"
 
cuteAnimal;
// "sugar glider"
```

Now might be a good time to drop to your knees, throw your fists into the air, and scream-ask, "What?!?"

Let me give you a second. You ready? Okay, here's what's going on:

JavaScript does not treat variables as local by default; you must use the keyword `var`. Without this keyword, variables have a global scope. JavaScript knew about the variable `cuteAnimal` because we accidentally gave it a global scope. To make it local in scope (always what you want; don't pollute that global namespace with variables!), you'd have to add that `var` keyword, like so:

```javascript
function makeVariable() {
  var cuteAnimal = "sugar glider";
  return cuteAnimal;
}
 
makeVariable();
// "sugar glider"
 
cuteAnimal;
// ReferenceError: cuteAnimal is not defined
```

It's been mentioned already, but again, it is best to use the key word `var` before declaring a variable. This ensures that the variable is set to the current scope. If `var` is not used in defining a new variable it becomes global and is accessible throughout the program.

Keep in mind too that the same variable name used in different scopes is effectively a different variable.

We sometimes refer to repeating a variable name in an inner scope as "shadowing" — it's best to avoid, as you'll quickly see how confusing it can be:

```javascript
var cuteAnimal = 'quokka';
 
function makeVariable() {
  var cuteAnimal = 'sugar glider';
  return cuteAnimal;
}
 
makeVariable();
// 'sugar glider'
 
cuteAnimal;
// 'quokka'
```

See? Nothing breaks, but if you have a lot of shadowed variables (or even just a lot of space between a variable's initial declaration and its subsequent change(s)), you're gonna have a bad time.

####CHANGING VARIABLE VALUES
Local variable assignment can overwrite global variable assignment:

```javascript
volume = 10; //declares a global variable called volume and sets it to 10
 
function returnEleven () {
  var volume = 11;  //declares a local variable called volume and sets it to 11
  return volume;
}
 
returnEleven(); // returns 11
volume; // the global variable is still 10
 
function goToEleven(){
  volume = 11;  //changes the global variable to 11
  return volume;
}
 
goToEleven(); // returns 11
volume; // the global variable volume has been changed to 11
```

However, global variable assignment can't overwrite local variable assignment, rather it simply reassigns the value of the local variable:

```javascript
function sayHello() {
  var greeting = "hola";
  greeting = "hello";
  return greeting;
}
 
sayHello()
// Returns "hello",
// This demonstrates that the variable greeting is now pointing to the string "hello" instead of "hola"
 
greeting
// ReferenceError: greeting is not defined
// This demonstrates that the variable greeting is still local instead of global
```
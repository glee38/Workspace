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

##Intro to Data Types in JS
####OBJECTIVES
+ Define "data type"
+ Explain JavaScript's data types
+ Use the typeof operator

####WHAT IS A DATA TYPE?
At the machine level, all data on a computer is bits — 1s and 0s. Humans, it turns out, prefer not to work so close to the metal, so we have data types for describing different bits of information. Data types give us a quick way of understanding how we can operate on a given bit of data.

####MATH!
Let's do some math. Open up your console and enter:

```javascript
2 + 2
```

We should, unsurprisingly, see `4` show up in console. Cool. Now let's try

```javascript
2 + "2"
```

(Don't forget the quotation marks around the second number!)

And press enter.

Um. Something's broken?

Do you see `"22"`? That's because we're adding (with the + operator) things of two different *types*. `2` is a number; `"2"` is a string. So when we add them together, JavaScript recognizes that we can't do simple arithmetic — instead, it tries to make the two things compatible. In this case, it turns the first `2` into `"2"` and then *concatenates* (pushes together) the two things — so we get `"22"`.

One last example — before you press "enter" for this one, we want to think about what's going to happen. What will the result be?

```javascript
"2" + 2
```

Have you thought about it? What did we learn above? Are you ready? Okay, press "enter".

That's right, the result of `"2" + 2` is also `"22"`.

Why all this mumbo jumbo with types? Well, let's think about it. As humans when we see `2345`, we provide context: we might think of this sequence of integers in our heads as "two thousand three hundred forty-five"; we might note immediately that the integers are consecutive. We understand that `2345 + 2` should give us `2347`.

JavaScript can't do that. It sees `2345` and only knows that it's a number. Similarly, it sees *anything* in quotation marks as a string. We provide the context for JavaScript according to the rules that JavaScript follows — one of those rules happens to describe what happens when we add a number and a string.

####YOU'RE JUST NOT MY TYPE
How do we know what types we're dealing with? JavaScript gives us the handy dandy `typeof`. We use it like so

```javascript
typeof 2 // "number"
typeof "2" // "string"
typeof '2' // "string" — strings can be enclosed in single (') or double (") quotes
```

Pretty simple, right?
In addition to numbers and strings, JavaScript has the following primitive types:

+ Boolean
+ Undefined
+ Null

Enter the following commands in your console to get a feel for the different types:

```javascript
typeof 1
typeof 10
typeof 1.123
```

```javascript
typeof "Albert"
typeof '123'
typeof "What's my type?"
```

```javascript
typeof true
typeof false
```

```javascript
typeof undefined
```

```javascript
typeof null
```

Also enter the following — pay attention to the errors!

```javascript
typeof 1.123.45
```

```javascript
typeof 'I'm not going to work'
```

For now, we're going to explore numbers and strings the most. Later in the curriculum, you'll learn a lot more about booleans, `null` and `undefined`.

####NUMBERS
JavaScript uses numbers just like you would think of them, and we can even use decimal points. Enter the following in console:

```javascript
4
8.0
16.123
```

In fact, JavaScript treats *all* numbers as if they have decimal points (even if they don't). Sometimes this can lead to unexpected consequences. Enter the following in console:

```javascript
4
```

If you press enter, you'll see `4`. Simple enough. Now enter the following:

```javascript
4.0000000000000001
```

If you press `enter`, you'll see... `4`. Hrm. It's not important to know the details of how this behavior works right now (although we encourage you to search for resources online if you find it interesting!), but it is important to know that it happens.

####STRINGS
Strings are very straightforward in JavaScript. They are collections of characters. Plus signs are used to concatenate strings.

In their most basic form, strings look like

```javascript
"I'm a string"
'I\'m also a string'
```

Notice that we added a `\` before the `'` in the second string above. Because we use quotation marks to tell JavaScript, "Hey, this is a string!", we have to *escape* quotation marks when they're inside of a string so that JavaScript knows to treat them as part of the string.

Go ahead and enter `'I\'m a string'` in console. You should see `"I'm a string"` — when we wrap a string in double quotation marks, we don't need to escape single quotation marks (the apostrophe, in this example) that appear inside.

```javascript
"I'm a string"
```

Similarly, we don't need to escape double quotes when we use them in a singly-quoted string:

```javascript
'I said, "Strings are pretty nifty."'
```

We can "add" strings together — this is called concatenation:

```javascript
'Hello, ' + 'World'  // Returns 'Hello, World'
 
'High ' + 5 + '!!!' // Returns 'High 5!!!'
```

We can also insert strings into other strings — this is called *interpolation*. JavaScript supports string interpolation with [template literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals).

Template literals look and behave like strings, except instead of being wrapped in a single or double quote, they're wrapped in backticks (at the top left of your keyboard — it looks like `).

```javascript
`High ${3 + 2}!` // 'High 5!'
```

We'll cover string interpolation in greater depth later on, but for now notice that the whole string is wrapped in backticks, and the part that we *interpolate* is wrapped in `${}`. This, `${}`, is simply a special signal to JavaScript that people weren't likely to write out on their own — it signals to the JavaScript interpreter that it should *evaluate* (that is, run the code) whatever is inside of it. Being able to evaluate our code and put the result directly inside of a string is a super powerful idea that we'll make lots of use of.

##Primitive Data Types And Objects in JS

####OBJECTIVES
+ Explain what a primitive data type is
+ Explain what an object is
+ Create a primitive data type
+ Create an object
+ Explain why use the literal constructor over the new constructor

While JavaScript shares a lot of the same data types with Ruby, it does handle them a little bit differently.

In Ruby, every data type is an object. A string (whether its made with the literal constructor or `String.new`) is considered an instance of the String class. Also in Ruby, every object is mutable, which means you can change the object with methods like `upcase` and `swapcase`.

In JavaScript, data types fall into two categories: **primitive data types** and **objects** and they behave very differently.

All primitive types are immutable (values incapable of being changed), while objects are mutable. Primitive values are important for application performance. They are way faster for an application to process than objects.

####PRIMITIVE DATA TYPES
JavaScript has six primitive data types:
+ Number
+ String
+ Boolean
+ Undefined
+ Null
+ Symbol

Primitive values are created using the literal constructor for each data type.

```javascript
var name = "joe";
typeof name;
// returns "string"
 
var cents = 99;
typeof cents;
// returns "number"
 
var truth = true;
typeof truth;
//returns "boolean"
```

####OBJECTS
In JavaScript, objects can be seen as a collection of properties. You can think of them as most similiar to Ruby hashes, but with superpowers of storing functions as values in addition to the standard strings, numbers, etc.

Objects are created using the New constructor:

```javascript
var word = new String("hello");
typeof word;
//returns "object"
 
word;
//returns String {0: "h", 1: "e", 2: "l", 3: "l", 4: "o", length: 5, [[PrimitiveValue]]: "hello"}
 
var num = new Number(10);
typeof num;
//returns "object"
num;
//returns Number {[[PrimitiveValue]]: 10}
```

Because objects are mutable, you can use convenience methods to mutate these values. We'll get more into String convenience methods later, but just know they exist and can be done to any string object.

####MUTATING NUMBERS AND STRINGS
So we know we can modify objects but not primitive data types. But that's not entirely true. Let's take a string created with the literal constructor as an example:

```javascript
var word = "hello";
typeof word;
//returns "string"
 
 
word.toUpperCase();
// returns "HELLO"
```

But wait, I thought primitive values, like the one we just created, were immutable? That's because JavaScript gives you the ability to treat primitive values like objects. In the example above, behind the scenes, JavaScript turns the primitive value into an object, mutates the object, and turns it back into a primitive value.

Given the following code:

```javascript
  var greeting = "hello";
  var word = new String("hello");
  word === greeting; 
  // returns false
 ```
  
The `===` is a type comparison operator. This means that it checks not just value, but data type. If the value and the data type don't match, it returns false. Using the type comparison operator, `greeting` and `word` are not the same type (one is a primitive value and the other an object) so we wouldn't expect them to be equal. But you can use convenience methods to manipulate both in the same way.

Because of this, you want to use the literal constructor as much as possible. For all intents and purposes, you can use the literal constructor and the new constructor in same way, but your code will process much more quickly with the literal constructor.

##Strings in JS
####OBJECTIVES
+ Use string concatenation
+ Use JS string convenience methods
+ Convert string to number data type

Just like in Ruby, JavaScript has a handful of convenience methods built into the language that allow us to easily manipulate and modify strings. You can read all about Strings in JS, and even more methods [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String).

Feel free to open up the Developer Tools in Chrome or Firefox to play around with these methods.

####CONCATENATION
Unlike Ruby, JavaScript doesn't come with a prior knowledge of how to interpolate a variable (remember, interpolation in Ruby looks like this: `"Hi, my name is #{name}"`).

Instead, when you want to combine several variables into one long string, you must concatenate.

```javascript
var name = "cricky";
console.log("Hi my name is " + name);
//this will print "Hi my name is cricky"
```

JavaScript does something unusual when you add a string with a number:

```javascript
var hours = 10;
var species = "sloths";
 
// the line below prints "sloths sleep 10 hours a day"
console.log(species + " sleep " + hours + " hours a day");
```

You'll notice in the example above, the variable `hours` is storing a number, while the rest of the sentence is a string. When adding a number and a string, JavaScript automatically transforms the number into a string.

####LENGTH
Javascript strings have a `length` property that will return the number of characters in that string. Note that this is not a function call.

```javascript
var species = "mantis shrimp";
 
// the line below will print 13
console.log(species.length);
```

####CHANGING CASE
The two common functions to change the case of a string are `toUpperCase()` and `toLowerCase()`. It's important to note that when you call a function that doesn't require a parameter, like `toUpperCase()` and `toLowerCase()` does need to be invoked with `()`.

```javascript
"I'm not shouting!!!".toUpperCase();
// => "I'M NOT SHOUTING!!!"
 
"I'M A CONSCIENTIOUS LIBRARIAN.".toLowerCase();
// => "i'm a conscientious librarian."
```

You may notice that there is no pre-built `capitalize()` method. To capitalize just the first letter of a string, you'll have to make your own custom method.

####REPLACING CHARACTERS
To replace characters with new characters, you can use the `replace()` method. It works similarly to Ruby's `gsub` method in that the first parameter is the set of characters you would like to remove and the second is the string you would like to add instead.

```javascript
var sentence = "pandas have two compound eyes and a proboscis".replace("pandas", "butterflies");
// => "butterflies have two compound eyes and a proboscis"
sentence.replace("two compound eyes", "two antennae")
// => "butterflies have two antennae and a proboscis"
```

Notice that the `replace()` will replace only the first occurrence:

```javascript
var quote = "xylophone phone home";
quote.replace("o", "*");
 
// quote is now "xyl*phone phone home"
```

The string class' `replace()` function can also take Regex for the first parameter:

```javascript
var phoneNumber = "5556768799";
 
// the line below will return "(555) 676 - 8799"
phoneNumber.replace(/(\d{3})(\d{3})(\d{4})/, '($1) $2 - $3');
```

####TURN STRING TO NUMBER
Two different functions to turn are `parseInt()` and `Number()`. The `Number` function creates a new number, while the `parseInt` function parses the string. Check out [this stack overflow post](http://stackoverflow.com/questions/4090518/what-is-the-difference-between-parseint-and-number) for more information.

```javascript
Number("78");
// => 78
parseInt("78");
// => 78
Number("20px");
// => NaN
parseInt("20px");
// => 20
```

####SLICE
The `slice()` method extracts a section of a string and returns a new string. It accepts one required parameter, the index to start on, and one optional parameter, the index to end on.

```javascript
var sentence = "They misunderestimated me.";
var word = sentence.slice(5, 22);
 
// the line below prints "misunderestimated"
console.log(word); 
```

Like in Ruby, you can also use negative indices with strings:

```javascript
var bushism = "They misunderestimated me.";
var word = bushism.slice(5, -4);
 
// the line below prints "misunderestimated"
console.log(word); 
```

####SPLIT
The `split()` method splits a String object into an array of strings by separating the string into substrings.

```javascript
var longString = "Jan,Feb,Mar,Apr,May,Jun";
var months = longString.split(",");
 
// months is now ["Jan","Feb","Mar","Apr","May","Jun"]
```

##Math in JS
In Ruby solving for the square root of a number involves using a class method `.sqrt` on the `Math` class. Methods like `sqrt` in Ruby are class methods that we call on the `Math` class.

While JavaScript supports a lot of the same mathematical operations as Ruby, there is one big difference. `Math` in JavaScript is actually a single built-in object that you can perform operations on. This `Math` object does have plenty of convenience functions that allow you to do math simply and easily. Think of these functions as the equivalent to instance methods in Ruby. You can read more about math in JavaScript [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math).

####OBJECTIVES
+ Explain how numbers work in JS
+ Round numbers to nearest whole number
+ Perform series of math problems on numbers following order of operations

####ROUNDING
If you'd like to round to the nearest whole number, use the `.round()` method built out for you by JavaScript's Math class.

```javascript
// Returns the value 20
Math.round(20.49);
 
// Returns the value 21
Math.round(20.5);
 
// Returns the value -20
Math.round(-20.5);
 
// Returns the value -21
Math.round(-20.51);
```

####FLOOR
If you'd like to round to a whole number less than or equal to a number, use `Math.floor()`.

```javascript
// Returns 45
Math.floor(  45.95 );
 
// Returns -46
Math.floor( -45.95 );
```

####ABSOLUTE VALUE
The `Math.abs()` function returns the absolute value of a number.

```javascript
// Returns the value 1
Math.abs('-1');
 
// Returns the value 1.67
Math.abs('1.67');
 
// Returns the value 2.5
Math.abs(-2.5);
```

####SQUARE ROOT
To take the square root of a number, pass the number to the Math class' `sqrt()` method.

```javascript
// Returns the value 3
Math.sqrt(9); // 3
 
// Returns the value 1.414213562373095
Math.sqrt(2);
 
// Returns the value 1
Math.sqrt(1);
 
// Returns the value 0
Math.sqrt(0);
```

####ORDER OF OPERATIONS
Just like in Ruby, parentheses indicate precedence. For instance, the equation to convert Fahrenheit to Celsius is to first subtract thirty-two then multiply by five-ninths. To prioritize this subtraction before the multiplication happens, we wrap it in parentheses.

```javascript
temp_in_fahrenheit = 104
var celsius = (temp_in_fahrenheit - 32) * 5/9;
var rounded = Math.round(celsius);
rounded + "°C";
 
// Returns the value "40°C"
```

If we forgot to wrap the subtraction in parentheses, the code would multiply thirty-two by five-ninths then subtract this product from the value for Fahrenheit, leading to a return value of "86°C" instead (for reference, 86°C is 187°F).

####ADDING STRINGS AND NUMBERS
Something to be aware of as a Rubyist learning JavaScript is that JavaScript will concatenate numbers. In Ruby, when you add the strings "1", "2", and "3", you get the return string "123". However, if you were to add the strings "1" and "2" plus the number 3, you would get a type error:

Ruby:

```ruby
# Ruby
 
"1" + "2" + "3"
# => "123"
 
"1" + "2" + 3
# => TypeError: no implicit conversion of Fixnum into String
```

Unlike Ruby, JavaScript will convert this final number into a string and tack that string onto the end.

Javascript:

```javascript
// JavaScript
 
"1" + "2" + "3"
// => "123"
 
"1" + "2" + 3
// => "123"
```

Here are some more examples of JavaScript adding strings with one number:

```javascript
"1" + "5"
// => "15"
 
"1" + 5
// => "15"
 
5 + "1"
// => "51"
```

Notice that if number preceeds the strings, JavaScript will perform the math on the strings before adding the result of the math to the String at the end:

```javascript
5 + 5 + "5"
// => "105"
```

However, if a string starts off the statement, JavaScript will treat the following numbers as strings and not perform math on them:

```javascript
"5" + 5 + 5
// => "555"
```

For more info, see the docs for the [addition operator](http://es5.github.io/#x11.6.1).

##Functions in JS
You're already very familiar with the idea of wrapping our code as methods in Ruby in order to make them reusable. In JavaScript, we call them functions and the syntax is slightly different, but the general idea is the same.

####OBJECTIVES
+ Explain how function return values work
+ Write a function in JavaScript without parameters
+ Return a value from a function in JavaScript
+ Write a function with parameters
+ Write a function with default parameters

####FUNCTION WITHOUT PARAMETERS
Here's the basic syntax for a function that doesn't take any parameters (you know them as arguments):

```javascript
function nameOfFunction() {
  // code goes here
  return valueToReturn;
}
```

Notice the `def` keyword has been replaced with the `function` keyword. The name of the function is always followed by `()` and then curly braces that begin and
end the function.

Further, the name of the function is not snake_cased, but rather lowerCamelCased. Snakecase is not used in JavaScript so leave your underscores at home people!

One last important thing to note is that JavaScript functions will always return `undefined` unless you use the `return` keyword. In Ruby, writing `return` is optional because Ruby always returns the value of the last line of code evaluated. However, JavaScript has no implicit-return-value concept, so you must write `return` before the value you want to return.

**NOTE**: This isn't exactly true in [ECMAScript 6](http://es6-features.org/).
For arrow function expression bodies that are not wrapped in curly brackets, you can omit the return:

```javascript
[1, 2, 3, 4].filter(i => i % 2 === 0) // [2, 4]
```

But if you wrap the function body in curly brackets, you must explicitly return your desired value:

```
[1, 2, 3, 4].filter(i => { return i % 2 === 0 }) // [2, 4]
 
// alternatively
 
[1, 2, 3, 4].filter(i => {
    return i % 2 === 0
})
```

Here's a simple example of a function that will will greet us good morning:

```javascript
function greet() {
  return "Good morning you!";
}
```

To call a function, you type the functions name followed by a beginning parenthesis and followed by an ending parenthesis, followed by a semicolon:

```javascript
// Returns "Good morning you!"
greet();
```

If you try to call on a function using just its name, as you can in Ruby, the entire function will get returned back to you instead of the function's return value so be careful!

```javascript
// Returns [Function: greet]
greet;
```

It's important to note that JavaScript does not run the code during a function definition. All it does is read that there is a function called `greet` and store it in memory. It doesn't actually create any of the variables inside the function (if there are any) until the method is called.

####FUNCTION RETURN VALUES
Unless you use the `return` keyword or call a function that explicitly returns a value, the implicit return value in JavaScript is `undefined`.

> Undefined represents the absence of a primitive value

Other special return values in JavaScript are `null`

> Null represents the absence of an object

and `NaN`

> NaN represents an error from the improper use of a math operator.

####PARAMETERS
The above is function is fine, but it could be better. For instance, what if you wanted your program to say the person's name instead of just "you" (ex. "Good morning Adam!" or "Good morning Steph!"). Well, just like in Ruby, you'd want to pass this function an argument. In JavaScript, arguments are called parameters.

```javascript
function greet(name) {
  return "Good morning " + name + "!";
}
 
// Returns "Good morning Adam!"
greet("Adam");
 
// Returns "Good morning Steph!"
greet("Steph");
```

Cool! So much better and more dynamic than having JavaScript return "Good morning you!". What if we also wanted it to greet us based on the time of day, for instance "Good afternoon Harold!" or "Good night Jasmine!"? Well, then we'd have to pass our function a second parameter, the time of day:

```javascript
function greet(name, timeOfDay) {
  return "Good "+ timeOfDay + " "+ name + "!";
}
 
// Returns "Good afternoon Harold!"
greet("Harold", "afternoon");
 
// Returns "Good night Jasmine!"
greet("Jasmine", "night");
```

Like in Ruby, you can add as many parameters to your JavaScript functions as you like. Or you can pass them none. Just depends on what task you're trying to accomplish.

####FUNCTION EXPRESSION
There are two different ways to write functions in JavaScript. You can write them as a function **declaration** which is how we've been writing them, or as a function **expression**.

A function expression looks something like this:

```javascript
var greet = function(name, timeOfDay){
  return "Good "+ timeOfDay + " "+ name + "!";
};
```

We can still call this function in the same way we would a function written with a function declaration:

```javascript
// Returns "Good afternoon Grover!"
greet("Grover", "afternoon");
```

Function declarations are hoisted to the top of the current scope; function
declarations are not.

```javascript 
// this works, even though we're calling
// the function before the declaration —
// the `sayGoodbye` function is *hoisted*
// to the top of the current scope when
// the script is evaluated
sayGoodbye();
 
function sayGoodbye() {
    return 'Goodbye!';
}
 
 
// this does not work, as the function
// is not hoisted in a function expression
 
sayHello(); // TypeError: sayHello is not a function
 
var sayHello = function() {
    return 'Hello!';
};
```

It's good to be familiar with both ways of writing a function for when you start working with other developers, or using Google resources for help.

####DEFAULT PARAMETERS
Ruby has an idea of allowing you to pass optional arguments by defaulting argument values. To create a default argument, you add an equal sign where you
define the variable name of your argument and set it equal to the value you want it to default to:

```ruby
def greet(name="you")
  "Good morning #{name}!";
end
```

This way, you can call on the method with the argument and it will set name to be the string you passed.

Ruby:

```ruby
greet("Jasmine")
# => "Good morning Jasmine!"
```

**or** you can call on the method without the argument and `name` will default to the string `"you"`:

```ruby
greet
# => "Good morning you!"
```

In JavaScript, you can make functions behave has though they have default arguments. Here again is the simple greeting function:

```javascript
function greet(name) {
  return "Good morning " + name + "!";
}
```

To give the variable `name` a default value, you check to see if `name` has been defined on the first line of your function. To do this, you call JavaScript's `typeof` operator, which is a lot like calling .class in Ruby.
If the function wasn't passed a parameter, then the variable will be `"undefined"`.

```javascript
function greet(name) {
  if (typeof name === "undefined") {
    // add code
  }
  return "Good morning " + name + "!";
}
```

Inside the `if` statement, go ahead and set the default value you desire:

```javascript
function greet(name) {
  if (typeof name === "undefined") {
    name = "you";
  }
  return "Good morning " + name + "!";
}
```

Let's now see our function in action:

```javascript
// Returns "Good morning Blake!"
greet("Blake");
 
// Returns "Good morning you!"
greet();
```

Unlike Ruby, JavaScript doesn't force you to call a function with a set number of parameters. If a JavaScript function it defined to accept two parameters, and
you call the function with only one parameter, the function will still run, but place `undefined` in the spot of the missing paramater:

```javascript
function greet(name, timeOfDay) {
  return "Good "+ timeOfDay + " "+ name + "!";
}
 
greet("joe");
// returns "Good undefined joe!"
```

Be careful when calling functions with parameters. This could inadvertently mess up your return values.

####ES6 NOTE
ECMAScript 6 introduces [default parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters) in JavaScript. Their syntax should look familiar:

```javascript
function greet(name="Sue", timeOfDay="morning") {
    return "Good " + timeOfDay + " " name + "!";
}
 
// 'Good morning, Sue!'
greet();
 
// 'Good morning, George!'
greet('George');
 
// 'Good afternoon, Dory!'
greet('Dory', 'afternoon');
```

####EXAMPLE
If you'd like more practice with JavaScript functions, this section covers how to rewrite a Ruby method that converts feet to meters in JavaScript. Here's that Ruby method:

```ruby
def convert_to_meters(feet)
  meters = feet / 3.2808
  rounded = meters.round(2)
  "#{rounded} meters"
end
```

Now let's make it a JavaScript function!

The first step is to change the `def` keyword into a `function` keyword. The next step is to change the method's name, which is in snake case, into lower camelCase. The third step will be to change the `end` keyword into an ending curly brace and add a beginning curly brace after the argument, like so:

```javascript
function convertToMeters(feet) {
  // add code
  // add code
  // add code
}
```

The next step is to take the variable, `meters` and preempt it with the keyword `var` so that it's limited in its scope. We'll then end that line of code with a semicolon:

```javascript
function convertToMeters(feet) {
  var meters = feet / 3.2808;
  // add code
  // add code
}
```

Just like we changed the `meters` variable from having a global scope to having a local scope, we'll do the same for the `rounded` variable:

```javascript
function convertToMeters(feet) {
  var meters = feet / 3.2808;
  var rounded = // add code
  // add code
}
```

To round a float in JavaScript, you call `.toFixed()` on the number. If you're operating on the literal number, remember to wrap it in parentheses, but since we're storing it in the variable `num`, we don't have to worry about that. You then pass `.toFixed()` a parameter, the number 2, meaning you would like two digits after the decimal:

```javascript
var num = 3.14159;
 
// Returns the number 3.14
num.toFixed(2);
```

Let's incorporate the `.toFixed()` function to the second line of our new JavaScript function:

```javascript
function convertToMeters(feet) {
  var meters = feet / 3.2808;
  var rounded = meters.toFixed(2);
  // add code
}
```

Finally, let's concatenate our rounded meter value with the string " meters":

```javascript
function convertToMeters(feet) {
  var meters = feet / 3.2808;
  var rounded = meters.toFixed(2);
  rounded + " meters";
}
```

This looks about right. To see if this function works, we'll call on the function by name, just like in Ruby:

```javascript
// Returns undefined
convertToMeters(16)
```

Alright, so instead of getting our desired result of `"4.88 meters"`, we got `undefined`. As mentioned above, this is because JavaScript functions, unlike Ruby methods, will always return `undefined` unless you use the `return` keyword. Let's add that keyword to the left of the function's last line:

```javascript
function convertToMeters(feet) {
  var meters = feet / 3.2808;
  var rounded = meters.toFixed(2);
  return rounded + " meters";
}
```

And if we try to run our function again, we can see if that did the trick:

```javascript
// Returns "4.88 meters"
convertToMeters(16)
```

And it did! Now try this out for yourself. Turn the Ruby method below into a JavaScript function. If you get stuck, take a look at the solution at the end of this document.

```
def convert_to_fahrenheit(celsius=0)
  celsius *  9/5 + 32
end
```

####RESOURCES
+ [Eloquent JavaScript - Functions](http://eloquentjavascript.net/03_functions.html)
+ [MDN - Functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions)
+ [MDN - Arrow Functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
+ [MDN - Default Parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters)

####SOLUTION
Here's the Ruby method that converts Celsius to Fahrenheit:

```ruby
def convert_to_fahrenheit(celsius=0)
  (celsius *  9/5) + 32
end
```

Here's the JavaScript method that converts Celsius to Fahrenheit:

```javascript
function convertToFahrenheit(celsius) {
  if (typeof(celsius) === "undefined") {
    var celsius = 0;
  }
  return (celsius *  9/5) + 32;
}
```
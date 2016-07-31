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

##Javascript Debugging with Mocha
Testing is important no matter what language we're working with. There is always the chance that our code won't behave as expected. Tests and debugging skills help us make sure that our code always works appropriately. JavaScript provides tons of testing libraries; we're going to use [Mocha](https://learn.co/tracks/full-stack-web-development/javascript/javascript-basics/mochajs.org). JavaScript comes with a built in debugging tool, called `debugger`.

####OBJECTIVES
+ Read Mocha tests
+ Run Mocha tests
+ Use debugger to run through code line by line

####RUNNING THE TEST SUITE
We've got a test suite set up for you, and we're going to walk through how to run Mocha tests to correct our code. The tests are located in `test/index-test.js`. You'll be writing your solution in `index.js`. Note that our test file has the same name as our application file, just with a `'-test'` prefix. This is a convention that we'll see (in various forms) a lot — and it's a good one to follow, as it makes it clear which tests cover which parts of the application.

To run Mocha tests, you enter `learn` in your terminal (in the Learn IDE or in Terminal.app).

The command `learn` will automatically run every single test in your test suite. We'll cover how to run a single test file or even a single test below.

Let's go ahead and run the tests now. You should see the results for every test in the browser like this:

![mocha test output](https://curriculum-content.s3.amazonaws.com/skills-based-js/mocha_test_output.png)

Well, that's not very exciting. Let's make sure we can get these tests to pass!

####SOLVING THE FIRST TEST
We're just getting started, so we expected that all our tests would fail. Let's go ahead and tackle the first test. The first error we see from Jasmine is `ReferenceError: sayHey is not defined`. So let's go ahead and define that function in `index.js`.

```javascript
function sayHey() {
 
}
```

Save your changes and go back to the browser and click on the first test. You should see an error message that says `Expected undefined to be 'Hey!'`. This makes sense, because we haven't provided a return value for our function yet — so when the function runs, the result is simply `undefined`. Our test expects the result to be the string `'Hey!'`. Let's go ahead and add a return value to our function:

```javascript
function sayHey(){
  return "Hey!"
}
```

Run `learn` again — the test should now pass!

####SOLVING THE LAST TEST WITH `DEBUGGER`
Our last test is failing. You should see the error `ReferenceError: sayHeyFriend is not defined`.

Let's go ahead and define that function in `index.js`:

```javascript
function sayHeyFriend() {
 
}
```

Instead of plowing through the tests by making assumptions about what they want, we're going to use the JavaScript `debugger` to test our code. We'll be following these steps:

1. Add the debugger to our code and save it
2. From the Mocha test, we'll open up a console in the browser
3. Investigate the state
4. Find the bug

####STEP ONE - ADD THE DEBUGGER
Let's put the `debugger` inside our function definition, like so (we're also adding the `name` argument):

```javascript
function sayHeyFriend(name) {
  debugger;
}
```

####STEP TWO - OPEN THE CONSOLE
Now we'll run our tests so that they're aware of the debugger. Simply run `npm run debug` if you're working locally, or `npm run debug-ide` if you're working in the IDE.

**Flat fact**: `npm` is a package manager originally intended for the Node.js runtime but now used by the entire JavaScript ecosystem. We use it here because we want our tests to run flexibly in a lot of different environments — it helps us pull in the libraries that our code needs to run.

If you're working locally, an instance of Chrome should open up; from the IDE, you'll be presented with a URL like this

![ide debugger](https://curriculum-content.s3.amazonaws.com/skills-based-js/ide_debugger.png)

**Your URL will be slightly different**. Copy and paste this URL into Chrome (you must use Chrome for debugging in this case).

From there, open your browser's console. (Remember, the shortcut to open the console in Chrome is `command` + `option` + `J`.)

The page will be mostly greyed out and the message "Paused in debugger" should appear up top.

####STEP THREE - INVESTIGATE THE STATE
Navigate back to the console, either by clicking console or typing in the very bottom screen on Chrome, and enter `name`. We expect it to be `"Kristin"`, which is the parameter the spec is passing to our function, and it is:

![name revealed in console](https://curriculum-content.s3.amazonaws.com/skills-based-js/javascript_debugger_open.png)

####STEP FOUR - FIND THE BUG
Around now, we might remember that our function is returning `undefined` instead of the value of the remainder because we didn't use the `return` keyword, so let's go ahead and add that and save the JavaScript file:

```javascript
function sayHeyFriend(name){
  return name;
}
```

Now we'll click the blue forward arrow button to exit debugger. It's pretty much the same as typing `exit` in Pry: ![debuggers blue unpause arrow](http://web-dev-readme-photos.s3.amazonaws.com/js/jasmine-and-debugging/blue-arrow.png)

Let's run our tests without the debugger (simply enter `learn`). We still get an error: `Expected 'Kristin' to be 'Hey, Kristin!'`.

We're just returning the name passed in as a parameter instead of greeting that person.

Change your solution in `index.js`:

```javascript
function sayHeyFriend(name){
  return `Hey, ${name}!`;
}
```

Go ahead and run `learn` again, and both tests should pass!

##Javascript Intro to Mocha
####OBJECTIVES
+ Run Mocha Tests
+ Read Mocha Tests
+ Use `debugger` to figure out bugs in code

####MOCHA
In Mocha, to make a set of tests for a particular subject, we set it apart by creating a `describe` function with the first argument as the subject, and the second as an function. Within that function, we can either go more specific with nested `describe` blocks or an it function. `it` is an actual test; it takes a string as the first argument about what it is testing and a function that contains an assertion (we're using the `expect` [library](https://github.com/mjackson/expect) here) that compares your code to the expected outcome.

If we need to run certain code before or after some (or all) functions run, we can use `before()` and `after()` as well as `beforeEach()` and `afterEach()`.

For example, in our test file, we're writing browser-based tests, we need to simulate a browser in our testing environment. To do so, we use `jsdom`, which mocks out objects and behaviors as if we were in a browser without actually forcing us to render anything in a browser window. This setup makes our tests portable — that is, it makes it so that they can run just about anywhere.

We set up the "browser" in a call to `before()`:

```javascript
before(done => {
  const src = path.resolve(__dirname, '..', 'code.js');
 
  jsdom.env('<div></div>', [src], (err, window) => {
    if (err) {
      return done(err);
    }
 
    Object.keys(window).forEach(key => {
      global[key] = window[key]
    });
 
    done();
  });
});
```

This looks a little intimidating, but don't worry, we'll walk you through it.

The first thing to notice is `done`. This signals to Mocha that what we're doing inside of `before()` runs asynchronously (of or requiring a form of computer control timing protocol in which a specific operation begins upon receipt of an indication (signal) that the preceding operation has been completed), and tells Mocha to wait until we call `done()` to start running any of the tests. Notice where we call `done()` inside of the callback that gets `err, window` as its arguments.

Then we assign the location of the code we want to test to the variable `src` — pretty basic. (Don't worry too much about `path.resolve` — it's a part of the Node.js path library for determining the path of something. In this case, it's figuring out where `code.js` lives.)

Then we call `jsdom.env()`. This function receives four arguments:

1. An HTML string. This string sets up the DOM — it can be arbitrarily long (we could even read in a full HTML file), but in this case, we just need something basic, since our tests don't really use the DOM.

2. An array of paths to source files. We only have on file to test, so it's the only element in the array.

3. A callback. This function, in typical Node.js fashion, receives an error first. The `err` will most likely be `null`, but if it's defined, we call `done(err)` to tell Mocha to stop and show us what went wrong. Assuming things are going as expected, we then take all of the things defined on `window` (including, in this lab, the functions we've written) and add them to `global` so that we can call them in our tests.

Finally, we call `done()` with no arguments to tell Mocha that we're finished with this `before()`. The tests start running here.

####TEST WALK-THROUGH

```javascript
describe('favoriteIceCream(favorite)', () => {
  it('returns "I love ${favorite}"', () => {
    expect(favoriteIceCream("mint chocolate chip")).toBe("I love mint chocolate chip");
  });
});
```

The test above tests a function named `favoriteIceCream`. The `it` block declares in plain English the expectation being tested. In this case, we're testing that the function returns a sentence about the favorite ice cream. The `expect` block calls the function behind tests, and uses a matcher (in this case `toBe`) to test what the return value of the function should be.

The `expect` is calling the function `favoriteIceCream()` and passing in the parameter `"mint chocolate chip"` — it's just as if `favoriteIceCream()` were running in a "real" application, but we can make assertions about what it returns.

####THE DEBUGGER
Before you get started, make sure you run `learn` to see the test output in your terminal. Take each test step by step, and remember that you can use `debugger` if you get stuck.

For example, in our function `favoriteIceCream()`, you might want to see what the arguments look like. You can add a `debugger` statement like so:

```javascript
function favoriteIceCream(flavor) {
  debugger
}
```

Then, if you open up `index.html` in your browser (if you're using the IDE, simply right click on the file and select "Open in browser") and call `favoriteIceCream('chocolate')` in the browser's terminal, you can inspect the `flavor` argument simply by hovering over it. Pretty cool, right?

####DEBUGGING WITH NODE-INSPECTOR
As we mentioned above, we're actually running our tests in a slightly contrived environment. This setup lets us be very flexible with our approach to debugging. If you're reading this lesson locally (that is, if you aren't using the IDE), you can run `npm run debug` to launch a debugging session in your browser. This will run the tests in Terminal.app, letting you inspect the results as you step through the code. Note that you must use Chrome for the debugger to work.

Simply navigate to the provided URL in Chrome on your machine, and you can debug remotely.

##JS Scope
####OBJECTIVES
+ Explain how scope works in JavaScript
+ Explain what lexical or function-level scope is
+ Explain what block-level scope is
+ Explain how local and global variables work in JS

####WHAT IS SCOPE?
Scope is the current context of your code, and can be locally or globally defined. Understanding scope is the key to understanding how your variables interact with each other in your code. Without a solid understanding of scope, your variables could be storing entirely different values than you think they are.

####RUBY VERSUS JAVASCRIPT SCOPE
When talking about scope in this README, we mean variable scope — what's available in the context of your currently executing code. In Ruby, there are four levels of variable scope — global, class, instance, and local — determined by prefixes on the variable name:

+ `$` for global variables
+ `@@` for class variables
+ `@` for instance variables
+ no prefix for local variables

We'll focus on local variables for now. In Ruby, a variable's locality is determined by the function context it appears in. So in this example

```ruby
def locality
  here = true
end
```

`here` is local to the method `locality`. Similarly,

```ruby
["bread", "wine", "cheese"].each do |food|
  puts food
end
```

In the above example, `food` is local to the `each` block.

In Ruby, you can only access local variables that have been declared before the currently executing line in the current scope:

```ruby
x = 1
$x = 3
@@x = 4
@x = 5
 
def my_method
  y = 2
  puts $x
  puts @@x
  puts @x
  puts x
end
 
my_method
 
puts y
 
puts x
```

The method call `my_method` returns the unknown local variable or method error, because `x` is not defined in the scope of that method. However, it successfully prints `$x`, `@@x`, and `@x` because of their broader scopes. (You'll probably see `warning: class variable access from toplevel` for `@@x` — this is expected, and you shouldn't use class variables in this way. We're bending the rules here to highlight how local scope works.)

`puts y` returns the exact same error as `my_method` because `y` was only defined inside the method, not in the global scope. `puts x` does work, because we're printing out `x` in the same scope in which it was created.

But translate that same code to JavaScript and you get a very different result:

```javascript 
var x = 1;
 
function myFunction(){
  var y = 2;
  console.log(x);
}
 
 
myFunction();
// prints out 1
 
console.log(y)
// error - y is not defined
 
console.log(x)
// prints out 1
```

The function call `myFunction();` actually does work perfectly, even `console.log(x);` This is because the variable `x` is defined in the global scope. Any variable defined in an outer scope is available to inner scopes. (So *everything* defined in the global scope is available *everywhere*.)

However, anything defined in a function is not accessible outside of it, which is why the `console.log(y);` does not work. `y` was only defined inside the `myFunction` function, making it inaccessible anywhere else.

And again `console.log(x);` works because were printing it out in the scope in which it was defined.

####THE KEY TO SCOPE
To determine a variable's scope in JavaScript, ask yourself two questions:

```
1. Is it declared inside a function?
2. Is it declared with the `var` keyword?
```

If a variable is declared in the outermost scope of a project, it's going to be global no matter what – that is, whether you write `var foo = 'bar'` or `foo = 'bar'`. Remember, saying that a variable is "global" in JavaScript just means that it's a property of the top-most object. (In the browser, that object is `window`; in other environments (like Node.js), it could be `global`.)

Inside a function, variables declared with `var` are only available within that function's scope. Variable's declared without `var` attach themselves to the global object.

Contrast this to Ruby's variable scoping mechanisms: Where Ruby uses prefixes to determine a variable's scope, JavaScript determines scope primarily based on where a variable is declared (and only when declared within a function does any sort of keyword prefix matter).

In our `myFunction` function, the variable `y` was defined with the `var` keyword. If instead, we had done:

```javascript
function myFunction(){
  y = 2;
  console.log(x);
}
```

We would be able to call `console.log(y)` outside the function as well.

Let's take our whole code example:

```javascript
var x = 1;
 
function myFunction(){
  y = 2;
  console.log(x);
}
 
console.log(y);
 
console.log(x);
```

If you copy and paste this into the JS Console, you'll get an error with `console.log(y);` At this point in time, even though y is a global variable, the variable hasn't been defined. JavaScript has just stored a function called `myFunction`.

If we were to do this:

```javascript
var x = 1;
 
function myFunction(){
  y = 2;
  console.log(x);
}
 
myFunction();
 
console.log(y);
 
console.log(x);
```

Now, because of the `myFunction` call, the global variable `y` exists, and `console.log(y)` will work!

####THE SCOPE OF SCOPE, OR GETTING CLOSURE
One of the most powerful things about scope in JavaScript is how easily we can hide variables from the global scope but still make them available in inner scopes.

```javascript
function outerFunction() {
  var innerVariable = "I'm sort of a secret.";
 
  return function innerScope() {
    var inaccessible = "Nothing can touch me.";
 
    return innerVariable;
  }
}
```

JavaScript has first-class functions, meaning that we can pass them around with ease. When we call `outerFunction()`, the returned value is another function.
Let's give it a try:

```javascript
var myScope = outerFunction();
 
// the stringified version of `innerScope()`
myScope;
```

Cool! What happens if we try to call innerScope() directly?

```javascript
innerScope();
```

This will throw the error `undefined is not a function` (very helpful, JavaScript). But if we call `myScope` (the variable to which we assigned the output of `outerFunction()`):

```javascript
myScope();
```

We should see `"I'm sort of a secret."` returned! What happened?

Inside `outerFunction()`, we made the variable `innerVariable` available to `innerScope()` in what we call a **closure**. In this example, `innerScope()` *remembers* the environment it was created in, and it maintains references to variables declared in that environment in a closure. As we noted above, JavaScript functions have access to their entire outer scope, so the `innerScope()` function has access to `outerFunction()`'s environment and to the global (`window`) environment.

Note, though, that `outerFunction()` doesn't know anything about what's in `innerScope()` — the variable `inaccessible` is aptly named, because we can't get at its value except inside `innerScope()`.

####A FINAL NOTE (EXTRA CREDIT)
We've over-simplified the case for JavaScript for the moment. ECMAScript 6 (ES6) introduces two new declarations, `let` and `const`. Scope-wise, the difference is that `let` and `const` have block-level scope whereas `var` only has function-level scope. This means that declaring with `let` and `const` inside, for example, an `if` block will restrict the use of a variable to that `if` block. You can try out the following at https://babeljs.io/repl

```javascript
if (true) {
  const blockVariable = "block";
  var localVariable = "local";
}
 
// when you run the following lines, you should see:
// "local"
// blockVariable is not defined
// on the right-hand side of the lower middle part of your screen.
console.log(localVariable);
console.log(blockVariable);
```

Try changing `const` to `let` — you should get the same error output.

##Intro to Comparison Operators in JS
####OBJECTIVES
+ Explain the different comparison operators in JS
+ Explain strict comparison operators
+ Use strict comparison operators

####ABOUT
Recall that Comparison operators allow you to test the relationship (equality and identity) between objects. 

####LIST
By now you know about the following comparison operators:

Operator | Description | Example
---------|-------------|--------
== | checks if value of two operands are equal or not. If yes then returns true | (10 == 20) is not true; hence returns false
!= | checks if the value of 2 operands are equal. If values are not equal, returns true | (a != b) is true; hence returns true
> | checks if value of left operand is greater than value of right operand. If left operand is greater than right operand, returns true | (10 > 20) is not true hence returns false
< | checks if value of left operand is less than value of right operand. If left operand is less than right operand, returns true | (10 < 20) is true
>= | checks if value of left operand is greater than or equal to value of right operand. If it is, returns true; else returns false | (10 >= 20 ) is false
<= | checks if value of left operand is less than or equal to right operand. If left operand is less than or equal to right operand, returns true; else returns false | (10 <= 20) is true

####STRICT AND LOOSE COMPARISON OPERATORS
In JavaScript, in addition to the regular comparison operators, we have **strict comparison operators**; which are strict equals (`===`) and strict not-equals (`!==`).

####LOOSE COMPARISON OPERATORS
**Loose comparison operators** ( `==` and `!=` ) are quite liberal - they convert type to match one another. This means that values may be considered equal even if they are different types, since the operator will force coercion of one or both operators into a single type (usually a number) before performing a comparison.

####STRICT COMPARISON OPERATORS
On the other hand, **strict comparison operators** (`===` and `!==` ) compare both type and value equality.

Here is an Example:

```javascript
20 == '20' //=> true
20 === '20' //=> false
```

The following is written in all caps to stress its importance; it's qualified in mixed case to note the one minor exception.

**YOU ALMOST ALWAYS WANT TO USE `===` or `!==`.**

Relying on JavaScript's implicit type-casting is inviting subtle and hard to track bugs into your code. Use `===` and `!==`. The only exception is when you're dealing with a value that might be either `null` or `undefined` but can also be `0` or `false`. In this case, a simple `if (variable) { ... }` won't work, because all of the above are false-y. But we can check `if (variable == null) { ... }` or `if (variable != null) { ... }` to catch `null` and `undefined` but not `0` and `false`.

##Intro To Flow Control in JS
####OBJECTIVES
+ Write if-statements in JS
+ Write if-elsif-else statements in JS
+ Use the ternary operator in JS
+ Write switch statements in JS

####ABOUT
Flow Control allows the execution of code only under certain conditions. In Ruby, we used if statements, if/else statements, if/elsif/else statements, ternary operators, and case statements to control what code runs when. JavaScript has similar methods to control what blocks of code to execute: if statements, if/else statements, if/else if/else statements, ternary operators, and switch statements.

####IF STATEMENTS
An if statement evaluates the code wrapped in parenthesis to either true or false. If `true`, the code block is executed. If false, nothing is executed.

JS Syntax:

```javascript
if (conditionToTestIsTrue) {
  // code to be executed here
}
```

Let's write a function `basicTeenager` that accepts an age as a parameter. The function should contain an if-statement that checks to see if the age is a teenager. If the age is a teenager, it should return `"You are a teenager!"`

```javascript
function basicTeenager(age) {
  if (age >= 13 && age <= 19) {
    return "You are a teenager!";
  }
}
```

####IF/ELSE STATEMENTS
You will often see an `If` statement used in combination with an `else` clause. An `else` clause is a fallback to an `if` statement and will only get executed if the previous if statement is false.

Syntax:

```javascript
if (conditionToTestIsTrue) {
  // condition is false hence code is not executed
} else {
  // code to be executed because previous condition is false
}
```

+ Define a function `teenager` that accepts an age as a parameter. If the age is between 13-19 it should return `"You are a teenager!"`. Otherwise, the function should return `"You are not a teenager"`.

```javascript
function teenager(age) {
  if (age >= 13 && age <= 19) {
    return "You are a teenager!";
  } 
  else {
    return "You are not a teenager";
  }
}
```

####IF/ELSE IF STATEMENTS
`if` statements can also be combined with an `else if` clause. This is like an else statement, but with its own condition. It will only run if its condition is true, and the previous statement's condition was false.

**Note**: An important thing to keep in mind when transitioning to JavaScript from Ruby is that the `elsif` becomes two full words: `else if`.

Syntax:

```javascript
if (conditionToTestIsTrue){
    // condition is false hence code is not executed
} else if (thisConditionIsTrue) {
  // execute this code if previous statement is false
} else {
  // execute this code if the 2 conditions above are false
}
```

+ Define a function `ageChecker` that takes in an age as a parameter. If the age is between 13-19 it should return `"You are a teenager!"`. If the age is 12 or below, it should return `"You are a kid"`. If the age is above 19, it should return `"You are a grownup"`

```javascript
function ageChecker(age) {
  if (age >= 13 && age <= 19) {
    return "You are a teenager!";
  } 
  else if (age <= 12) {
    return "You are a kid";
  } 
  else {
    return "You are a grownup";
  }
}
```

####TERNARY OPERATOR
The ternary operator is used as a shortcut for the `if-else` statement. You've probably seen it before in Ruby looking something like this:

```ruby
cart = ["graphic t-shirt", "aluminum water bottle"]
 
puts cart.empty? ? "Please add something to your cart." : "You're ready to check out." 
 
# Above prints:
# You're ready to check out.
```

This operator tests a condition; if the condition is true, it returns a certain value, otherwise it returns a different value:

Syntax:

```javascript
conditionToTest ? valueToBeReturnedIfTrue : valueToBeReturnedIfFalse
```

+ Define a function `ternaryTeenager` that accepts age as a parameter. The body of the function should use the ternary operator to return `"You are a teenager"` if age is between 13-19 and returns `"You are not a teenager"` if the age is anything else.

```javascript
function ternaryTeenager(age) {
  return (age >= 13 && age <= 19) ? "You are a teenager" : "You are not a teenager";
}
```

####SWITCH STATEMENTS
Switch statements acts like a big if/else if/else chain. The switch expression is evaluated once and the value of the expression is compared with the values of each case. If there is a match, the associated block of code is executed.

Syntax:

```javascript
switch (expression) {
  case n:
      // code to be executed if case n is true
      break; // break out of switch statement once code executed
  case m:
      // code to be executed if case m is true
      break; // break out of switch statement once code executed
  default:  // all other cases
      // code to be executed if case n and case m false
}
```

Example:

```javascript
var mood = "hungry"
switch(mood){
  case "happy":
    console.log("Dance to Pharrel's Happy");
    break;
  case "sad":
    console.log("You should eat a pint of icecream");
    break;
  case "anxious":
    console.log("Take some deep breaths");
    break;
  case "hungry":
    console.log("You should eat a big chocolate cake");
    break;
  default: 
    console.log("That's not a mood we support");
}
```

In the example above, we'll see `"You should eat a big chocolate cake"` printed to the console. If we change the value of the `mood` variable to `sad` you'll see `"You should eat a pint of icecream"`. If the value of `mood` changed to `"grumpy"`, the default statement would trigger and print out `"That's not a mood we support"`.

+ Define a function `switchAge` that accepts an age as a parameter. The case statement should switch on `age` and return `"You are a teenager"` if the age is 13, 14, 15, 16, 17, 18, or 19, and return `"You have an age"` as the default.

```javascript
function switchAge(age) {
  switch(age) {
    case 13:
    case 14:
    case 15:
    case 16:
    case 17:
    case 18:
    case 19:
      return "You are a teenager";
      break;
    default:
      return "You have an age";
      break;
  }
}
```

####RESOURCES
+ [Codecademy - if/if else/if else if else](http://www.codecademy.com/glossary/javascript/if-statement)
+ [MDN - if..else](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else)
+ [Codecademy - Ternary Operator](http://www.codecademy.com/glossary/javascript/ternary-operator)
+ [Codecademy - Switch Statements](http://www.codecademy.com/glossary/javascript/switch-statements)

##Intro to Arrays in JS
####OBJECTIVES
+ Explain what an array is and why we use it
+ Create an array
+ Add an element to an array
+ Access an element in an array
+ Replace an element in an array
+ Delete an element from an array
+ Iterate over an array

####CREATION
JavaScript arrays can contain any types of values and they can be of mixed types, just like in Ruby. You can create arrays in two different ways, the most common of which is to list values in a pair of square brackets. These are called array literals.

Syntax:

```javascript
var arrayName = [element0, element1, ..., elementN];
```

Examples:

```javascript
var primeNumbers = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37];
 
var tvShows = ["game of thrones", "true detective", "the good wife", "empire"];
 
var weirdGreeting = [ "he", 110, "w", 0, "r", {"1":"d"} ];
 
var empty = [];
```

The Array constructor is another approach to making a new JavaScript array.

```javascript
var evenNumbers = new Array();
```

+ Define a function called `createArray`. The function does not need to accept an argument, but should return an array of the strings `snickers`, `hundred grand`, `kitkat`, and `skittles`.

```javascript
function createArray() {
  var hellsYeah = ["snickers", "hundred grand", "kitkat", "skittles"];
  return hellsYeah;
}
```

####ADDING AN ELEMENT
Ruby has two ways to add elements to the end of the array, `push` and `<<`. You're probably more familiar with the latter which is also known as the shovel method. JavaScript doesn't have the shovel method, but like Ruby, it does feature a push method. Take a look at the code below:

```javascript
var superheroines = ["catwoman", "she-hulk", "mystique"];
 
superheroines.push("wonder woman");
// superheroines is now ["catwoman", "she-hulk", "mystique", "wonder woman"]
```

To add elements to an array at specific indexes, you use the bracket equals notation. This notation will also work for replacing values.

Let's add three elements to our empty `evenNumbers` array. First, decide what index you want your element to have (remember the first element in an array has an index of 0). Then you wrap this desired index in brackets, place the array's variable name directly to the left. Remember to place an equal sign to the right of the closing square bracket, and put the value that you want your element to have after the equal sign.

```javascript
var evenNumbers = new Array();
 
evenNumbers[0] = 2;
evenNumbers[1] = 4;
evenNumbers[3] = 8;
 
// evenNumbers is now [ 2, 4, undefined, 8 ]
```

Notice that since we didn't tell JavaScript what value we wanted the third element to have, it defaulted to `undefined`.

+ Define a function `addElementToArray`, that accepts an array as a parameter. The function should add `"A Christmas Story"` to the end of the array. The function should return the entire array.

```javascript
function addElementToArray(array) {
  array.push("A Christmas Story");
  return array;
}
```

####ACCESSING AN ELEMENT
You can get elements out of arrays if you know their index. Just like in Ruby, array elements' indexes start at 0 and increment by 1, so the first element's index is 0, the second element's index is 1, the third element's is 2, etc.

```javascript
var entrepreneurs = ["Elizabeth Holmes", "Laurene Powell Jobs", "Arianna Huffington"];
 
// the line below will print the string "Elizabeth Holmes"
console.log(entrepreneurs[0]);
 
// the code below will print the string "Arianna Huffington is the co-founder and editress-in-chief of The Huffington Post"
var bio = " is the co-founder and editress-in-chief of The Huffington Post";
console.log(entrepreneurs[2] + bio);
 
// the line below will return undefined
entrepreneurs[9];
```

+ Define a function `accessElementFromArray`. The function should accept an array of states as an argument and return the third item in the array.

```javascript
function accessElementFromArray(array) {
  return array[2];
}
```

####REPLACING AN ELEMENT
Replacing the value of an element in a JavaScript array is very similar to the equivalent in Ruby. Say you have an array of author names, and you would like to replace the second element, J. D. Salinger, with the string "Harper Lee". Since the second element has an index of 1, you simply reassign using the index number:

```javascript
var authors = ["ray bradbury", "j. d. salinger", "maya angelou"];
 
authors[1] = "harper lee";
// authors is now ["ray bradbury", "harper lee", "maya angelou"];
```

+ Define a function `replaceElementInArray` which should accept an array of names as an argument. The function should replace the second item in the array with "Carter". The function should return the replaced item from the array.

```javascript
function replaceElementInArray(array) {
  array[1] = "Carter";
  return array[1];
}
```

####REMOVING AN ELEMENT
To remove an element in JavaScript, you can call on the `splice()` function. This function takes two required arguments and some optional arguments:

1. index to start at (required)
2. number of elements to remove (required)
3. an element to add to the array (optional)
4. an element to add to the array (optional)
5. etc.

```javascript
var myFish = ['angel', 'clown', 'drum', 'mandarin', 'surgeon'];
 
// removes 1 element from index 3
var firstRemovedFish = myFish.splice(3, 1);
 
// myFish is now ['angel', 'clown', 'drum', 'surgeon']
// firstRemovedFish is ['mandarin']
 
// removes 1 element from index 2, and inserts 'trumpet'
var secondRemovedFish = myFish.splice(2, 1, 'trumpet');
// myFish is ['angel', 'clown', 'trumpet', 'surgeon']
// secondRemovedFish is ['drum']
 
// removes 2 elements from index 0, and inserts 'parrot', 'anemone' and 'blue'
var removedFishes = myFish.splice(0, 2, 'parrot', 'anemone', 'blue');
// myFish is ['parrot', 'anemone', 'blue', 'trumpet', 'surgeon']
// removedFishes is ['angel', 'clown']
```

+ Define a function `removeElementFromArray` that take an array of dishes as a parameter. The function should remove the third and fourth item from the array, and adds "Roast Chicken" to the end, using the `splice` function. The function should return the updated array.

```javascript
function removeElementFromArray(array) {
  array.splice(2, 2, "Roast Chicken");
  return array;
}
```

####ITERATION
JavaScript's `forEach` function will help you to iterate through an array. The forEach method executes a provided function once per array element. The first argument that this provided function needs is the variable name for currentValue. In Ruby, this value is typically seen inside of the double pipes (ex. `letters.each do |letter|`).

```javascript
var letters = ["z", "y", "x", "w", "v", "u", "t", "s"];
 
letters.forEach(function(letter) {
  console.log("♫ " + letter + " ♬");
});
// this will print the following to the console:
// ♫ z ♬
// ♫ y ♬
// ♫ x ♬
// ♫ w ♬
// ♫ v ♬
// ♫ u ♬
// ♫ t ♬
// ♫ s ♬
```

Like Ruby's `.each_with_index` method, you can also instruct JavaScript to keep track of the index number of the element it is currently on. To do this, pass the provided function a second argument:

```javascript
var letters = ["z", "y", "x", "w", "v", "u", "t", "s"];
 
letters.forEach(function(letter, index) {
  var number = index + 1;
  console.log(number + ". " + letter);
});
// this will print the following to the console:
// 1. z
// 2. y
// 3. x
// 4. w
// 5. v
// 6. u
// 7. t
// 8. s
```

+ Write a function `iterateArray` that accepts an array of numbers as a parameter. The body should also contain an empty array, stored in the variable `newNums`. You should iterate over the array of numbers, add 5 to each number, and store the larger number in the array `newNums`. The function should return the `newNums` array.

```javascript
function iterateArray(array) {
  var newNums = [];
  array.forEach(function(number) {
    newNums.push(number + 5);
  });
  return newNums;
}
```

####ARRAY WACKINESS
It's important to remember that arrays in JavaScript are kind of wonky. You can assign properties to them:

```javascript
var array = [1, 2, 3];
 
array.myProperty = "I'm a property!";
```
 
Which can lead to weird behavior:

```javascript
array;
// [1, 2, 3];
 
// Where did our property go?
array.myProperty;
// "I'm a property!";
 
array.length;
// 3 - Would you have expected 3 or 4?
```

We don't tend to do these kinds of things on purpose, but it's important to be aware that they can happen so that you have a good sense of where to look if/when strange bugs start to appear.

####RESOURCES
+ [MDN - Arrays](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
+ [Codecademy - Arrays](http://www.codecademy.com/glossary/javascript)

##Intro to Looping in JS
####OBJECTIVES
+ Build a for loop
+ Build a while loop
+ Build a do-while loop
+ Explain the purpose of a loop
+ Explain the difference between each type of loop in JS

####INTRODUCTION
Sometimes, we need to do things repeatedly. Let's say we have a bunch of gifts to wrap. They all happen to be the same size and shape, so for every gift, we need to cut a similarly sized piece of wrapping paper, fold it up over the edges of the gift, tape it together, and add a nice little card. Then we set the wrapped gift aside and moved onto the next gift.

In programming terms, we can think of the gifts as an array and the act of wrapping them as a function. We could, of course, write the following code:

```javascript
var gifts = ['teddy bear', 'drone', 'doll']
 
function wrapGift(gift) {
  console.log(`Wrapped ${gift} and added a bow!`)
}
```

We could then call `wrapGift()` on each gift individually:

```javascript
wrapGift(gifts[0])
wrapGift(gifts[1])
wrapGift(gifts[2])
```

But if we had more gifts, we'd have to write out more calls to `wrapGift()` — it'd probably get tiring after a while.

This is where loops come in handy! With a loop, we can just write the repeated action once and perform the action on every item in the collection.

####ABOUT
Loops are used to execute the same block of code a specified number of times. JavaScript loops come in a few different flavors — namely, `for`, `while`, and `do-while`. We'll cover each of these kinds of loop below.

####THE `FOR` LOOP
Of the loops in JavaScript, the `for` loop is the most common. The `for` loop is made up of four statements and has the following structure:

####SYNTAX

```javascript
for ([initialization]; [condition]; [iteration]) {
  [loopBody]
}
```

+ **Initialization**
	+ An expression (including assignment expressions) or variable declaration. Typically used to initialize a counter variable. This expression may optionally declare new variables with the var keyword
	
+ **Condition**
	+ An expression evaluated before each loop iteration. If this expression evaluates to true, statement is executed

+ **Iteration**
	+ A statement executed at the end of each iteration. Typically, this will involve incrementing or decrementing a counter and thus bringing the loop ever closer to its end
+ **loopBody**
	+ Code which runs on every iteration as long as condition evaluates to true

> Use `for` loop when you know how many times you want the loop to run (for example, when you have an array of known size)

####EXAMPLE
The code below will print the string "Hello World!" 99 times

```javascript
// i is set to equal to 1
// as long as i is less than 100 execute the code in the loopBody
// - which is print "Hello World"; increment i each time code in loopBody is executed
 
for (var i = 1; i < 100; i++) {
  console.log( "Hello World the " + i + " time" );
}
 
// The above prints:
// Hello World the 1 time
// Hello World the 2 time
// Hello World the 3 time
```

You'll encounter `for` loops again when you learn about iterating through object literals.

**TODO**: Build a function `forLoop`. It takes an array as an argument. Start counting from 0, and, using a `for` loop, add a string to the array. But not just any string. If your `i` value is `1`, add the string `"I am 1 strange loop."`; but if your `i` value is anything else, add the string `"I am ${i} strange loops."` to the array 25 times. (Remember flow control with `if` and `else`? And how do we *interpolate* `i`?) Then return the array.

**HINT**: Your `for` loop could look something like this:

```javascript
for (let i = 0; i < 25; i++) {
  // ...
}
```

####THE `WHILE` LOOP
The `while` loop similar to an if statement, except that its body will keep executing until the condition evaluates to false. Has the following structure:

**SYNTAX**

```javascript
while ([condition]) {
  [loopBody]
}
```

A `while` loop is best used when we don't know how many times your loop needs to run - that is, your condition is dependent on a dynamic function/return value

**EXAMPLE**

```javascript
function maybeTrue() {
  return Math.random() >= 0.5
}
 
// run until `maybeTrue()` returns `false`
// (so the body of the loop might _never_ run!)
while (maybeTrue()) {
  console.log("And I ran, I ran so far away!");
}
```

In this example, `maybeTrue()` returns `true` 50% of the time, and our loop runs until `maybeTrue()` returns `false`. We've used a `while` loop because we don't have any specific number to count up or down to in our loop — we just want it to run until the condition is no longer met.

But we can also use a `while` loop in place of a `for` loop — we just have to remember to change the condition on each pass so that the loop terminates (otherwise it will run forever).

```javascript
let countdown = 100;
 
while (countdown > 0) {
  console.log(--countdown)
}
```

**TODO**: Create a function called `whileLoop` in `loops.js`. The function should take a number as an argument. Using a `while` loop, count down (using `console.log`) from the passed in number to 0. Then return the string `'done'`.

####THE DO-WHILE LOOP
The `do-while` loop is almost exactly the same as the while loop, except for the fact that the loop's body is executed at least once before the condition is tested. Has the following structure:

**SYNTAX**

```javascript
do {
  [loopBody];
} while ([condition]);
```

You will rarely see `do-while` used since very few situations require a loop that blindly executes at least once. That being said, take a look at the example below:

**EXAMPLE**

```javascript
function maybeTrue() {
  return Math.random() >= 0.5
}
 
do {
  console.log('doo-bee-doo-bee-doo')
} while (maybeTrue());
```

Remember how we couldn't be sure with the plain `while` loop above that the body would run using `maybeTrue()`? With `do`, we *can* be sure that the body will run!

**TODO**: Define a function called `doWhileLoop` in loops.js. The function should take an array as an argument. Use the maybeTrue() function (you can copy it from this README) as the condition, and remove elements from the array until the array is empty or until maybeTrue() returns false. (Your condition might look something like array.length > 0 && maybeTrue().) Finally, return the array.
CONCLUSION
If seeing all of these new loops all at once is freaking you out, take a deep breath. Remember, 98% of the time you will want to use a for loop. A general heuristic for choosing which loop, is try a for. If using for doesn't serve your purposes, then go ahead and try a different loop. Also remember that you can always refer to documentation on these loops at any time. After some time coding in JavaScript, writing a for loop will come as naturally to you as wrapping one gift after another.
#jQuery
##JS Libraries
####OBJECTIVES
+ Explain what a JS library is
+ Explain why jQUery was invented
+ Include jQuery in an HTML page

####INTRO
A JavaScript library is prewritten JavaScript code that helps you streamline your own code base.

There are many JavaScript libraries designed for different purposes. [Jasmine](http://jasmine.github.io/) is a library for writing tests. [D3.js](https://d3js.org/) is a library for data visualization. [Bootstrap](http://getbootstrap.com/) has a JavaScript library for adding animations and effects to our sites.

One of the most widely used JavaScript libraries is [jQuery](https://jquery.org/).

####THE PROBLEM WITH JAVASCRIPT
As you've learned from CSS, not all browsers are created equal. There are properties specific to each browser, and code you have to write so that designs and features are cross-browser compatible. Even though things have improved drastically from the earlier versions of browsers (Internet Explorer 6, etc.), we still have to pay attention to browser differences.

JavaScript has evolved in much the same way as CSS. Even though things have improved considerably, there are definitely differences in the way JavaScript must be handled across browsers.

Let's say we want to use JavaScript to add a CSS property to an HTML element.

We'll be working with the following HTML (which is also in `html/index.html` for you to test yourself with the console) for this example.

```html
<!doctype html>
<head>
  <title> JavaScript Madness</title>
</head>
<body>
  <p id="hey">HEY HEY HEY HEY HEY</p>
</body>
```

This HTML renders as a white page with black text displaying `"HEY HEY HEY HEY HEY"` in the top left corner of the page.

Let's say we want to change the font color to blue and the background color of the `p` tag to gray using JavaScript. We could do that with the following JavaScript:

```javascript
document.getElementById("hey").style.color = "blue";
document.getElementById("hey").style.backgroundColor = "#ccc";
```

`document` is the JavaScript object that represents the HTML document that gets loaded to render the page. We can use JavaScript functions to select and manipulate different HTML elements via `document`.

The functions `getElementById()` selects an HTML element by an ID. In this case we're passing in the ID `"hey"` which will select the `p` tag on our page. We're then using the JavaScript `style.property = "value"` function which allows us to change the CSS of an element.

Now that we're familiar with using JavaScript to change the CSS, let's try to float the text to the right side of the page.

For earlier versions of Firefox and Chrome, you could achieve that with the following JS:

```javascript
document.getElementById("header").style.cssFloat = "left";
```

But this code wouldn't work with earlier versions of Internet Explorer. Instead, you would have to write:

```javascript
document.getElementById("header").style.styleFloat = "left";
```

This need for different JavaScript code for different browsers can get very messy very quickly. Suddenly you need crazy if-statements and browser detection to trigger different lines of code for each browser, all just for a simple CSS change.

####JQUERY TO THE RESCUE
jQuery was created to solve this problem of needing different lines of code for different browsers. If you include the jQuery library in your code, you can write one single line of code that handles each browser on its own. AMAZING!

jQuery provides us with immense convenience methods that make our jobs as developers much easier, especially when dealing with AJAX and API calls. We can write significantly fewer lines of code and still achieve the same effect. jQuery is also an open source library and has a tremendous community around it. At the time of this writing, over [78% of the top million sites on the web use jQuery](http://trends.builtwith.com/javascript/jQuery) - crazy! There are many many jQuery plugins that provide additional functionality and that allow us to build complex applications very quickly.

##Modifying HTML with jQuery
####OBJECTIVES
+ Load jQuery in HTML file
+ Write inline jQuery to modify HTML
+ Explain how jQuery modifies HTML

####INLINE JQUERY
We're going to use jQuery to add some text to our HTML page.

####INCLUDE JQUERY LINK
This lesson doesn't render as a lab, but there are files within this repository you'll need to code along. Click on the Github icon in Learn, and fork and clone this repository.

In order to start writing jQuery, we need to include the library in our HTML. One way to do this would be to download a copy of the jQuery library and include it with our project. We can also link to the library hosted by a content delivery network, or CDN. For this example, we'll be loading jQuery from Google's CDN. Copy the code below and paste it `html/index.html` at the very bottom of the HTML `body` (right above the `</body>` close tag). This script tag links our HTML file to the jQuery library.

```html
<script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.3/jquery.min.js"></script>
```

####ADDING TEXT
Go ahead and open `html/index.html` in the browser. You should see a really boring looking website with the text `yo yo yo yo yo yo yo`.

We want to add the text `hey hey hey hey!!!!!` to end of our paragraph.

Below our `script` tag that loads jQuery, right before the closing `body` tag, we'll want to add an opening and a closing `script` tag:

```html
<script>
</script>
```

Between these tags is where we want to write our inline jQuery. The script tags need to be at the bottom of the page because the code we're going to write is dependent on the `p` tag being already loaded in the browser. Our jQuery will error if there isn't a `p` tag to add text to.

And now, in between the script tags, add the following code:

```javascript
$("#yo").append("hey hey hey hey!!!!!");
```

Save the changes to `html/index.html` and reload in the browser. You should see `yo yo yo yo yo yo yo hey hey hey hey!!!!!` on the page!!

The `$` is just a function — it's equivalent to `jQuery` (which is also a function that you can call). You might think of it as a fancy alias (with a few tricks up its sleeve) to `document.querySelectorAll`.

`"#yo"` is our jQuery selector -- we're selecting the HTML element with the ID `yo`. We're then using the jQuery append function, which adds text to an HTML element, and we're passing in `"hey hey hey hey!!!!!"` which is the text we want to add.

Don't worry too much about the mechanics of these selectors and functions, we'll go over those in way more detail. Just notice that the text appeared on the screen, even though we didn't explicitly add it between the `p` tags.

##Document Ready
####OBJECTIVES
+ Set up a call to `document.ready()`
+ Explain why `document.ready` is important

####INTRODUCTION
We don't ever want to write our JavaScript and jQuery inside our HTML files. For the same reasons that we want to separate out our CSS from our HTML, we want to separate out our JavaScript from our HTML, too.

But so far we've written our JavaScript code at the bottom of our HTML `<body>` so that the code would run once the page loads. How can we run our code when it's in a totally different file? We need to guarantee that the HTML document is loaded before our other files are triggered.

####SEPARATING AND LINKING CODE
This lesson doesn't render as a lab, but there are files within this repository you'll need to code along. Click on the Github icon in Learn, and fork and clone this repository.

If you take a look at `index.html`, you'll notice we have jQuery-flavored JavaScript code written at the bottom. Our goal is to refactor this site to move that code out into `script.js`.

The first thing we need to do is load `script.js` in `index.html`. In the olden days (the 1990s and 2000s), we used to import our scripts in the `<head>` of our HTML documents. As our applications grew more interactive, our JavaScript files grew larger and our pages took longer to load. This was because the browser loads *everything* in between the `<head>` tags before it attempts to render the page. Once the browser gets to `<body>`, it starts to load things in order (synchronously).

When that's just painting tags with the appropriate styles, the browser simply chugs along; but when it encounters a `<script>` tag, it either needs to evaluate the script or else make a request to the location specified in the `<script>` tag's `src` attribute. These requests take time, so nowadays, we put all of our `<script>` tags at the bottom of `<body>`, below all of the static HTML content. Go ahead and add a `<script>` tag for "script.js" at the bottom of `<body>`:

```html
<script src="script.js"></script>
```

Now that our HTML file can find our JavaScript File, let's remove the code between the `script` tags from the bottom of our HTML file and move it to `script.js`.

In this simple example, you should already see `"this is so freaking cool."` appended to `div#text`. But normally, it's not safe to execute JavaScript code until the browser tells us it's ready.

####DOCUMENT READY
Thankfully, the browser has a built-in way to determine when a page is loaded. You'll be coding along in `index.html` and `script.js`.

In `script.js`, we need to set up a document ready in order to detect when our HTML page has loaded, and the document is ready to be manipulated:

```javascript
$(document).ready(function() {
  // code to be executed goes here
});
```

The `$` is a shortcut for `jQuery`, and provides an interface to the library. Every time you see `$`, think `jQuery`.

Once the `load` event fires (which we've told jQuery to listen for with `$(document).ready()`), the rest of the code will fire. Place the document ready around the jQuery already in `script.js`. Save your changes, and refresh in the browser. You should see the text appear in the browser!

####RESOURCES
+ [Learn jQuery](http://learn.jquery.com/using-jquery-core/document-ready/)
+ [jQuery Docs](https://api.jquery.com/ready/)

##The DOM
####OBJECTIVES
+ Explain what the DOM is
+ Explain the DOM hierarchy
+ Explain how to access the DOM with JavaScript

####INTRO
Let's think about Gmail for a second. When you're looking at your inbox, there are a million actions you can take. You favorite an email and the star turns yellow without a new page loading. You add a tag to an email and the tag also appears without the page reloading. Even opening an email just replaces the inbox content with the email content without loading a new page. All of this is done using JavaScript. But how was the code able to manipulate the HTML? By using, traversing and manipulating the DOM.

The DOM, which stands for Document Object Model, provides a way of manipulating HTML and XML documents. (We call this "way of manipulating" things an Application Programming Interface, or API — but more on those later.) The DOM provides a structural representation of the document in tree form, enabling you to modify its content and visual presentation by using a scripting language such as JavaScript.

####THE TREE
![Dom Structure Tree](https://s3.amazonaws.com/learn-verified/dom-tree.gif)

####WINDOW
+ The highest level of the DOM tree is the `window` object. Think of the window as the browser window. The `window` contains the entire DOM document. All components of your HTML file will be accessible from within the window.

+ The `window` object has a large number of properties that return information about the object. Below are a few examples:

```javascript
window.document;
//returns the entire HTML document
 
window.innerWidth;
// returns the inner width of the browser window. Open a console in the browser and enter this. Then shrink the browser window and run it again. You should get a different value.
 
window.innerHeight;
// returns the inner height of the browser window.
```

####DOCUMENT
+ The `document` object represents any web page loaded in the browser. The `document` contains all the nodes that represent the HTML elements of the page. We use the `document` object to traverse through the HTML and manipulate elements.

+ There are many `document` properties that allow us to obtain information about the `document` programmatically.

```javascript
document.all;
// returns all the nodes inside the document object
 
document.contentType;
// returns the type of content contained. Most web pages should return "text/html"
 
document.URL;
// returns the URL of the document object
```

Below the document, are all the nodes representing the HTML or XML elements on the page.

####ALTERING THE DOM
We can alter the DOM through several different ways:

1. Add/remove HTML elements in the page.
	+ You can add elements with functions like `appendChild`.
	+ You can remove elements with the similarly named `removeChild`.
	+ Both of these functions can be called on any node in the DOM tree.

2. Add/remove/change HTML attributes.
	+ If you have a DOM node called `element`, `element.attributes` gives you access to its attributes.
	+ You can remove attributes with `removeAttribute`.

3. Add/remove/change CSS styles.
	+ You can modify any DOM node's style property.

4. Listen for key presses or mouse events on Elements.
	+ You can add a listener directly using `addEventListener`. Elements emit an [extensive array](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener) of events.

5. Create events in the page.

####SELECTING ELEMENTS
We can select HTML elements in the document with JavaScript and jQuery:

JS:

```javascript
document.getElementsByTagName("p");
//returns all p tags on a page
 
// alternatively, we could do
document.querySelectorAll('p');
 
// the results of these two functions
// are the same in this example, but as
// we'll see later, getElementsByTagName
// and querySelectorAll have different uses
```

Lots of developers use a library called [jQuery](https://jquery.org/) for interacting with the DOM. The jQuery library provides a `jQuery` function (aliased as `$`), which wraps `document.querySelectorAll()` and adds a bit of magic.

We could write the above example in jQuery as

```javascript
$("p");
```

The DOM will become increasingly important as we use JavaScript and jQuery to manipulate our sites.

####RESOURCES
+ [MDN DOM Introduction](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction)
+ [MDN Document Properties](https://developer.mozilla.org/en-US/docs/Web/API/Document)
+ [MDN EventTarget.addEventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)

##jQuery Selectors
####OBJECTIVES
+ Explain the purpose of a DOM selector
+ Use a DOM selector to select an HTML element

####INTRO
Because jQuery was created as a way to make DOM manipulation quick and painless, a big part of that is correctly selecting the DOM node you want to work with. Thankfully, the browser provides a seamless way to select the correct HTML, similar to CSS selectors.

When we're using jQuery, we can wrap these selectors (which are just strings) in a call to `$` — for example, if we wanted all the `div`s on a page, we could call `$('div')`.

You will be coding your solution in `index.html` and `js/selectors.js`. There are tests for this walk-through to make sure your code works as expected.

####GETTING EVERYTHING SET UP
First things first, we need to load several external resources to `index.html`:

+ jQuery: `<script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.7/jquery.min.js"></script>`
+ Our JS file: `<script src="js/selectors.js"></script>`

Both of these script tags should go at the bottom the `body`. Order here matters, if we plan on using jQuery in `js/selectors.js` that file needs to be linked first.

####USING SELECTORS
To set up a jQuery selector, you start with the `$`. Every line of jQuery code should start with the `$`.

```javascript
$('selector-goes-here')
```

In between the quotation marks, you place your selector. Just like in CSS, there are many types of selectors. Let's dive into a few.

You'll want to have `index.html` open in the browser, with the console open.

####ELEMENT SELECTOR
The element selector returns all HTML elements with that specific tag:

```javascript
$('img')
```

Because we have six images on our page, this selector will return an array of all six images.

####CLASS SELECTORS
You'll notice `index.html` has two `divs` with the class `pics`. Let's use selectors to select those two divs.

```javascript
$('.pics')
```

Just like in CSS, we use a `.` to denote a class. This code is selecting any HTML elements with the class `pics`, which in this case is two different divs.

####ID SELECTORS
The first gif on the page is a baby doing ninja moves. An ID selector works in much the same way as a class selector, you just replace the `.` with a `#`:'

```javascript
$('#baby-ninja')
```

####DESCENDANT SELECTOR
Let's say we want to select all list items inside the `ul` tag. The `li` tags are descendants of the `ul` tag. We can use a descendant selector like this:

```javascript
$('ul li')
```

####FIRST CHILD
Let's say we want to select the first `img` tag inside each `div`. We could do that with the first child selector. You set up this selector:

```javascript
$('parent-tag child-tag:first-child')
```

Because we want to select the first image inside a `div`, we put `div` first. Next comes the element we want to select, which is `img`, followed by `:first-child`:

```javascript
$('div img:first-child')
```

**Example**
Write a function firstListItem which does not accept any parameters. The function should use a first-child selector to return the first list item in the ul with the ID pic-list.

```javascript
function firstListItem() {
  return $('ul#pic-list li:first-child');
}
```

####ALT TAG SELECTOR
Let's say we want to select an image that has a specific alt text. The second image on our page (or the beatles) has the alt text `"the beatles making faces"`. We can use that text to find the image:

```javascript
$("img[alt='the beatles making faces']")
```

####LAST SELECTOR
Let's say you want to select the last div on the page. You could use the last selector to do that:

```javascript
$('div:last')
```

####OTHERS
There are a tremendous number of other DOM selectors, including but not limited to last-child, nth-type-of, next sibling, input selector, and the list goes on. Take a look at the [MDN docs](https://api.jquery.com/category/selectors/) for a full list.

####RESOURCES
+ [MDN jQuery Selectors](https://api.jquery.com/category/selectors/)

##jQuery Methods
####OBJECTIVES
+ Explain what a jQuery method is
+ Use jQuery methods to manipulate UI elements

####INTRO
Now that we know about jQuery selectors, we know how to select HTML elements to be able to manipulate them. But how do you actually start doing the manipulating? Enter jQuery methods, a quick and easy way to apply effects to DOM elements.

####ATTRIBUTE METHODS
Attribute methods retrieve the value of a DOM attribute and set that value based on a specific parameter. There are a ton of attribute methods, including (but not limited to `addClass`, `removeClass`, `toggleClass`, and `val`)

####ADDCLASS
The `addClass()` function is called on a specific HTML element, and adds a class (passed in as a parameter) to that element.

Given the following HTML:

```html
<p> This is my paragraph</p>
```

And this CSS:

```css
.myBorder {
  border-color: blue;
  border-width: 5px;
  border-style: dotted;
}
```

In our HTML, we never applied the class `myBorder` to our `p` tag. But let's say we wanted to do that programmatically with jQuery:

```javascript
$("p").addClass("myBorder").
```

We use `$("p")` as our selector to select the `p` tag, and then we call the `addClass` function, and pass it the paramater `"myBorder"`.

Our resulting HTML will look like this:

```html
<p class="myBorder"> This is my paragraph</p>
```

####VAL
Let's say you're building a form for an online order. Typically, you give your user an option to check if their shipping address is different than their billing address. If they click the box that they are different addresses, then they would expect an additional form to fill out for shipping address. In order to achieve this, you would want to check the `value` of the checkbox, and trigger the form to be filled out if it is checked with the `val` function.

The `val()` function returns the `value` attribute for an HTML element.

Given the following HTML:

```html
<form>
  <input type="checkbox" value="choice a">Choice A
  <input type="checkbox" value="choice b">Choice B
</form>
```

We can select the value of an input with:

```javascript
$('input').val();
```

It's important to note that this function returns the very first match, so it would find the first input field and return `"choice a"`.

We could change our selector to look for the selected checkbox:

```javascript
$('input:checked').val();
```

If you wanted to change the value of the input to "choice Z" instead of `"choice a"`, you would just pass a parameter to the val function:

```javascript
$('input').val("choice Z");
```

####CSS METHODS
This group of jQuery methods deals with retrieving and setting CSS properties of HTML elements. These methods include `addClass`, `hasClass`, `removeClass`, `width`, `scrollLeft`, and many more.

####HASCLASS
If you wanted to trigger an event based on a class already set on a property, you could use `hasClass` to check if that class exists. If it does, it will return `true`.

Given the following HTML:

```html
<ul class="favList">
  <li>Chocolate</li>
  <li>Puppies</li>
  <li>Gold</li>
</ul>
```

We could check if this list has the class `favList` by passing it as an argument to `hasClass`:

```javascript
$('ul').hasClass('favList')
```

####EFFECTS
We can use jQuery to add special effects (like animations) to our web page.

####ANIMATE
The animate function allows you to create a custom animation on any HTML element. The function accepts a CSS object as a parameter.

Let's say we have an image of a bird on our website that we want to get bigger, we could achieve that with the `animate` function:

**HTML**:

```html
<img src="images/bird.png">
```

**jQuery**:

```javascript
$('img').animate({
  height: '500px'})
```

####FADEOUT
The `fadeOut` function makes the select elements the function is called on transparent.

Let's take the bird image again:

```html
<img src="images/bird.png">
```

We could make the bird disappear by using `fadeOut`. This function accepts a parameter of the amount of time in milliseconds for the item to fade:

```html
$('img').fadeOut(4000);
```

####FADEIN
Once an item is transparent, you can make it come back to full visibility with `fadeIn`.

**HTML**:

```html
<img src="images/bird.png">
```

**jQuery**:

```javascript
$('img').fadeOut(4000); //takes 4 seconds to fade image out
$('img').fadeIn(2000); //image back to full visibility in 2 seconds
```

####HIDE
The `hide` function hides all matched elements.

Let's say we have a paragraph that we want to hide:

```html
<p> paragraph.</p>
```

We easily hide it:

```javascript
$('p').hide(); // hides the paragraph immediately
$('p').hide(4000); //gradually shrinks and hides the image over 4 seconds
```

####SHOW
The show function makes hidden elements reappear:

```html
<p> paragraph.</p>
```

We easily hide it:

```javascript
$('p').hide(); // hides the paragraph immediately
$('p').show(); // shows the paragraph immediately
 
$('p').hide();
$('p').show(4000); // gradually grows and shows the paragraph
```

####DOM INSERTION
The methods under this category allow us to insert new content into our site.

####APPEND
The append method adds specific content to the end of the element.

**HTML**:

```html
<p> Hey there. This is a short paragraph.</p>
```

To add text to the paragraph, we can use the `append` function and pass it a parameter of the content we want to add to the end of the `p` tag:

```javascript
$('p').append("Now the paragraph is a little bit longer because I am adding text to it with jQuery");
```

Now our HTML looks something like this:

```html
<p> Hey there. This is a short paragraph.Now the paragraph is a little bit longer because I am adding text to it with jQuery</p>
```

####RESOURCES
+ [jQuery Attribute Methods](https://api.jquery.com/category/attributes/)
+ [jQuery CSS Methods](https://api.jquery.com/category/css/)
+ [jQuery Effects Methods](https://api.jquery.com/category/effects/)
+ [jQuery DOM Insertion Methods](http://api.jquery.com/category/manipulation/dom-insertion-inside/)

##jQuery Event Listeners
####OBJECTIVES
+ Explain what an event listener is and how it's used
+ Implement an event listener

####INTRO
Ever used a website where an action you took triggered something to happen on the page? You clicked part of a form and suddenly more to fill out appeared. You moused over part of the page and a modal window appeared. On Facebook, you click to see more comments and the rest appear. The list goes on and on and on. When these actions happen, code is responding to an event taken by a user, and responding with an action.

In JavaScript, these things that happen are called **DOM events** and the code written to trigger the action is called an **event listener** or **event handler**.

####ATTACH EVENT HANDLER
In order to trigger events, we need to set up a handler that will respond when we can run our code.

Let's take a basic example:

```html
<h1> This is some text to click</h1>
```

Given the above HTML, let's set up an event handler to pay attention to this `h1` tag:

```javascript
$('h1').on("eventname", function(){
    //action you want taken
});
```

We use `on` to bind an event to the HTML element we selected (in this case `h1`). The `on` function takes an argument of the name of the event you want the user to take. Whenever that event happens, the code inside the function will run.

####LOAD
The load event handler triggers an action to happen when the element it's called on is loaded. An image is a good example for when this would be used because images typically load slower than text.

**HTML**:

```html
  <img src="https://s3.amazonaws.com/after-school-assets/minion-tongue.jpg">
```

**JS**:

```javascript
$('img').on('load', function(){
  //actions you want to happen
});
```

####KEYPRESS, KEYDOWN, KEYUP
The `keypress` event keeps track of every time a key on the keyboard is pressed (excluding ALT, CTRL, SHIFT, ESC). It's important to note that `keypress` isn't covered by browser specifications, so it's behavior isn't guaranteed.

`keydown` tracks every time a key is pressed down on the keyboard, and the `keyup` checks every time a key press is released.

`keypress`:

```javascript
$(document).on('keypress', function(key) {
  if (key.which == 13){
    alert('enter was pressed');
  }
});
```

`keyup`:

```javascript
$(document).on('keyup', function(key) {
  if (key.which == 82){
    alert('r was pressed');
  }
});
```

`keydown`:

```javascript
$(document).on('keydown', function(key) {
  if(key.which == 83){
      alert('s was pressed');
  }
});
```

The three examples above use `document` as the jQuery selector, so any time a key is pressed anywhere on the screen, the alert will appear. If you want to check if the cursor is in a particular area (like an input field) then you would just need to change the selector.

Let's walk through how `keydown` works. We use `document` as our selector, and bind the `keydown` event. We pass `theKey` to the function as an argument. Inside the function, we set up an `if` statement that checks if the `which` of the pressed key matches. `83` is the ASCII code for `s`. If the `s` key was pressed, the alert `"s was pressed"` will appear.

####SUBMIT
The `submit` event submits a form. Let's set up our submit event so that it only submits the form if a certain value has been entered in the text field:

```javascript
$("form").on("submit", function() {
  if ($( "input:first" ).val() === "correct") {
    alert('your form is going to be submitted now');
    return;
  }
  alert("you entered the wrong value");
  return;
});
```

In the example above, we are using the `submit` event. We use the HTML `form` as our selector to bind the event on. We then set up an if-statement that checks to see if the value of the first input is `"correct"`. If the user typed `"correct"` then the alert `'your form is going to be submitted now'` will appear, and the function will `return` so that no other lines of code will be executed. If they didn't enter `"correct"` the alert `"you entered the wrong value"` will appear. As of right now, the form is still submitting even with the wrong value, but later we'll learn how to prevent that from happening.

####RESOURCES
There are many many more jQuery events. Take a look at the docs to learn about all of them!

+ [Browser Events](https://api.jquery.com/category/events/browser-events/)
+ [ASCII Key Values](http://keycode.info/)

And keep in mind that jQuery events are based on the `Event` class that's built into browsers. You can read more about `Event` here:

+ [Event](https://developer.mozilla.org/en-US/docs/Web/API/Event)

##This
####OBJECTIVES
1. Explain what `this` is and what it can refer to
2. Use `this` to refer to an object that called a function
3. Use `this` to refactor event handlers
4. Explain how `this` refers to different objects based on scope

####INTRODUCTION
It's often important in programming to know what the "owner" of a function is, so that we can operate on some specific object or data safely.

For instance, consider an event handler that fires when someone clicks on a link in the DOM. When that event handling function is invoked, we might want to know which *specific* link was clicked, so that we can manipulate it in some way.

That's where the `this` keyword comes into play. Every function is automatically assigned a `this` value when called, and that represents "who" called the function. This value can be an object, an event, or even another function.

The value of `this` is determined by the execution context, or scope, of the function call.

####GLOBAL SCOPE
Code running outside of a function is in the **Global Scope**. Every JavaScript runtime has a default global object that will be the value of `this` when a function is called in global scope. In the browser, if we're referencing `this` in the global scope, we're referencing the `window` object.

Try adding the following code to `script.js` and then run `index.html` in your browser with the JavaScript console open:

```javascript
console.log(this === window);
// returns true
```

Here, we called `this` in the global scope, so
the value of `this` was set to the default global object, `window`, when the function was invoked.

####FUNCTION (LOCAL) SCOPE
Inside of a function, the value of `this` will be set based on how the function is called.

####SIMPLE FUNCTION CALL
Add the following to `script.js` and refresh the page with the console open:

```javascript
function checkThis(){
  console.log(this);
}
checkThis();
// outputs window object
```

This will also output the `window` object, because this simple function call doesn't set the `this` value, and because we aren't in strict mode, the value of `this` must be an object, so the default global object `window` is used.

**Advanced**: Strict mode is a setting that enables better error-checking in your code by prohibiting the use of implicitly declared variables, duplicate
parameter names, and other potentially bug-causing behavior, and converting some silent execution errors into not-so-silent ones. For more information, check out the [MDN documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode).

Let's put our function in strict mode:

```javascript
function checkThis(){
  'use strict';
  console.log(this);
}
checkThis();
// outputs "undefined"
```

In strict mode, `this` remains at whatever it's set to when entering the execution context. If it's not defined, it remains undefined rather than being assigned the default `window` object.

####CONSTRUCTOR FUNCTION CALL

When we use the constructor function pattern to create objects, we make use of `this` to assign property values to the instance of the object being constructed.

```javascript
function Chair(style, color) {
  console.log(this);
  this.style = style;
  this.color = color;
}
var sofa = new Chair("sofa", "green");
```

Here, because we're calling `Chair()` with the `new` keyword, `this` refers to the new `Chair` object. So when you see `this.style = style;` you can think of it as saying "set the style property of THIS current
instance of a Chair."

In your console, `this` will be an empty Chair
object because we haven't assigned the property values yet when we output `this`, but try moving that `console.log(this)` to the end of the function and see what you get!

####OBJECT FUNCTION CALL
Objects can have functions as well as properties. When a function is an attribute of an object, `this` is set to the object instance that contains the function, similar to how it works with a constructor function.

```javascript
var couch = {
  color: 'green',
  f: function() {
    return this;
  }
};
 
console.log(couch.f());
// outputs object
```

**Top-tip**: Notice that this only outputs an `Object` and not something called `couch`? That's because `this` is just an instance of the base `Object` type here, and `couch` is just the name of the variable holding that
object instance. The object itself is the owner of the function, and has no idea what you named it.

####DOM EVENTS
We can also make use of `this` when handling events on the DOM. If our `index.html` has three image elements with the same css class, we might need to know which specific `img` was clicked.

```javascript
<img class="pix" src="http://i.giphy.com/S1phUc5mmaZqM.gif">
<img class="pix" src="http://i.giphy.com/eGe59ekUJEll6.gif">
<img class="pix" src="http://i.giphy.com/l41lNT5u8hCI92nQc.gif">
<script type="text/javascript" charset="utf-8">
    var els = document.getElementsByClassName("pix");
    function handleClick(e) {
      console.log(this);
    }
    for(var i=0 ; i < els.length ; i++){
      els[i].addEventListener("click", handleClick, false);
    }
</script>
```

Here, when we click a given image, we see that `this` refers to that specific DOM element, so if we wanted to do something like hide it or apply some other class to it, we could safely do so on just that one element.

####SUMMARY
We learned that we can use the `this` keyword to understand who the "owner" of a function is, and how the execution context of the function call affects the value of `this`. We also saw how to use `this` in objects and when manipulating the DOM to ensure that we are operating on only the specific objects that we intend to.

####RESOURCES
+ [MDN This](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)
+ [JavaScript is Sexy](http://javascriptissexy.com/understand-javascripts-this-with-clarity-and-master-it/)
+ [StackOverFlow This Quiz](http://javascriptissexy.com/understand-javascripts-this-with-clarity-and-master-it/)

##JS Anonymous Functions
####Objectives
+ Explain what an anonymous function is and why it's useful
+ Call an anonymous function

####INTRO
We know functions (like methods in Ruby) are used to bundle our code to be able to reuse again and again and again. So why take the time to define and store a function we never plan on reusing? It takes up memory, requires extra typing, and completely goes against the purpose of a function. Thankfully, JavaScript allows us to easily solve this problem by using an **anonymous function**. An anonymous function is one that is never defined and is planned on being used once. They allow us to execute a block of a code once. One and done.

####USAGE
One of the most common usage's for anonymous functions is a callback function (function passed as a parameter to another function).

####EVENT HANDLER CALLBACKS
We know that functions can accept functions as parameters. We've seen this time and time again when setting up jQuery event handlers. The `on` function accepts a string of the event you would like to bind, and a function which is executed when the event is trigged. But that function we pass as a parameter, it was never defined anywhere previously. That function does not have a name.

```javascript
$('#submit').on('click', function(){
  alert("form submitted!");
});
```

The anonymous function (the callback function) in the example above simply creates an alert in the browser with the text `"form submitted!"`. This function is never executed on another part of the code. We only want it to fire when the submit button is clicked.

####SETTIMEOUT
The `setTimeout` function executes a code block after a specific amount of time has passed. This function accepts two parameters, a function and time in milliseconds.

```javascript
setTimeout(function(){ console.log("I waited 5 seconds to execute");}, 5000)
```

The `setTimeout` function accepts the anonymous function as the first parameter. This anonymous function prints `"I waited 5 seconds to execute"` to the console.

```javascript
function(){ console.log("I waited 5 seconds to execute");}
```

####FUNCTION EXPRESSION
We've seen function expressions already, but they can also be considered anonymous functions:

```javascript
var numberz = function() {
    return 6+3;
}
 
//anonymous function because the function the variable is storing doesn't have a name
 
var cats = function kitties() {
    return "meow meow pow pow";
}
// not an anonymous function because the function has the name kitties
```

Remember that function declarations get hoisted to the top of their scope, but function expressions do not. In the above example, `var numberz;` would get hoisted to the top of the scope, not the anonymous function it's storing. Therefore, at the top of the scope, the variable `numberz` is storing `undefined` and thus not taking up memory for the entire duration of the program.

####POSTSCRIPT
You might be thinking that you've seen something like these anonymous functions before — and you'd be right. Anonymous function are *sort of* like Ruby blocks in that they provide a handy way to process incoming data but can't be called outside of their immediate context. Pretty cool, eh?

####RESOURCES
+ [JavaScript WE Blog](https://javascriptweblog.wordpress.com/2010/07/06/function-declarations-vs-function-expressions/)
+ [Thoughtbot Blog](https://robots.thoughtbot.com/back-to-basics-anonymous-functions-and-closures)

##jQuery Iterators
####OBJECTIVES
+ use `$.each` to iterate over a collection of objects
+ use `.each` to iterate over jQuery objects
+ use `$.map` to iterate over a collection of objects
+ use `.map` to iterate over jQuery objects

####INTRO
Just like Ruby and JavaScript had iterators, jQuery does too. These iterators work in much the same way as all other iterators, and let us manipulate the dom. Feel free to copy and paste the examples below into `index.html` and `script.js` to try them out!

####`$.EACH`
Let's say we have an array of names, and we want to iterate over them to say hello:

```javascript
var names = ["Carleton", "Avi", "Azat", "Ann" ]
```

jQuery has a built in iterator that let's us iterate over **plain JavaScript arrays**:

```javascript
$.each(names, function(index, name){
  console.log("hey " + name);
});
```

`$.each` accepts two parameters, the array to iterate over, and a function that contains the actions to be taken with each item in the array. This anonymous function accepts two parameters: the index of the array, and a variable placeholder to represent each item individually.

The first time iterating over the `names` array, `index` is storing 0 and `name` is storing `"Carleton"`.

####`.EACH`

`.each` is only different from `$.each` because it works on a collection of jQuery objects, not just a plain JavaScript array.

Let's say we have three divs on a page, and we want to print the div number inside each div using jQuery.

**HTML**:

```html
<div>
</div>
<div>
</div>
<div>
</div>
<div>
</div>
```

We can use the jQuery selector `$('div')` which will return us an array of jQuery objects.

We can iterate over this array using `.each` and use it to append the div number to the body of the div:

```javascript
$('div').each(function(index, div){
  $(this).append("this is div number " + (index + 1));
});
```

The `.each` function accepts a function as a parameter. The function accepts two parameters, a variable to represent the index of the array and a variable to represent each individual jQuery object in the array. Once inside the body of the function, `this` represents the element of the array.

####`$.MAP`

Just like `$.each`, `$.map` only works on plain JavaScript arrays. Just like in Ruby, `map` in JavaScript creates a new array with the results of calling a provided function.

Let's take our first example of an array of names:

```javascript
var names = ["Carleton", "Avi", "Azat", "Ann" ];
```

Using `$.map`, we can iterate over the names to add their programming language of choice:

```javascript
$.map(names, function(name, index){
   return name + " loves JavaScript"; 
});
```

You'll notice the order of the parameters in the anonymous functions are reversed.This is done to match the order of parameters in the plain JavaScript `map` function.

####`.MAP`
Let's say we have a list on a page, and we want to get all the text from the list to manipulate them:

```html
<ol>
  <li>Chocolate</li>
  <li>Fried Chicken</li>
  <li>Dumplings</li>
  <li>Pizza</li>
</ol>
```

We can use `.map` to iterate over the lists and return an array of the text:

```javascript
function listIterate(){
  return $('li').map(function(item, index){
      return item.innerHTML;
  });
}
```

We defined a function `listIterate`. The body of the function calls `.map` on `$("li")` which returns an array of jQuery objects representing all the list items. The important thing to note is that we have `return` in this method twice. The first `return` sets the new array as the return value of the `listIterate` function. The second `return` tells `.map` to add the text of the list item to the new array and move on to the next element in the array.

##Prevent Default
####OBJECTIVES
+ Explain what prevent default does and when its used
+ Use `event.preventDefault()` to stop default browser behavior

####INTRO
What happens when you submit a form? Think about when you signed up for an online service. Maybe an Amazon account, or you log into your Facebook or email accounts. What happened in the browser when you submitted that form? As soon as the the form was submitted the page in the browser refreshes. This happens every single time. This is known as the default browser behavior.

While this is obviously the behavior we would want to have most of the time, there are times when you maybe don't want a page refresh. Maybe you have client-side validations that check to make sure the form input is correct and the user doesn't enter a valid input. Maybe you're building a single page application (like the calculator or a to-do list) and refreshing the page would clear the data from the page.

Both of those circumstances involve stopping jQuery from performing the default behavior. We can do that by using the `preventDefault` function.

####DEFAULT BEHAVIOR
Let's say we have the following form with a div below it:

```html
<form>
  <input type="text" id="name">
  <input type="submit" value="submit">
</form>
<div id="hello">
</div>
```

And the following `submit` event that says hello to the user based on the name they entered. The greeting is added to the `div` with the ID `hello`.

```javascript
$('form').on('submit', function(event){
  var name = $('#name').val();
  $("#hello").text("Hello, " + name);
});
```

When you actually enter and submit the form, instead of seeing the greeting, you'll see the page refresh. Go ahead and open `index.html` in the browser and `js/script.js` in the text editor. You'll want to uncomment the code under the comment `/ browser refreshes on submit` and make sure the rest of the code in the file is commented out. Go ahead and test the form submission. You should see the form submit and the page refresh. Obviously that isn't going to cut it for us.

####JQUERY EVENT OBJECT
So how do we use `preventDefault`? We need to refactor our code slightly:

```javascript
$('form').on('submit', function(event){
  var name = $('#name').val();
  $("#hello").text("Hello, " + name);
  event.preventDefault();
});
```

In the above code, we had to pass an `event` to the anonymous function. This event is the jQuery event object. Every time an event is bound to an element, this jQuery event object is created to represent that event.

In `js/script.js` go ahead and comment out all the code except for the code directly below the comment `//examine event object`. That code should have `debugger`. Refresh `index.html` in the browser and fire your click event. When you're dropped in the debugger console, go ahead and take a look at `event`.

You should see something like this:

```javascript
event
> n.Event {originalEvent: Event, type: "submit", timeStamp: 1453912261129, jQuery211012266199523583055: true, which: undefined…}
```

`event.currentTarget;` will return the HTML `form` because that is the element the submit event is bound to. `event.type;` returns `"submit"`.

####USING PREVENT DEFAULT
Now that we know what `event` represents, let's talk about what we do with that object. We have to pass `event` as an argument to the anonymous callback function.

```javascript
$('form').on('submit', function(event){
 //code
});
```

Now that we have `event` accessible inside the function, we can use it to call `preventDefault`:

```javascript
event.preventDefault();
```

What this does is stop the event from performing it's default behavior. Now refresh index.html in the browser and submit the form. You should see your greeting appear!

##Stop Propogation
####OBJECTIVES
+ Explain what `stopPropagation()` is and why it's used
+ Explain "bubbling up"
	+ See: [what is even bubbling and capturing](http://stackoverflow.com/questions/4616694/what-is-event-bubbling-and-capturing)
+ Use stop propagation to prevent event handlers from bubbling up the DOM

####INTRO
Let's say you're building a course registration site for a Flatiron. You need to build a list of available courses, and include some details about each course.

You need to build it so that when you click a course, the details about the course disappear and reappear. Each course would need to have a click event that toggles the details. Flatiron also wants to be able to remove courses if they're not offered that semester, so there will need to be a button that removes that course (an `x`)

We'd end up with some HTML like this:

```html
<ul class="courses">
  <li class="course">
    Ruby
      <span class='delete'>x</span>
    <div class="detail">
      Learn Ruby, It loves you. Be happy.
    </div>
  </li>
  <li class="course">
    JavaScript
      <span class='delete'>x</span>
    <div class="detail">
      Learn JavaScript and build powerful full stack web apps
    </div>
  </li>
  <li class="course">
    iOS
      <span class='delete'>x</span>
    <div class="detail">
      Everyone loves a good iPhone app
    </div>
  </li>
</ul>
```

And let's say we create those click events:

```javascript
$('.course').on('click', function(){
    $(this).find('.details').slideToggle();
  });
 
  $('.course .delete').on('click', function(){
    alert("about to delete");
  })
```

The first click event toggles the details on and off the screen. The second click event just alerts `"about to delete"`.

Open `index.html` in the browser and `js/script.js` in the text editor. Make sure all the code in `js/script.js` is commented out except the code under the comment `// toggle and delete`.

Go ahead and first click `Ruby` in the browser to hide the details. Then click the x to "delete" the item. You should see the alert appear, as we expected. But wait, why do the details toggle on the screen? How did that event happen?

####BUBBLING UP
In jQuery, all click events "bubble up" the DOM. The `document` object knows about every event that is triggered on a page. This means that in our example above, the `span` with the `x` is a child of the `li` with the class `course`. When we click the `x`, that click event bubbles up the DOM and the parent element, the `li` knows a click event has been triggered, and that triggers the click event of the parent.

What in the world? Why is that behavior we would want? In most cases you wouldn't at all. Imagine if you had a large series of nested elements all with click events. Firing the click event of the innermost child would trigger the click events of every single parent.

####STOP PROPAGATION
So how the heck do we stop that from happening? With `stopPropagation`. Let's go ahead and refactor our jQuery:

```javascript
$('.course').on('click', function(){
    $(this).find('.details').slideToggle();
  });
 
  $('.course .delete').on('click', function(event){
    alert("about to delete");
    event.stopPropagation();
  })
```

You'll notice we didn't change anything to the click event on the class `course`. But we did make some changes to the delete button. We passed `event` to the anonymous function and then called the `stopPropagation` function on the event object. This function stops the click event from bubbling up the dom.

Go ahead and comment out all the code in `js/script.js` except the code under the comment `//stop propagation`. Refresh the page in the browser. Click a course name to hide the details and then click the `x`. The alert should appear and the details should remain hidden.

##AJAX and Callbacks
####OBJECTIVES
+ Explain what an API is and why it's used
+ Explain what Ajax is and why it's used
+ Make a get request using Ajax to append text to a page
+ Explain what a callback is and why Ajax runs asynchronously

####INTRODUCTION
In JavaScript, we can use `XMLHttpReqest` directly to access remote data, or web APIs, but it requires us to do a lot of lower-level wiring to get everything working. Now we're going to take a look at the Ajax capabilities of jQuery, which abstracts the XHR code into a nice, higher-level package.

####POSTMAN AND THE GITHUB API (DONE)
We interact with web APIs through a set of urls. Each url defines a resource that we request or take action on.

Rather than just talk about it, let's get our hands dirty and explore an API. To do this, we're going to use a tool called Postman. Postman is an easy to use Chrome extension that lets us make different web requests. It easily allows us to interact with web APIs. For our exercise, we are going to work with the GitHub API to retrieve information about the jQuery GitHub repository. To get started we need to setup Postman.

####POSTMAN INSTALLATION
1. Visit https://www.getpostman.com and install the Chrome extension.
2. Once you have it installed, open Postman from the Chrome Apps menu.
3. Skip the signup and go straight to the app.

Great! Now you're all set up to make your own API requests. As you know, jQuery is a large open source project with many contributors. Those contributors make a hefty amount of commits. The GitHub API allows us to retrieve the list of commits made to the jQuery repository.

At this point Postman should be loaded and ready to go:

+ Enter **https://api.github.com/repos/jquery/jquery/** commits in the URL textbox.
+ Click the **Send** button.

Once the request finishes, Postman will display the results. Does this format look familiar? If you said JSON (JavaScript Object Notation), then give yourself a pat on the back. What we're looking at is a JSON list of all the commits made to the jQuery repository. Each hash in the array has an author key. Do you recognize any of the committers? If not, let's try to narrow the results returned from the GitHub API.

GitHub exposes a way for us to do this using HTML parameters. By changing the URL slightly to include the `author` parameter, we can ask the GitHub API to return only the commits made by John Resig (bonus points if you know who this is!).

Let's add `?author=jeresig` to the end of the url and see how the results change.

Go back to Postman and perform the following:

+ Enter **https://api.github.com/repos/jquery/jquery/commits?author=jeresig**
+ Click the **Send** button.

As you can see, getting data from an API is pretty easy. But we haven't really said what an API is.

####DEFINING APIS
For the purpose of this lesson, we are mostly concerned with web APIs. But the term API actually has a more broad meaning.

> In computer programming, an application programming interface (API) is a set of routines, protocols, and tools for building software and applications. - Wikipedia

In its simplest form, an API in relation to Object Oriented programming is a class and the list of methods we define for that class. When creating a class, we are defining a guidebook on how to interact with other parts of the code. We get to decide which methods and variables are public or private, essentially controlling how to interact with the class.

When we apply this concept to the web, we get web APIs like the GitHub API and Twitter API. From our Postman experiment, we saw how GitHub provides a way for us to interact with the data on their system. Just like how a class provides a set of public methods to interact with, web APIs provide us with urls.

The list of urls that GitHub provides on https://developer.github.com/v3 act as the public methods into their system. The developers that created the API control which resources they want to share and who has access to them. In the end it's all just the same data available from GitHub. The big difference is the GitHub API is just data and not the HTML/CSS/JavaScript which is the only thing our applications need.

####AJAX
Wouldn't it be nice if page refreshes didn't exist? What if we could do multiple things at once from a single web page? In a perfect world we could type into a search textbox and have searches being performed in the background as we type. That world is here and it's called Ajax! Asynchronous Javascript and XML (Ajax) is a technique that is used in web applications. It provides a way for content to be retrieved from a server and displayed without having to refresh the entire page.

Modern dynamic applications provide a better user experience by allowing users to manipulate data on the server and see the results without having to refresh the page. This is the power of Ajax in action. In the background, requests are made to a web API using JavaScript. As developers we can then choose to alter the displayed HTML based on the responses from the web API.

When you've used `XMLHttpRequest` directly to query an API like Github and dynamically update your page, you've been doing Ajax. With jQuery, we have a set of Ajax-related functions to make that even easier.

Let's try an example. You have an `index.html` file with a basic structure. Let's add a reference to jQuery so we can use it.

```html
<body>
  <p id="sentences">
    Loading our page...
  </p>
  <script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/3.1.0/jquery.min.js"></script>
  <script src="script.js"></script>
</body>
```

There's also a `sentence.html` file. Let's add some data to it.

```html
<p>Ajax is a brand of cleaning products, introduced by Colgate-Palmolive in 1947 for a powdered household and industrial cleaner.</p>
<p>It was one of the company's first major brands.</p>
```

Now in our `script.js`, let's use the jQuery `get` function to make a `GET` request to our `sentence.html` data.

**Note**: Remote data can come from anywhere, even your own server! Remote just means it isn't directly included in the current page.

```javascript
// We should wait for the page to load before running our ajax request
$(document).ready(function(){
  // Now we start the Ajax GET request. The first parameter is the URL with the data.
  // The second parameter is a function that handles the response.
  $.get("sentence.html", function(response) {
    // Here we are getting the element on the page with the id of sentences and
    // insert the response
    $("#sentences").html(response);
  });
});
```

Let's load up `index.html` and see what happens!

Okay. Not a whole lot.

We actually need to serve this page from an HTTP server rather than load it directly in our browser. At the console, run the following command:

```
$ python -m SimpleHTTPServer
```

This starts a simple server that will serve our files over HTTP. You need to start a server instead of just opening up `index.html` in the browser because of the browser enforced same-origin policy. To prevent security risks, the browser enforces a same origin policy. A different origin can be interpreted as a different domain, different protocol, and a different port.

When you open up `index.html` by right clicking on the file, the site opens with `file://`. Same-origin policy only allows http, data, chrome, chrome-extension, https, chrome-extension-resource protocols. Ajax uses http requests, and thus must interact with http protocol in the browser.

Browse to [http://localhost:8000](http://localhost:8000/) and watch as the Ajax request is made and the new data is added to our web page. Pretty cool! This might all happen too quick to really notice anything so you may want to have your terminal window side by side with the browser window. This way you can see the request hit our server.

We used the power of Ajax to load data from the `sentence.html`. This same idea can be applied to calling the GitHub API or another remote resource.

####CALLBACKS
If we look at our last example, the Ajax request completed very quick but this won't always be the case. If we request data from a slow server over a slow internet connection, it might take multiple seconds to get a response. Using a callback allows our code to make our request and continue doing other things until the request completes.

Ajax follows an asynchronous pattern, which makes Ajax requests *non-blocking*. This means we don't sit around and wait for the request to finish before running the rest of our code. We set callbacks and then fire and forget. When the request is complete, the callbacks are responsible for deciding what to do with the returned data.

To make this concept stick, let's look at a real world example. When you put food into a microwave, you don't stand there and wait for the food to finish cooking.

But even if you stand there, you can do other things. You probably pick up your phone, look at Instagram, read some text messages and of course, work on Learn. Basically, you are doing other things while the microwave takes care of cooking your food.

When the food is cooked, the microwave beeps and you remove the food and eat it. This final step of removing the food and eating it is exactly how our callbacks work. One thing to note, as we wait for our food, we don't check if its done every 5 seconds, we wait until the beep tells us it's done. Checking every 5 seconds is called *polling*, and it's a lot less efficient than waiting for the beep, which is our *callback*.

####HANDLING PROBLEMS
So far, we have been dealing with successful API requests. But things don't always go according to plan. What happens if the API we are using doesn't respond? Or if we attempt to retrieve a resource that doesn't exist?

This can happen when API requests are based on user input. Let's go back to the GitHub API where we are retrieving commits. Imagine we want to retrieve a specific commit using a SHA.

**Postman**:
https://api.github.com/repos/jquery/jquery/commits?sha=8f447576c918e3004ea479c278c11677920dc41a
Returns success

**Postman error**:
https://api.github.com/repos/jquery/jquery/commits?sha=fake-SHA
Returns a 404 not found

A good developer will make sure to handle these unexpected events gracefully when using Ajax. We can provide multiple callbacks when using jQuery: one to handle a successful response and one to handle when an error occurs.

Let's add this inside our document ready function, then open your inspector and reload your page.

```javascript
$.get("this_doesnt_exist.html", function(data) {
  // This will not be called because the .html file request doesn't exist
  doSomethingGood();
}).fail(function(error) {
  // This is called when an error occurs
  console.log('Something went wrong: ' + error);
});
```

We chained an additional call to `fail` on the end of our request. We passed a callback function to the method which will run only if an error occurs. In our example we logged the error to the console, but in real world situation you might want to try to fix the issue or inform the user.

Note that it doesn't matter what you call `data` and `error` in the above examples — the only thing that matters is the order of the arguments. In the callback to `get()`, the first argument is going to be the data that the server sent back, so it makes sense to call it `data` — but we could just as well call it `response`. Similarly, the first argument to `fail()`'s callback is an error object, so we should probably give it a descriptive name like `error` (but we don't have to!).

This is another example of how we could use jQuery to perform an Ajax request.

```javascript
var url = "https://api.github.com/repos/rails/rails/commits?sha=82885325e04d78fb7ec608a4670164d842d23078";
 
$.get(url)
  .done(function(data) {
    console.log("Done");
    console.log(data);
  });
```

Note: The callback that gets passed into `.done` gets `data` as an argument. `data` represents the response returned from the API. jQuery handles passing in that `data` object to the callbacks. This is essential to our fire and forget technique. We don't have to sit around and wait for the API to give us a response. Instead, we tell jQuery that when it receives a response to please pass it along to our callbacks so they can handle it accordingly.

####RESOURCES
+ [Application programming interface](http://en.wikipedia.org/wiki/Application_programming_interface)
+ [Jquery.Get](http://api.jquery.com/jquery.get/)


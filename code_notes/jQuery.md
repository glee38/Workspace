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
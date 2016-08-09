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
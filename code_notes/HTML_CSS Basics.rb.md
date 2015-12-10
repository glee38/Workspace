#HTML Basics

Hyper Text Markup Language, or HTML, is a way to demarcate a document into different parts. Each part is *marked* by elements (using tags). Each element has it's own special conotation that the browser uses to make render the HTML document. 

+ Use [this](http://www.cril.univ-artois.fr/~lecoutre/teaching/web/sheets/HTML.pdf) cheat sheet on HTML elements for guidance.

##Elements

+ All begin with `<` and end with `>` 
	+ ex. `<div>` (this last part is a tag)
+ Most have an opening tag such as `<div>` and a closing tag `</div>`
	+ The `/` indicates to the browser that that tag is a closing tag
	+ The element is everything between the tags and the tags themselves
+ Some tags are self closing like the line break element `<br>`.
+ Elements can have IDs and classes to aid the browser in finding specific tags.
	+ Must begin with a letter A-Z or a-z
	+ Can be followed by: letters (A-Za-z), digits (0-9), hyphens ("-"), and underscores ("_")
	+ **IDs can only be used once per page**. 
		+ ex: `<div id="this-special-div"></div>`
	+ **Classes can be used as many times as you want**. 
		+ ex: `<div classes="a-less-special-div"></div>`
+ Elements nested inside other elements are called children
	+ Children inherit attributes from their parents.
	+ Don't nest everything. [Here's](http://sewingandembroiderywarehouse.com/embtrb.htm) a website that nested everything. Don't do that!
+ Elements next to one another are siblings
	+ Siblings do not inherit from one another, but are important for selecting in CSS

Here is an example of element relations:

```
  <div>  <!-- the parent element -->
    <p></p>  <!-- the first sibling element/the first
     child-->
    <p></p>  <!-- the second sibling element/the second
     child -->
  </div>
```

##CSS Basics

Cascading Style Sheets, or CSS, is language created to style an HTML document by telling the browser how specific elements should look. CSS does this by selecting elments based on their tag, ids, classes, or all of the above. The reason for CSS is the seperation of concerns. We want HTML only to be concerned with how it displays and demarcates information, and we let CSS worry about how to make that information look pretty.
CSS selectors

+ Selects elements to assign them styles
+ `*` (wildcard) selects every element
+ An element, such as `div` will select all elements of that type
+ Select an id like so: `#some-id`
+ Classes are selected like this: `.some-class`
+ To select all children elements of a parent do something like this: `div p`

![descendant selector](/Users/gracelee/Desktop/Screen Shot 2015-12-09 at 6.57.49 PM.png)

![child selector](/Users/gracelee/Desktop/Workspace/code_notes/Screen Shot 2015-12-09 at 7.03.31 PM.png)

![sibling selector](/Users/gracelee/Desktop/Workspace/code_notes/Screen Shot 2015-12-09 at 7.04.11 PM.png)

+ To select multiple different elements seperate them by commas like this: `div, p, a`

Here's an example of CSS styling:

```
  * {
    border-top-color: red;  /* color in CSS refers to
     font color */
  }  /* all elements will have red font */
```

##CSS Specificity
###CSS Specificity: An Overview
+ Specificity determines, which CSS rule is applied by the browsers.

+ Specificity is usually the reason why your CSS-rules don’t apply to some elements, although you think they should.
+ Every selector has its place in the specificity hierarchy.
+ If two selectors apply to the same element, the one with higher specificity wins.
+ There are four distinct categories which define the specificity level of a given selector: **inline styles**, **IDs**, **classes+attributes** and **elements**.
+ When selectors have an equal specificity value, the latest rule is the one that counts.
+ When selectors have an unequal specificity value, the more specific rule is the one that counts.
+ Rules with more specific selectors have a greater specificity.
+ The last rule defined overrides any previous, conflicting rules.
+ The embedded style sheet has a greater specificity than other rules.
+ ID selectors have a higher specificity than attribute selectors.
+ You should always try to use IDs to increase the specificity.
+ A class selector beats any number of element selectors.
+ The universal selector and inherited selectors have a specificity of 0, 0, 0, 0.
+ You can calculate CSS specificity with CSS Specificity Calculator.

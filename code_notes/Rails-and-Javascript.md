#Rails & Javascript
##What Is The Asset Pipeline
####OBJECTIVES
1. Understand the 4 main features of the asset pipeline.
2. Identify the Asset Paths
3. Know how Asset Manifests provide concatenation of CSS and JS.
4. Use preprocessing languages like SASS or CoffeeScript
5. Define Asset Fingerprinting

####OUTLINE
For a long time we treated Javascript and CSS as an after thought in developing web applications. All of our asset code, things like images, stylesheets, and javascript were kept in a massive folder called public and served outside of the context of our Rails application. As the web evolved, that no longer made sense.

The asset pipeline is the Rails answer to managing stylesheets, javascripts, and images.

####ASSET PATHS
A lot of files go into creating web applications. The CSS and JavaScript files alone can be hard to organize. What folders do we create? What files go where? The Asset Pipeline provides an answer for this problem. We have to keep things very organized in our application but by keeping separate files and folders for each concept or unit of code, we have 2 problems.

1. How does Rails know where things are? Is the calendar JS file in `app/assets/javascripts/calendar.js` or `vendor/javascripts/calendar.js`?

2. We don't want to serve each file separately as this will make our page load very slow. It makes sense for us to maintain separate small files for readability and organization but for the browser, we'd rather smash all those small files together and load 1 JS file and 1 CSS file. This process is called concatenation.

Let's talk about our first problem, how does Rails know where to look? The Asset Pipeline has a concept called Asset Paths for handling this. Just like in BASH where we have a PATH environment variable that is a combination of folder paths, the Asset Path is a combination of folder paths for Rails to look for assets in. Let's take a look at an example of how our Asset Path is configured.

```ruby
Rails.application.config.assets.paths =>
[
  "/Users/avi/asset-test/app/assets/images",
  "/Users/avi/asset-test/app/assets/javascripts",
  "/Users/avi/asset-test/app/assets/stylesheets",
  "/Users/avi/asset-test/vendor/assets/javascripts",
  "/Users/avi/asset-test/vendor/assets/stylesheets",
  "/Users/avi/.rvm/gems/ruby-2.2.3/gems/turbolinks-2.5.3/lib/assets/javascripts",
  "/Users/avi/.rvm/gems/ruby-2.2.3/gems/jquery-rails-4.1.0/vendor/assets/javascripts",
  "/Users/avi/.rvm/gems/ruby-2.2.3/gems/coffee-rails-4.1.1/lib/assets/javascripts"
]
```

If we put an asset in any of these folders, we can access them via the URL `'/assets'` in our application. If you have additional folders for Rails to search, you can add the folders to the Asset Path. This is done in the file `config/initializers/assets.rb`.

```ruby
Rails.application.config.assets.paths << "New Path"
```

We can put assets anywhere, configure our Asset Path and access them via a single /assets URL.

####MANIFESTS AND CONCATENATION
Now that we can put files anywhere, how do we get them to be included in our web pages? The Asset Pipeline uses a manifest file to tell Rails what to load. This manifest file is a central location where we can list all the CSS and JS files our application needs. This isn't a feature of JS or CSS but rather the asset pipeline. Here is an example of what our JS manifest file looks like:

File: `app/assets/javascripts/application.js`

```javascript
//= require jquery
//= require calendar
```

When you include the manifest file in your layout with javascript_include_tag, the asset pipeline will look for all of the files listed here in the Asset Path. Notice how we require calendar. This file lives in `app/assets/javascripts/calendar.js` yet we only specified the name and not the full path. The Asset Pipeline will search all the configured paths for a file with the name we provided.

Now that we solved the question of discoverability, let's talk about concatenation. Like we discussed earlier, we don't want to load our files in the browser one by one. It's better to perform one download then a bunch of small downloads from our browser. The manifests files we configure in Rails will automatically concatenate the files listed in them into one file in production. When we are developing our application this might not be the best option since it can make debugging hard but Rails will actually serve each file separately when we are running in development mode. No need to do anything.

Finally, the sprocket directives that power our asset manifests will be covered in detail later.

####PREPROCESSING
Being able to combine files and load them from a set of pre defined locations in our application is a great benefit of the Asset Pipeline. That's only the beginning. Because we're loading assets through Rails, we can preprocess the files using popular languages like SCSS for writing better CSS and Coffeescript for cleaner JS. If you make an asset named `theme.css.scss`, you are telling the asset pipeline to run the file through the SCSS preprocessor before serving theme.css to the browser. The SCSS preprocessor compiles the file into CSS. The only thing we had to do was provide the correct file extension, `.scss`, to the file and the asset pipeline knows to run it through the SCSS preprocessor.

####FINGERPRINTING
The last benefit we will talk about is Fingerprinting, but first let's talk about the problem it helps us solve. When we serve files to the browser, they are likely to be cached to avoid downloading them again in the future. What's caching you might ask?

Caching something means keeping a copy of a time consuming operation locally so that you don't have to re-do the expensive operation again if the inputs and outputs are going to be exactly the same. Cache's are usually key value stores, where the value is the answer to the expensive operation and the key is something that's unique to that item. If you request a page from the server, and then request the same page from the server again, the quickest way to get that request fulfilled is to actually keep a copy of what you got last time locally. Browsers cache lots of the responses they get to requests they've made by using the headers that get sent with the response. The headers tell the browers how long the page remains "fresh" before it "expires". Once the page has expired the browser will make a new request for the page to refresh its cache. We say that the fastest request is the request that's not made. It's also often said that cache invalidation is one of the two hard problems in computer science, so think carefully when you start caching things! Caching saves bandwidth for us and provides a speed boost for the user. This is great until you change the file and you want all of your users to get the new one instead of the old version they have stored in their browser cache. But how do we let the browser know we've modified the file? If the new version has the same name as the old version, the browser will continue using the old file from its cache. We need a way to change the filename when the contents change so that the browsers won't keep serving the old file.

Fingerprinting is a technique that makes the name of a filename dependent on the contents of the file. When the file contents change, the filename is also changed. For content that is static or infrequently changed, this provides an easy way to tell whether two versions of a file are identical, even across different servers or deployment dates.

When a filename is unique and based on its content, HTTP headers can be set to encourage caches everywhere (whether at CDNs, at ISPs, in networking equipment, or in web browsers) to keep their own copy of the content. When the content is updated, the fingerprint will change. This will cause the remote clients to request a new copy of the content. This is known as cache busting.

The technique sprockets uses for fingerprinting is to insert a hash of the content into the end of the file name. For example, take this CSS file name global.css. Sprockets will add the hash `908e25f4bf641868d8683022a5b62f54` to the end of the file name like this:

```
global-908e25f4bf641868d8683022a5b62f54.css
```

If you happened to be using an older version of Rails (Rails 2.x), the strategy used was to append a date-based query string to every asset linked with a built-in helper. This looked like this:

```
global.css?1309495796
```

The query string strategy has several disadvantages:

+ Not all caches will reliably cache content where the filename only differs by query parameters

Steve Souders recommends, "...avoiding a querystring for cacheable resources". 5-20% of your requests will not be cached. Query strings in particular do not work at all with some CDNs for cache invalidation.

+ The file name can change between nodes in multi-server environments.

The default query string in Rails 2.x is based on the modification time of the files. When assets are deployed to a cluster, there is no guarantee that the timestamps will be the same, resulting in different values being used depending on which server handles the request.

+ Too much cache invalidation

When static assets are deployed with each new release of code, the mtime (time of last modification) of all these files changes, forcing all remote clients to fetch them again, even when the content of those assets has not changed.

Fingerprinting fixes all these problems by ensuring that filenames are consistent based on their content.

Fingerprinting is enabled by default for production and disabled for all other environments. You can enable or disable it in your configuration through the `config.assets.digest` option.

####CONCLUSION
The Asset Pipeline is definitely more complex then just serving assets from a public folder and it can be hard to debug. Learning how to use it will pay off in the long run by saving us time and headaches. Just think about all the problems it solves for us.

1. Asset Paths
2. Manifests and Concatenation
3. Preprocessing
4. Fingerprinting

Finally, definitely check out the DHH Keynote where he introduces the asset pipeline, https://www.youtube.com/watch?v=cGdCI2HhfAU. It's good, I promise.

##Javascript Manifests
####OBJECTIVES
1. Create Javascript Manifest Files
2. Require Javascript Files in Manifests with Sprocket Directives
3. Include Javascript Manifest Files in Layouts
4. See a Manifest in Development vs Production

####OUTLINE
Our previous lesson gave us a good overview of the Asset Pipeline. We touch briefly on manifest files and how they provide a central place to list the assets we want to load. Let's dive deeper into the JavaScript manifest file.

####MANIFEST FILES
Manifest files have special characteristics about them that differentiate them from plain old JS files. If you open your JavaScript manifest, `app/assets/javascripts/application.js`, you will see that some of the lines start with `//=`. This is called a directive and it tells sprockets that this isn't a normal JS comment.

####`REQUIRE` DIRECTIVES
While there are a number of [different directives](https://github.com/rails/sprockets#sprockets-directives) available, let's learn about the one that is used to list the JS files we want to include in our application. This directive, `require`, tells sprockets that the file we are specifying should be loaded. One thing to note is you don't have to put the file extension. If your file is `main.js` then you only need to type `//= require main`.

You may notice another directive in your manifest file. The `require_tree` directive tells sprockets to load all files in the given folder. By default, the manifest file has `//= require_tree .` which will include all JS files in the same folder that `application.js` is located. This makes adding new JS files into our application really easy but can cause problems. As your application grows, you may not want all of the JS loaded everywhere. For example, say you have two pages that have a similiar button. You want those two buttons to behave differently even though they look the same. If all JS is loaded all the time, then the browser will not know what JS should be applied to these buttons. In the end, it's generally better to control what JS is being loaded for each page. Additionally, the files will be loaded in alphabetical order. Often, external libraries will have a dependency on another JS file being loaded before it, and all your Javascript will error out if this load order is not honored. Given that things load in alphabetical order it's unlikely things will magically be loaded in the right order. Check the console in your browser to see if you are getting these types of errors and if so manually require each file in the order you need it to be loaded and get rid of `require tree`.

One last thing to remember, when you require something in your manifest file, the path you provide must be the asset path. If you have the file `comments.js` in the folder `/assets/javascripts/blog` you would need to use `//= require blog/comments` to include it in our manifest file.

####LOADING A MANIFEST FILE IN YOUR LAYOUT
Now that we have our manifest file, we need to include it in our application. The only thing we need to do is use the javascript_include_tag in our application layout file. This is just a rails helper that generates a script tag instructing the browser to load that JS file.

**File: app/views/layouts/application.html.slim**

```html
<%= javascript_include_tag "application" %>
```

Sprockets will then take care of loading our manifest file and determining what assets to load. If we are in development mode, each asset will be loaded individually.

```html
<script src="assets/jquery.min.js" />
<script src="assets/bootstrap.min.js" />
<script src="assets/blogs.js" />
```

In production mode, all the assets will be combined into a single file.

```html
<script src="/assets/application-908e25f4bf641868d8683022a5b62f54.js" />
```

####RESOURCES
+ http://guides.rubyonrails.org/asset_pipeline.html

##Page Specific Javascript
####OVERVIEW
As we create our application, using a single file for all of our JS can cause a lot of headaches. As the file gets bigger and bigger it becomes harder to find anything. We want to organize our JS into different files based on their functionality. One way to do this is to have page-specific JS files.

####OBJECTIVES
1. Write and load page-specific Javascript in the Manifest
2. Load page-specific Javascript through `javascript_include_tag`
3. Creating a `content_for` page-specific JS and write Javascript in partials

####PAGE-SPECIFIC JS IN A MANIFEST
By default, when you use the [Rails Generators](http://guides.rubyonrails.org/generators.html) to create a new resource, Rails will create page-specific JS and CSS files. This is where we want to put any JS that is specific to the web pages associated with the resource. For example, say we have a Blog resource. We add JS that loads the comments for a blog after the page loads. This bit of JS should be put in the file `app/assets/javascripts/blogs.js.coffee` ([CoffeeScript](http://coffeescript.org/) is the default way to create JS for Rails). From here we just need to make sure our JS file is included in the JS manifest.

**File: `app/assets/javascripts/application.js`**

```javascript
//= require blogs
```

####CONTROLLER SPECIFIC JS
When the browser loads our JavaScript, it parses the entire file and runs it. With a big application this can be a lot of JS. Different pages might start to have functionality we don't want to share accross the application. An option to allow for a page-specific JS file to be loaded only with the pages we want is to use the name of the controller.

```html
<%= javascript_include_tag params[:controller] %>
```

Instead of adding `blogs.js` to the manifest file, we could instead load the file based off of the controller's name. We can place this in the head or below the body of our layout and it will load the JS file that matches the name of the controller.

A request made to `blogs#index` would result in params:
`{controller: 'blogs', action: 'index'}` and the `javascript_include_tag` will now load the `blogs.js` file. If we visit a page from a different controller, the `javascript_include_tag` will include the JS specific to that controller.

The downside of this is we'd no longer be getting the benefits of asset concatenation or caching. The browser will have to make a separate request for this file in addition to the request for the main concatenated `application.js` file. The benefit of this strategy could be that we're less likely to invalidate the cache for our entire JS file if it is in pieces.

####USING `CONTENT_FOR`
The final way to include page-specific JS is to use a `content_for :js` block in your layout. We can either put this in the head or below the body of the layout.

**File: `app/views/layouts/application.html.erb`**

```html
<head>
  <meta>
  <%= javascript_include_tag 'application' %>
  <%= yield :js %>
```

Then in your view
**File: `app/views/blogs/show.html.erb`**

```html
<% content_for :js do %>
  <script>
    alert('Some page-specific JS');
  </script>
<% end %>
```

Anything placed in our script tag will run only on our show page.

####CLASS BASED TARGETING
While both of those solutions work, some people find that they aren't the most elegant solutions to the problem. One way you can continue to keep things simple with the asset pipeline is to continue to have it concatenate all of your javascript together, but wrap all your pages in a div with a specific class for that page.

```html
<% app/views/layouts/application.html.erb %>
<body class="<%= controller_name %> <%= action_name %>">
  <%= yield %>
</body>
```

Assuming your `contact` page action was inside a controller named PagesController, the rendered result would be the following:

```
<body class="pages contact">
  ...
</body>
```

Now you could write some javascript like this

```javascript
$(".pages.contact").click(function() {
  console.log("only runs on the contact page!")
})
```

##External Javascript Libraries
####OUTLINE
Loading JavaScript into our application can be done many different ways in Rails. We can use HTML script tags for JS files located on another server. We can place third party JS files in our vendor folder. Finally we can even use gems to load the JS libraries we need.

####EXTERNAL JAVASCRIPT
Not all of our JavaScript files will be loaded from our application directories. Sometimes we may want to load JS from a CDN. This can allow us to save on bandwidth and if we have a CDN setup for our application, it will help with file download speeds for users throughout the world. To load JS like this, we create HTML script tags in our application layout file.

```html
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/js/bootstrap.min.js" />
```

####VENDOR ASSETS
Our applications will grow as we add more and more features. We might end up with a lot of different JS libraries along the way. The problem is all of these external JS files will need to be downloaded one by one and this will slow page load times. At some point, we will probably decide to make these external JS files to be internal JS files.

We don't really want to clutter our main `assets/javascripts` folder with code written and maintained by other people. This is why Rails provides the `vendor/assets/javascripts` folder. We can place third party JS libraries in here and add them to our JS manifest file. We also get the added benefit of having all of these external JS files combined into one file with all our application JS files. One thing to consider when doing this is if you are using a popular library, a user has probably already downloaded this JS file from another site they visited and is sitting in their cache. By adding this to your manifest you'd be forcing them to download the code again.

####GEMS
Manually adding JS to our vendor directory can be cumbersome and hard to maintain. New versions of the JS files come out and unless we're keeping track of every release, it's easy to fall behind. Luckily, many of the popular JS libraries we use have gems. These gems package the JS files and when installed, add them to our asset path allowing us to require the JS inside of our manifest file.

For example, let's install the jQuery gem in our Gemfile. Once we `bundle install`, we are able to add `//= require jquery` to our manifest file. Now jQuery will be loaded by Rails. Plus, we can use bundler to update jQuery when new versions are released. Handling updating is particular helpful for bigger JS frameworks that might have many JS files and dependencies. This makes our life simpler, but it also may cause problems. It's possible we don't want to update to the newest version of every JS file. The API of the library could have changed causing our code to break. It also is going to require the user to re-download the JS libary when there may be no need to. We also (as in the previous section) would be concatenating our JS libraries together which might lose the benefits of caching those libraries.

##CSS Manifests
####OBJECTIVES
1. Create CSS Manifest Files
2. Require CSS Files in Manifests with Sprocket Directives
3. Include CSS Manifest Files in Layouts
4. See a Manifest in Development vs Production

####OVERVIEW
CSS makes our web applications look good but can be hard to manage. As our application grows, so do the amount of stylesheets we need to manage. The Asset Pipeline can help us manage this chaos much like with JavaScript.

####MANIFEST FILES
Like our JavaScript manifest, the CSS manifest has a special syntax that differentiates it from a regular CSS file. The `*= require` directive is very similar to it's JS counterpart. We are just using a CSS comment instead of a JS comment.

```css
/*
*= require main
*/
```

**Note**: In a CSS manifest file you must open the CSS comment block with `/*` and then each directive appears on an individual line starting with `*=`. You close the comment block with `*/`. It's a little different than the JS directive comments of `//=` which are individual valid JS comments.

####`REQUIRE` DIRECTIVES
When we require a CSS asset in our manifest, if it is located in one of the configured folders, it will be included in our application. One thing to remember is when you require something the path you provide must be the asset path. For example, if you have the file `app/assets/stylesheets/blogs/main.css` you will need to require it like this, `*= require 'blogs/main'`.

####LOADING A MANIFEST FILE IN YOUR LAYOUT
Loading our CSS manifest file into our application is just as easy as it was with JS. We create a `stylesheet_link_tag` in our application layout and Sprockets takes care of loading the manifest and determining what assets to include.

```html
<%= stylesheet_link_tag 'application' %>
```

In development mode, each CSS file will get it's own link tag. This allows for easier debugging. A manifest file that looks like this:

```css
/*
*= require main
*= require blogs
*= require posts
*/
```

Would create this in our application layout's head tag: (assuming fingerprinting/digests are turned off)

```css
  <link rel="stylesheet" href="/assets/main.css" /> 
  <link rel="stylesheet" href="/assets/blogs.css" /> 
  <link rel="stylesheet" href="/assets/posts.css" /> 
```

####MANIFESTS IN PRODUCTION
In production mode, Sprockets will take all of our CSS files and create one large CSS file. It will also [minify](https://developers.google.com/speed/docs/insights/MinifyResources) the contents removing unneeded whitespace to reduce the overall size of the file. Both of these things will speed up page load times for our users. If we look at our previous manifest example:

```css
/*
*= require main
*= require blogs
*= require posts
*/
```

We would only get a single link tag from this in our application manifest.

```html
<link rel="stylesheet" href="/assets/application-4dd5b109ee3439da54f5bdfd78a80473.css" /> 
```

The CSS in this file would look like this:

```css
body{background-color:#FFF;font-size:14px;margin:0}a{color:#1B97F2;text-decoration:none}.clear{clear:both}ul{margin:4px 0;padding-left:17px}ul.horizontal{list-style:none;margin:0;padding:0}ul.horizontal li{margin:0;padding:0;float:left}#flash_notice,#flash_alert{padding:10px 0;text-align:center;color:#FFF}
```

Notice the lack of whitespace? That's the minification we taked about earlier. This is great for production use but it's a bit hard to read. This is why when we are in development mode, we get individual, unminified files instead.

####RESOURCES
+ https://github.com/rails/sprockets#sprockets-directives
+ http://guides.rubyonrails.org/asset_pipeline.html

##External CSS Frameworks In Rails
####OUTLINE
Loading stylesheets into our application can be done a few different ways in Rails. We can use HTML link tags for CSS located on another server. We can place third party CSS files in our vendor folder. We can also use gems to load the CSS frameworks we need like Twitter Bootstrap.

####EXTERNAL STYLESHEETS
So far we have been loading CSS from our asset directories. You aren't required to do it this way but it does allow us to easily manage our CSS. The other way to load CSS is by using standard HTML `<style>` tags placed in the `<head>` tag of our application layout. There are benefits to using this approach. We can load stylesheets from other peoples CDNs without having to set one up ourselves. This can save us bandwidth and help with download speeds for users throughout the world. To load CSS like this, we create HTML link tags in the `<head>` of our application layout file.

```html
<head>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css" />
</head>
```

####VENDOR ASSETS
Managing a lot of different third party CSS files can be hard. We may lose track which files are for which CSS frameworks. Also, separate link tags mean these files will need to be downloaded one by one on the browser and this will slow page load times. At some point, we will probably decide to have these external CSS files be internal CSS files.

We don't really want to clutter our main `assets/stylesheets` folder with CSS files maintained by others. Rails provides the `vendor/assets/stylesheets` folder for these types of CSS files. We can place our third party CSS frameworks in here and add them to our CSS manifest file. We get the added benefit of having all external CSS files combined into one file with all our application CSS files.

####GEMS
Manually adding CSS to our vendor directory can also be cumbersome and hard to maintain. New version of CSS frameworks are released and it's easy to fall behind. Luckily for us, many of the popular CSS frameworks have gems. These gems package up these CSS frameworks and when installed, add them to our asset path allowing us to require them inside of our CSS manifest file.

To install the Twitter Bootstrap gem in our Gemfile, you will need to add `gem "bootstrap-sass"` to your Gemfile and run `bundle install`. Once that completes you are able to add `*= require bootstrap` to the CSS manifest file. Now Bootstrap will be loaded by Rails. We can also use bundler to update Twitter Bootstrap when new versions are released. Handling updating is particular helpful for bigger CSS frameworks like Bootstrap that have many CSS files.

####REFERENCES
+ [Bootstrap SASS Gem](https://github.com/twbs/bootstrap-sass)

##Asset Preprocessors In Rails
OUTLINE
The Asset Pipeline is very powerful. We have seen this with just asset concatentation alone. Imagine having to manually combine dozens of JS and CSS files and updating the layouts everytime we want to make a change and deploy our application. The Asset Pipeline just does this for us. On top of that, it also allows us to use preprocessors like Coffeescript, SASS and erb. But what is a preprocessor?
PREPROCESSORS
CSS and JavaScript have been around awhile. They do a good job styling our pages and creating cool client-side behavior. They aren't always the easiest to work with, though. There have been a lot of improvements to these languages, but because those upgrades require people to upgrade their browsers, we can't always use them. Wouldn't it be great if we could write CSS and JS the way we wanted and have a program turn it into the correct syntax? This is why we have preprocessors.
Because the asset pipeline parses all of our asset files, we are able to write CSS and JS in a language that isn't exactly CSS and JS. By using file extensions, the asset pipeline can recognize these files as not normal CSS and JS and run it through a preprocessor, which compiles to valid CSS and JS. If the Asset Pipeline sees main.js.coffee it knows this is a Coffeescript file and will run it through the Coffeescript preprocessor. The asset pipeline runs through each file extension in reverse order running the preprocessor for that extension. For example, a file that ends in .js.erb will have the erb preprocessor run on it first, the goal being a file that is purely valid javascript. You can use Ruby to programatically write JS and even use variables from your controller in your JS!
In development mode, the asset pipeline will run the preprocessor on any file that needs to be processed every time it's requested, SLOW! In production you might have had to run rake assets:precompile. This runs the preprocessors once, outputs all the now-static JS and CSS and then allows the webserver to serve them without ever touching Rails, much faster!
COFFEESCRIPT
If you have written any JavaScript, you know it has many quirks. For example, the difference between == vs === when testing equality or how to use Inheritance. Coffeescript takes all of these things that make using JavaScript hard and abstracts them away. What we are left with is a very Ruby like syntax. Take the following pulled from the Coffeescript documentation.
launch() if ignition is on
 
volume = 10 if band isnt SpinalTap
 
letTheWildRumpusBegin() unless answer is no
 
if car.speed < limit then accelerate()
 
winner = yes if pick in [47, 92, 13]
 
print inspect "My name is #{@name}"
If you look closely you can see this syntax looks a lot like Ruby. Once we run it through the preproccesor, we get to following.
var volume, winner;
 
if (ignition === true) {
  launch();
}
 
if (band !== SpinalTap) {
  volume = 10;
}
 
if (answer !== false) {
  letTheWildRumpusBegin();
}
 
if (car.speed < limit) {
  accelerate();
}
 
if (pick === 47 || pick === 92 || pick === 13) {
  winner = true;
}
 
print(inspect("My name is " + this.name));
That's plain old JS with all of it's quirks but we didn't have to write it.
SASS
While CSS might not be as complex as JavaScript, it still has its problems. Without language features like selector nesting, basic inheritance and variables it can be hard to keep CSS manageable. CSS3 promises to provide much needed enhancements to the aging CSS 2.1 standard but just like JS, we can only use these new features once browsers support them and a majority of people upgrade to that browser version. This is where SASS comes in. SASS is a sort of enhanced CSS that, once run through the preprocessor, turns into plain CSS.
Here is a simple example of nesting which SASS provides.
.error {
  a {
    color: red;
    text-decoration: none;
  }
  span {
    color: #ff1a1a;
    text-decoration: underline;
  }
}
The code above will turn into the following once run through the preprocessor.
.error a {
  color: red;
  text-decoration: none;
}
.error span {
  color: #ff1a1a;
  text-decoration: underline;
}
Notice how we avoided having to repeat .error. In SASS, if we need to change .error to .error-text we only have to change it in one place. In big applications that can save us from making a lot of mistakes.
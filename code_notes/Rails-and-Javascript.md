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
####OUTLINE
The Asset Pipeline is very powerful. We have seen this with just asset concatentation alone. Imagine having to manually combine dozens of JS and CSS files and updating the layouts everytime we want to make a change and deploy our application. The Asset Pipeline just does this for us. On top of that, it also allows us to use preprocessors like [Coffeescript](http://coffeescript.org/), [SASS](http://sass-lang.com/) and erb. But what is a preprocessor?

####PREPROCESSORS
CSS and JavaScript have been around awhile. They do a good job styling our pages and creating cool client-side behavior. They aren't always the easiest to work with, though. There have been a lot of improvements to these languages, but because those upgrades require people to upgrade their browsers, we can't always use them. Wouldn't it be great if we could write CSS and JS the way we wanted and have a program turn it into the correct syntax? This is why we have preprocessors.

Because the asset pipeline parses all of our asset files, we are able to write CSS and JS in a language that isn't exactly CSS and JS. By using file extensions, the asset pipeline can recognize these files as not normal CSS and JS and run it through a preprocessor, which compiles to valid CSS and JS. If the Asset Pipeline sees `main.js.coffee` it knows this is a Coffeescript file and will run it through the Coffeescript preprocessor. The asset pipeline runs through each file extension in reverse order running the preprocessor for that extension. For example, a file that ends in .js.erb will have the erb preprocessor run on it first, the goal being a file that is purely valid javascript. You can use Ruby to programatically write JS and even use variables from your controller in your JS!

In development mode, the asset pipeline will run the preprocessor on any file that needs to be processed every time it's requested, SLOW! In production you might have had to run `rake assets:precompile`. This runs the preprocessors once, outputs all the now-static JS and CSS and then allows the webserver to serve them without ever touching Rails, much faster!

####COFFEESCRIPT
If you have written any JavaScript, you know it has many quirks. For example, the difference between `==` vs `===` when testing equality or how to use [Inheritance](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain). Coffeescript takes all of these things that make using JavaScript hard and abstracts them away. What we are left with is a very Ruby like syntax. Take the following pulled from the Coffeescript documentation.

```coffeescript
launch() if ignition is on
 
volume = 10 if band isnt SpinalTap
 
letTheWildRumpusBegin() unless answer is no
 
if car.speed < limit then accelerate()
 
winner = yes if pick in [47, 92, 13]
 
print inspect "My name is #{@name}"
```

If you look closely you can see this syntax looks a lot like Ruby. Once we run it through the preproccesor, we get to following.

```javascript
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
```

That's plain old JS with all of it's quirks but we didn't have to write it.

####SASS
While CSS might not be as complex as JavaScript, it still has its problems. Without language features like selector nesting, basic inheritance and variables it can be hard to keep CSS manageable. CSS3 promises to provide much needed enhancements to the aging CSS 2.1 standard but just like JS, we can only use these new features once browsers support them and a majority of people upgrade to that browser version. This is where SASS comes in. SASS is a sort of enhanced CSS that, once run through the preprocessor, turns into plain CSS.

Here is a simple example of nesting which SASS provides.

```sass
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
```

The code above will turn into the following once run through the preprocessor.

```css
.error a {
  color: red;
  text-decoration: none;
}
.error span {
  color: #ff1a1a;
  text-decoration: underline;
}
```

Notice how we avoided having to repeat `.error`. In SASS, if we need to change `.error` to `.error-text` we only have to change it in one place. In big applications that can save us from making a lot of mistakes.

##Images and the Asset Pipeline
####OBJECTIVES
1. Add images to the Rails Asset Pipeline
2. Construct image asset URLs.
3. Use asset_path and asset_url helpers.
4. Use image_tag helpers

####OUTLINE
Most web applications have images. Just like JavaScript and Stylesheets, the Asset Pipeline makes dealing with images easier. The Asset Pipeline helps us build paths and deal with caching problems.

####WHERE DO IMAGES GO?
Where do we put our images so the Asset Pipeline can find them? We can put them in the `app/assets/images` directory. By default, this path is already included in the asset paths. It's possible to add more image paths to the asset paths by adding to `Rails.application.assets.paths`. Once the additional path has been added, we can access it using the standard `/assets` url. The cool thing here is we can use one URL path that maps to a bunch of different folders. For example the URL "/assets/logo.png" will look in all the folders in the asset paths for that image, but no matter where it's located its URL never changes.

If you pop open the console and type in `Rails.application.assets.paths` you'll see an array of the paths Rails considers to be asset paths. So techically, you could add an image to `assets/stylesheets` and Rails would find it, but that would be confusing to your coworkers. Because it's an array, you can easily shovel in more directories if you have images in places not on the list. In your `application.rb` you could do something like this.

```ruby
#application.rb
config.assets.paths << Rails.root.join("lib", "myrandomimagefolder")
```

####`ASSET_PATH` HELPER
The first of the helpers available to help us create image paths is the `asset_path` helper. It provides a way for us to get a relative path to an image file in the Asset Path. To use this helper, we specify the path relative to the assets folder.

```ruby
asset_path('logo.png')
```

If the asset pipeline finds the file, it will return the path to the image.

```html
/assets/logo.png
```

If the file is in a sub-folder in the images directory (images/banner), we need to include that as well.

```ruby
asset_path('banner/logo.png')
```

```html
/assets/banner/logo.png
```

If the asset pipeline can't find the file, the path returned will be `/` plus the file name.

```html
/logo.png
```

The `asset_path` helper is great for image paths located in our CSS files.

```css
.logo {
  background-image: url(<%= asset_path('logo') %>);
}
```

####`IMAGE_TAG` HELPER
Most of the time we don't only care what the URL is for an image, we want to generate an HTML tag that will actually display the image for a user.

The `image_tag` helper creates an `<img>` tag with the path to the image.

```html
<%= image_tag "logo.png" %>
```

Will create:

```html
<img src="/assets/logo.png" />
```

If the asset pipeline can't find the file, an image tag will be created with the name of the file plus the '/' path.

```html
<img src="/logo.png" />
```

Since this image doesn't exist we will see a broken image on our web page.

####FINGERPRINTING
Just like with our JS and CSS, browser caching can cause problems when we decide to update an image. When we use either `asset_path` or `image_tag` to display our images, the Asset Pipeline will add a fingerprint to the file. This will only happen if we are running our application with fingerprinting on. By default it's on in both production and development. If you want to turn it off you can go to the environment file `config/environment/development.rb` and change `config.assets.digest = false`.
If we were to look at one of these paths, it would look like this.

```html
/assets/logo-331238805bdaebb4b05e9385bc1261f8.png
```

A digest is appended to the file name. If the file is updated, the digest will change. We won't have to worry about users having old versions of image files when we make changes.

####IMAGE TAGS
It is possible to predict the path to a image file and create our own image tag. This is not recommended since it will limit your ability to move your file and avoid caching problems that the helpers fix. ALWAYS USE THE HELPERS.

##Bootstrap and the Asset Pipeline
####OBJECTIVES
1. Identify the components included in bootstrap (namely the CSS and JS required to integrate bootstrap - two main files compressed and minified composed of individual parts).
2. Integrate Bootstrap manually with CDN (not preferred way).
3. Use the bootstrap gem and add the correct requires to your manifest.
4. Rely on bootstrap provided CSS and javascript to power the frontend.

####OUTLINE
Starting a web application can be overwhelming. There are so many descisions to make that it can be hard to manage. The last thing we want is to make a great application that looks terrible and doesn't respond to well to different screen sizes. It would be great if we could just add something to our application that made structuring our UI as easy as Rails makes everything else. [Twitter Bootstrap](http://getbootstrap.com/) does just that. It's a grid based CSS framework that provides the ability to create great looking, responsive web sites.

[Twitter Bootstrap](http://getbootstrap.com/) has some really good documentation as well. If you browse to their site and click on CSS in the menu you will see how to structure your HTML and CSS in a way that allows for easy responsive UI.

If you click on [Components](http://getbootstrap.com/components/), you will see how to use all the reusable components that Bootstrap provides. These are common parts of web sites like buttons, dropdowns and navigation.

Finally, if you click on [JavaScript](http://getbootstrap.com/javascript/) you will see how to use Bootstrap's JavaScript component to enhance our application. It makes creating modals and collapsing menus super easy.

####INTEGRATION
When including Bootstrap in our application, we will most likely want to include the whole framework. This can be done a few different ways.

This first option, which is not preferred because it doesn't take advantage of the asset pipeline, is to use a `<link>` tag in the `<head>`.

```html
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css" integrity="sha384-1q8mTJOASx8j1Au+a5WDVnPi2lkFfwwEAa8hDDdjZlpLegxhjVME1fgjWPGmkzs7" crossorigin="anonymous">
```

and a `<script>` tag at the bottom of the `<body>` tag.

```html
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/js/bootstrap.min.js" integrity="sha384-0mSbJDEHialfmuBBQP6A4Qrprq5OVfW37PRR3j5ELqxss1yVqOtnepnHVP9aJ7xS" crossorigin="anonymous"></script>
</body>
```

Our preferred way is to use a gem. To do this, include the following in our Gemfile.

```ruby
gem 'bootstrap-sass'
```
 
Now run `bundle install`. Finally, include the following in your CSS manifest file.

```css
@import "bootstrap-sprockets";
@import "bootstrap";
```

And the following in your JavaScript manifest file.

```javascript
//= require bootstrap-sprockets
```

That's it! Now we have Boostrap ready to go and any additional updates only require a 'bundle update'

####USING BOOTSTRAP
Let's say you've created an amazing design for your web application. You tweaked the margins and padding just right, and the content aligned just the way you want. The whole time you've had your browser in full screen so you decide to shrink your browser window to see what happens only to discover that your awesome design doesn't look so awesome now. Next, you take out your phone and browse to your application. To your dismay, your web site is even worse on your phone. It's hard to read and requires a ton of scrolling and zooming to see anything.

To solve our problem, we could create completely new designs for each screen size but this can easily become hard to manage. Everytime you add a new feature, it has to be added in multiple places and customized for that specific layout. This is the problem that [Responive Web Design](https://en.wikipedia.org/wiki/Responsive_web_design) aims to fix. By using [media queries](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries) and careful design, we can create a web site that changes depending on the screen size. Twitter Boostrap helps us manage the complexities of responsive web design by providing a grid system to work with.

####GRID LAYOUT
Bootstrap's grid system divides the screen into rows with 12 columns. These columns contain our content. We can choose how to layout our site by creating 12 individual columns or fewer bigger columns. For example, here is a grid divided into 12 columns.

```html
<body>
  <div class="container">
    <div class="row">
      <div class="col-lg-1">
        <p>Column #1</p>
      </div>
      <div class="col-lg-1">
        <p>Column #2</p>
      </div>
      <div class="col-lg-1">
        <p>Column #3</p>
      </div>
      <!-- ... columns 4 through 11 ... -->
      <div class="col-lg-1">
        <p>Column #12</p>
      </div>
    </div>
  </div>
</body>
```

Each column would be vary narrow and probably not be able to fit much content. If we want we could also just have two bigger columns.

```html
<body>
  <div class="container">
    <div class="row">
      <div class="col-lg-6">
        <p>Column #1</p>
      </div>
      <div class="col-lg-6">
        <p>Column #2</p>
      </div>
    </div>
  </div>
</body>
```

Notice how instead of 12 `<div>` tags with the CSS class `col-lg-1` we use 2 `<div>` tags with the CSS class `col-lg-6`. The combined total of these two columns is still 12. This is an important aspect of Bootstrap. We should always be able to add the columns together to get a total of 12.

From here there is a lot of different ways we could configure our grid to respond to different screen sizes in different ways. Refer back to the documentation and examples to help.

####REFERENCES
+ [Twitter Bootstrap](http://getbootstrap.com/)
+ [Responive Web Design](https://en.wikipedia.org/wiki/Responsive_web_design)
+ [media queries](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries)

##How to Find Gems
####OBJECTIVES
1. Understand what to look for when choosing a gem.
2. Know where to find popular gems.

####THE RUBYGEMS ECOSYSTEM
We've been using gems like crazy at this point, from RSpec for testing to Rails for ... Rails-ing. Almost any general problem we need to solve has already been solved by someone else and released as a gem, allowing Ruby and Rails developers to reach incredible levels of productivity because we get to skip all that wheel-reinventing that Java programmers do all day.

**//Flat-fact**: Reinventing wheels is actually what Java programmers do as a side hustle while they're waiting for compiles.

Working in Ruby is such a joy not just because of the language itself, but also because of the people who freely give back to their fellow developers in the form of gems.

####DO I EVEN NEED A GEM?
The wealth of gems (pun absolutely intended) available for everyone to use in their projects is one of the things that sets Ruby apart from other languages, but it can also lead to *Gem Madness* - a condition where gem-crazed developers look for a gem to solve every problem without considering if a gem is really needed.

Before we go looking for a gem to solve a problem, we should take a little time to decide if the problem we want to solve is really so big that we need a gem, or if we might be able to figure out (or Google) a way to just write the code ourselves.

####WHERE TO LOOK
Okay, we know we need a gem, where do we find one?

We can actually look for gems right on our command line. Try this out in your console:

```
gem search ^twitter$ -d
```

The `gem search` command can take a regular expression and can be very handy if you know the name (or part of the name) of a gem and want to find it quickly.

However, that only searches the name, so if you need to search in a little more open way, the obvious choice is Google. A search of `rails gem problem-description` will probably get you there pretty quick.

Google, however, isn't great at letting us know if we want the gem it shows us (more on this later), so there's a couple of great resources that provide more context to lists of gems.

[Ruby Toolbox](https://www.ruby-toolbox.com/) is a site that aggregates and categorizes gems, and ranks them according to stats like total downloads, releases, and active commits.

Ruby Toolbox is *comprehensive*, which is a fancy way of saying "it's kind of a lot". Great if you're looking for something a little more obscure, or really want to explore all the options, but sometimes we want a more curated list of what gems are popular in the community.

Enter [Awesome Ruby](https://github.com/markets/awesome-ruby) and [Awesome Rails Gems](https://github.com/hothero/awesome-rails-gem), two open-source, community maintained lists of the most popular gems for Ruby and Rails by category.

**Note**: There are "Awesome" lists for a lot of things, from languages to free programming books and everything in between. Check out the [index](https://github.com/sindresorhus/awesome).

####QUALITIES TO LOOK FOR WHEN CHOOSING A GEM
Adding a gem to your project means you are taking on a *dependency* on outside code. If that gem has security problems, your project has security problems. If that gem has bugs, your project has bugs. If that gem doesn't keep up with newer versions of Rails, or even one of the gems that your new dependency might depend on, you might be stuck dealing with compatibility problems.

So let's look at some strategies for selecting high quality, reliable, and safe gems for our project.

####HOW POPULAR IS IT?
This is one time when we want to care about popularity contests. The more popular a gem is, the more likely it is to be maintained, to keep up with security issues, and new Rails versions.

Beyond that, the more popular a gem is, the more likely you are to be able to find help if you run into problems. Popular gems have a lot of questions and answers on StackOverflow. People like to blog about popular gems so they can get those sweet sweet clicks. All that adds up to you feeling confident that you can use that gem.

Some ways to test the popularity are:

1. Look it up on Ruby Toolbox. See how many downloads there are, how recently it's been committed to, and how many versions there are.
2. See if it's on the Awesome Ruby or Awesome Rails lists mentioned above.
3. Google the gem name and see what's there, and, importantly, how recent it is. Try things like "gem name tutorials" and "gem name errors".
4. Search StackOverflow for the gem and read what people say about it.
5. Seach Github for usage of the gem. You'll need to read the gem's documentation or code and pick out a class or method to search for. For example, searching Github for `has_attached_file`, which is a primary method in the [Paperclip](https://github.com/thoughtbot/paperclip) gem yields over 100K results, so it's obviously in wide use.

####HOW WELL-MAINTAINED IS IT?
We want our gems to be popular, but we also need them to be well-maintained. A well-maintained gem will be far less likely to cause you problems down the road.

Some ways to determined if a gem is well-maintained are:

1. Read the code. Is it put together in the way you'd expect? Can you understand it? Does it follow Ruby idioms and styles?
2. Look for documentation. Is it well-documented? The README, at a bare minimum, should tell you how to get up and running. Even better if there's a wiki with examples and sample code.
3. Look at issues and pull requests. How long do they stay open? How active are the committers in addressing issues and PRs? Are there problems with a specific version that haven't been resolved. 
**Hint**: This could also be an opportunity to do some open source contributing!
4. Look for tests. Is there a `/test` or `/spec` directory? How many tests are there? Is there a link to a continuous integration build result? Examining the tests is always a great way to learn how the gem works and how well it's put together.

####SUMMARY
Gems are great. They're a powerful tool and crucial to your success as a Ruby on Rails developer. But just because there's a gem out there for everything, doesn't mean that every gem is the best one to use, so do some due diligence when choosing what to add to your Gemfile.

##Paperclip
####OBJECTIVES
1. Use the Paperclip gem to upload photos as attachments to models.
2. Use a default image when nothing has been attached.
3. Understand image processing and resizing with Paperclip.

####LESSON
We're going to be using the Paperclip gem to add author pictures to our blog application so that readers can match a face to a post. Make sure to run `rake db:seed` to set up some test data!

####SETTING UP PAPERCLIP
In our blog application, we want to be able to show the author's headshot, so we're going to turn to the popular gem [Paperclip](https://github.com/thoughtbot/paperclip) to help us out. We could certainly write our own code to handle the image uploads, but as we'll see, Paperclip offers so much in terms of integrating with our ActiveRecord models, configuring storage options, and processing images after upload.

To set up Paperclip, first we need to install the [ImageMagick](http://www.imagemagick.org/script/index.php) dependency. Paperclip uses ImageMagick to resize images after upload.

On OS X, the preferred way to install ImageMagick is with [Homebrew](http://brew.sh/):

`brew install imagemagick`

Once we have ImageMagick, let's add Paperclip to our Gemfile:

```ruby
# Gemfile
 
#...
gem "paperclip"
```

Run `bundle install` to finish it up. If your Rails server is already running, remember to restart it so that it has access to the new gem.

####ADDING AN AVATAR TO AN AUTHOR
Now that we have Paperclip installed, let's jump right into adding avatar images to our `Author` model.

First, we need to wire up our model to use Paperclip's `has_attached_file` method, and tell it what attribute name we want to use to access the attached file. In this case, we'll go with `avatar`.

```ruby
# models/author.rb
 
class Author < ActiveRecord::Base
  has_many :posts
  has_attached_file :avatar
  validates_attachment_content_type :avatar, content_type: /\Aimage\/.*\Z/
end
```

The `validates_attachment_content_type` validator is provided by Paperclip, and ensures that we get an image file when we expect one. This validator is required by default.

Now we need to add the `avatar` field to our table via a migration. Paperclip provides a generator for us, so we can run:

`rails g paperclip author avatar`

This will generate a migration that looks something like:

```ruby
class AddAvatarColumnsToUsers < ActiveRecord::Migration
  def up
    add_attachment :authors, :avatar
  end
 
  def down
    remove_attachment :authors, :avatar
  end
end
```

**Note**: Paperclip provides two new methods for use in migrations: `add_attachment` and `remove_attachment`. Because these are custom attachment methods, Rails won't know how to automatically reverse an `add_attachment` migration, so you need `up` and `down` methods in the migration rather than simply using `change`.

Run `rake db:migrate` to add the column.

Now we need to set up the form view to give us a way to upload an avatar. Let's add that to the author form partial:

```html
# views/authors/_form.html.erb
 
<%= form_for @author, html: {multipart: true} do |f| %>
  <%= f.label :avatar %>
  <%= f.file_field :avatar %><br>
  <%= f.label :name %>
  <%= f.text_field :name %><br>
  <%= f.label :bio %>
  <%= f.text_area :bio %>
  <%= f.submit %>
<% end %>
```

Adding `html: { multipart: true }` as a parameter to `form_for` will generate a form that knows it will be submitting both text and binary data to the server. Then we use the `file_field` helper to generate the appropriate input and "choose" button to attach the file.

Finally, we need to update our strong params in the controller to allow the new `avatar` field to be used for mass assignment:

```ruby
# authors_controller.rb
 
# ...
 
  private
 
  def author_params
    params.require(:author).permit(:bio, :name, :avatar)
  end
```

Now if we run our server with `rails s` and browse to `/authors/new`, we should be able to create an author with an avatar.

####DISPLAYING THE AVATAR
We've attached the avatar, but now we need to display it to the user. Here's where Paperclip really starts to shine.

Let's add the avatar to our author `show` view:

```html
# views/authors/show.html.erb
 
<h1><%= @author.name %></h1>
<%= image_tag @author.avatar.url %>
<p><%= @author.bio %></p>
<p>Posts:</p>
# ...
```

Simple as that! Paperclip provides the `url` method on our `avatar` so that no matter where it's stored, we can always use it in an `image_tag`. Now we're starting to see why using Paperclip is more efficient than doing it ourselves.

Let's also add the avatar to the author `index` view, so we can see them in the list.

```html
# views/authors/index.html.erb
 
#...
  <% @authors.each do |author| %>
    <li>
    <%= image_tag author.avatar.url %>
    <%= link_to author.name, author_path(author) %><br>
    <%= author.bio %>
    </li>
  <% end %>
#...
```

Now if we load `/authors`, we'll see avatars. Unless they don't have one. Now what?

####SETTING DEFAULT AVATARS
Happily enough, Paperclip gives us an elegant way of dealing with missing avatars that doesn't involve us having to go into every view that displays an avatar and writing a bunch of logic.

Let's go back to our model and tell `has_attached_file` what the default is when no file has been attached:

```ruby
# author.rb
 
class Author < ActiveRecord::Base
  has_many :posts
  has_attached_file :avatar, default_url: ':style/default.png'
  validates_attachment_content_type :avatar, content_type: /\Aimage\/.*\Z/
end
```

You can store default images to match each style, e.g. in this case we would have a thumbnail default as well as an original default. You just need the supporting folders in your `app/assets/images` directory to match the style names ("original" is the default, unprocessed style).

**Note**: We have to place an image named `default.png` in our `app/assets/images/thumb` `and app/assets/images/original` folders (already provided), that part doesn't automatically happen via Paperclip (Image)Magick.

Reload `/authors`. No more broken images!

####GENERATING THUMBNAILS WITH PAPERCLIP
Depending on how big an author's photo is, our `index` page might have wildly different-sized images in the list.

We can easily constrain those with CSS, but what we really want to do is create a thumbnail to be used in the list so we aren't loading a bunch of giant images and slowing things down.

In order to do this, we're going to have to write a lot of low-level code using ImageMagick to process the avatar.

Kidding! Of course Paperclip already does this for us! Let's get back into our `Author`.

```ruby
# author.rb
 
class Author < ActiveRecord::Base
  has_many :posts
  has_attached_file :avatar, default_url: ':style/default.png', styles: { thumb: "100x100>" }
  validates_attachment_content_type :avatar, content_type: /\Aimage\/.*\Z/
end
```

And just like that, Paperclip will process the image and create a thumbnail style for us. To use it in the view, simply pass the symbol for the style to the `url` method:

```ruby
# views/authors/index.html.erb
 
#...
  <% @authors.each do |author| %>
    <li>
    <% image_tag author.avatar.url(:thumb) %>
    <%= link_to author.name, author_path(author) %><br>
    <%= author.bio %>
    </li>
  <% end %>
#...
```

If we go to `/authors/new` and create an author, then return to `/authors`, we'll see that the thumbnail has been generated for the new author, but now one or more of our existing author's thumbnails is a broken image.

That's because we added images to those authors before we defined the `:thumb` style, so we need to tell Paperclip to update our existing images with the new style. Fortunately, Paperclip provides a rake task for exactly
that.
On the console, run `rake paperclip:refresh:missing_styles`, then refresh that `/authors` page and we should have thumbnails for everyone.

####SUMMARY
We've seen how easy it is to upload and image attachments in our application using the Paperclip gem, have default avatars, and create thumbnails with very little code.

##Kaminari
####OBJECTIVES
1. Use the Kaminari gem to paginate blog posts.

####LESSON
We're going to be using the Kaminari gem to paginate our blog posts so that each page is more manageable.

Start by running `rake db:seed` on the included app. This is going to create a bunch of posts. Next run your server and browse to `/posts`.

What a nightmare, right? So many posts on one page! What we need is an easy way to only show a certain amount of posts per page, and allow the user to go through them page by page.

####KAMINARI TO THE RESCUE
Kaminari is a Japanese word meaning "thunder", and the obvious link between that and this lesson is that the [Kaminari](https://github.com/amatsuda/kaminari) gem makes pagination a slam dunk.

To get started, we'll add Kaminari to our Gemfile:

`gem 'kaminari'`

Then run `bundle install` to get it installed.

Now let's run `rails g kaminari:config` to generate a configuration file for Kaminari. You can find the file in `config/initializers`. Let's look inside:

```ruby
# config\initializers\kaminari_config.rb
 
Kaminari.configure do |config|
  # config.default_per_page = 25
  # config.max_per_page = nil
  # config.window = 4
  # config.outer_window = 0
  # config.left = 0
  # config.right = 0
  # config.page_method_name = :page
  # config.param_name = :page
end
```

These are the defaults. The one we're interested in is
`default_per_page`, which sets up how many results will be in each page.

Let's change that to 10 (don't forget to un-comment it).

```ruby
# config\initializers\kaminari_config.rb
 
Kaminari.configure do |config|
  config.default_per_page = 10
# ...
```

Restart your Rails server to make sure the new gem and initializer are picked up.

Okay. Now let's get into the controller and set our query up to use Kaminari.

```ruby
# controllers\posts_controller.rb
 
# ...
  def index
    @posts = Post.order(created_at: :desc).page(params[:page])
  end
# ...
```

Here we're getting posts by most recent and then using the `page` method of Kaminari to get a "page" (ten, in our case) of results. We're passing `params[:page]` to the page method so that we can control *which* page we get. And if `params[:page]` is nil, we'll get the first page, so it works by default.

If you reload your `/posts` page, you should see that we're now limited to ten results, which is much more manageable!

But how do we get to the next page of results?

Kaminari provides us with plenty of helpers to output navigation controls. Let's start by adding regular pagination controls with the `paginate` helper:

```html
# views\posts\index.html.erb
 
<h1>Blog Posts!</h1>
<%= paginate @posts %>
 
<% @posts.each do |post| %>
# ...
```

Reload the page and we now have the ability to go next, previous, first, last, or by page number.

If we want to add a little more contextual information, we can use the `page_entries_info` helper, like this:

```html
<h1>Blog Posts!</h1>
<%= page_entries_info @posts %>
<%= paginate @posts %>
<% @posts.each do |post| %>
# ...
```

And we can further customize our display, like, displaying only two page numbers on either side of the current page by passing in a value for `window`:

```html
<h1>Blog Posts!</h1>
<%= page_entries_info @posts %>
<%= paginate @posts, window: 2 %>
<% @posts.each do |post| %>
# ...
```

More customization options for `paginate` can be found in the Kaminari [README](https://github.com/amatsuda/kaminari).

####SUMMARY
We've seen just how easy it is to use Kaminari to quickly add pagination to large datasets in our app, despite still having no idea why it's named after thunder.

##Active Admin
####OBJECTIVES
1. Use the ActiveAdmin gem to add admin features to the blog app.
2. Customize the ActiveAdmin resource to prevent certain actions and hide form fields.

####LESSON
We're going to use [ActiveAdmin](https://github.com/activeadmin/activeadmin) to add administrative features to our blog application. Not anyone should just be able to come in and create a new `author` on our blog. That's something only an admin should do.

Our app already has [Devise](https://github.com/plataformatec/devise) set up for user management. We're going to leave our main blog pages open to the public and only password-protect the ActiveAdmin parts.

####SETTING UP ACTIVEADMIN
To get started, let's add ActiveAdmin to our Gemfile:

`gem 'activeadmin', github: 'activeadmin'`

We need the `github: 'activeadmin'` part to use the latest under-development version, which is compatible with Rails 4.2.

Then run `bundle install`.

**Note**: We're going to follow along with the [github docs](https://github.com/activeadmin/activeadmin/blob/master/docs/0-installation.md), because the ActiveAdmin website is out-of-date.

Now we generate the ActiveAdmin installation with `rails g active_admin:install`. This will create a migration for an `AdminUser` model, add a default user to our `seeds.rb` file, and generate the base ActiveAdmin files.

Let's run `rake db:migrate` then `rake db:seed` to finish the setup.

Finally, restart your Rails server, then browse to `http://localhost:3000/admin`.

We have a login page! Log in as `admin@example.com` with password `password` and you'll be on the ActiveAdmin dashboard.

**Top-tip**: You can change the login and password in `seeds.rb` before migrating, or even better, log in and change the login/password from within the app to make it more secure.

####REGISTERING MODELS
Okay, we have a dashboard, but there's not much to do. That's because we haven't registered any models yet.

Let's set this up to administer `authors`.

`rails generate active_admin:resource Author`

This will create a new file at `app/admin/author.rb`. We're not even gonna look at it yet. Trust me. Look away!

Just reload `/admin` and you should see a new item in the top bar for `Authors`. Click it. You can add new authors, edit and delete existing ones, all from a single command and no code!

####CUSTOMIZING ACTIVEADMIN RESOURCES
Out of the box it's incredibly easy to set up ActiveAdmin to provide admin features. But you might want to give some restrictions to what it can do.

First, let's prevent authors from being deleted, even by ActiveAdmin. *Now* you can look at `app/admin/author.rb`.

We want to make sure we're set up to use Strong Params to only allow certain fields, so let's set that up:

```ruby
# admin/author.rb
 
ActiveAdmin.register Author do
    permit_params :name, :genre
end
```

**Note**: In some instances, you will be required to set `permit_params` even if you don't want to change any of them from the default. If you receive an `ActiveModel::ForbiddenAttributes` exception when working with ActiveAdmin, set up those `permit_params`.

Now we're only allowing `name` and `genre` to be set.

By default, all CRUD actions are available to an ActiveAdmin resource. But we can change that:

```ruby
# admin/author.rb
 
ActiveAdmin.register Author do
 
  permit_params :name, :genre
  actions :all, except: [:destroy]
 
end
```

If we refresh the authors admin page, the "Delete" link is now gone.

If we want to edit the elements on the form, and remove `bio` so that the form matches our `permit_params`, we can override the default form:

```ruby
# admin/author.rb
 
  permit_params :name, :genre
  actions :all, except: [:destroy]
 
  form do |f|
    inputs 'Author' do
      f.input :name
      f.input :genre
    end
    f.semantic_errors
    f.actions
  end
```

Reload again and bio is gone. This `form do ... end` block uses an ActiveAdmin domain-specific language (DSL) to manipulate a [Formtastic](https://github.com/justinfrench/formtastic) form. Removing it completely returns the form to the default.

**Advanced**: You might be tempted to think of `admin/author.rb` as if it were a kind of model, but as you can see, it controls all aspects of an ActiveAdmin resource, including both business logic and presentation logic. One could make the argument that this is a violation of Separation of Concerns, but in Rails a "resource" is a concept that comprises model, controller, and view, so in that sense, an ActiveAdmin's resource is concerned with all aspects of that resource. In any case, it's easy to find where the code goes.

####SUMMARY
We've seen how easy it is to use ActiveAdmin to quickly add administration to our app, and how to do some basic customization. More configuration options are available and can be found in the docs, but I think we can all agree that the default options are very helpful and easy to use.

##Long Running Tasks in Rails
####OBJECTIVES
1. Create a long-running task and observe its impact on the user experience.

####LESSON
Most of the things we do in Rails apps revolve around simple tasks that affect only one or a few database objects at a time, and are handled in milliseconds, quickly enough that the impact on the request/response time of our pages is minimal.

Sometimes, however, we need to do something that takes longer to do and heavily impacts page load time, like sending a bunch of emails, uploading large files, manipulating a lot of records, or generating a report. Let's look at how that might happen.

####IMPORTING RECORDS FROM CSV
A common task in many systems is importing records from a Comma-Separated Value (CSV) file. You might need to do this for any number of reasons, from seeding a new database to integrating and sharing data from another system.

Attached is a very simple "customer database" app. It has a list of all customers at /customers.

Our sales team has just aquired some hot leads and wants them in the system right away.

Turns out we'll be getting new leads via a CSV every week, so instead of importing them manually, we'll just build a feature where the sales team can do it themselves.

We'll let them do the import right from the customers `index`, so let's just add a route to take the file upload:

```ruby
# config\routes.rb
 
  resources :customers, only: [:index]
  post 'customers/upload', to: 'customers#upload'
```

Then let's add the controls to upload a file to our `index` view:

```ruby
# views\customers\index.html.erb
 
<h1>Our Customers</h1>
<%= form_tag customers_upload_path, multipart: true do %>
  <%= file_field_tag :leads %>
  <%= submit_tag "Import Leads" %>
<% end %>
<ul>
<% @customers.each do |customer| %>
# ...
```

Finally, let's get into our controller and handle this file upload.

To do that, we first need to examine the file. Check out `db/customers.csv`. The first row defines the fields, and isn't part of the dataset, so that's called a `header`.

Each row is ordinal, like an array, so position 0 is email, position 1 is first name, and position 2 is last name.

Armed with all that, we can build our controller method:

```ruby
# controllers\customers_controller.rb
 
class CustomersController < ApplicationController
  require 'csv'
 
  def index
    @customers = Customer.all
  end
 
  def upload
    CSV.foreach(params[:leads].path, headers: true) do |lead|
      Customer.create(email: lead[0], first_name: lead[1], last_name: lead[2])
    end
    redirect_to customers_path
  end
 
end
```

**Note**: We're using the `CSV` library (`require 'csv'`), which is a standard Ruby library, to do the processing. You can learn more about it [here](http://ruby-doc.org/stdlib-1.9.3/libdoc/csv/rdoc/CSV.html).

Okay, let's reload `/customers`, select `customers.csv` from our project's `db` directory, and click go.

Wait for it.

Keep waiting.

Okay, eventually, the page should refresh and we'll see our new customers. Depending on the speed of your computer, it might have taken upwards of a full minute for 25,000 rows. 25,000 might seem like a lot, but systems routinely process millions and millions of rows of data every day.

####SUMMARY
We've seen how something as straightforward as creating records from a data file can take almost a minute for your server to process and reload a page. And while a minute might seem pretty fast to create 25K records, consider it from the standpoint of the user. Even as developers who know how long things take, if we have to wait more than a couple seconds for a page to load, we get frustrated, so clearly this isn't a great experience.

##Sidekiq
####OBJECTIVES
1. Use Sidekiq to move a long-running task to a background worker

####LESSON
If a long-running task takes *too* long, it will frustrate users and possibly cause time-outs and lead to errors.

In our case, our sales team is happy that we gave them a way to upload leads, but they want to be able to get right back to contacting customers and not have to wait for the uploading and processing to complete.

####WORKERS
In Rails, we talk about *workers* as processes that will allow us to execute tasks outside of the main web application thread.

The reason it takes so long to load `/customers` after uploading our file is that we are processing the file on the same thread as the request/response. File processing becomes a *blocking* action on the thread, and nothing else can happen until that's done, causing the sales
person to sit and stare at a blank screen for a minute.

Fortunately, in the Rails ecosystem, we have a lot of tools we can use to move blocking tasks to a background worker and allow the main thread to continue, providing a much snappier response for the user.

####SIDEKIQ
One of the most popular background worker tools is [Sidekiq](https://github.com/mperham/sidekiq).

We'll use Sidekiq to move our leads processing upload to a background thread so that our sales people can go back to work on the `/customers` page right away.

Let's get it started.

First we add Sidekiq to our Gemfile and run `bundle install`:

```ruby
gem 'sidekiq'
```

Sidekiq depends on [Redis](http://redis.io/), which you'll need to install. If you're running on OS X, you can run

```
brew install redis
```

You can also always [download Redis](http://redis.io/download) manually or exercise your Google-fu to figure out the best installation for your setup.

Sidekiq relies on a `Worker` to define and process a job. Let's add an `app/workers` directory and create our first worker:

```ruby
# app/workers/leads_worker.rb
 
class LeadsWorker
  include Sidekiq::Worker
 
  def perform(leads_file)
 
  end
end
```

That's the basic shape of any worker. You'll `include Sidekiq::Worker` and define a `perform` instance method that takes in whatever data is required to complete the job. In our case, it will be our leads file.

Now that we have our worker, we need to move the processing from the controller to the worker. All you have to do is take the long-running code out of one place, and put it inside of `perform`:

```ruby
# app/workers/leads_worker.rb
 
class LeadsWorker
  require 'csv'
  include Sidekiq::Worker
 
  def perform(leads_file)
    CSV.foreach(leads_file, headers: true) do |lead|
      Customer.create(email: lead[0], first_name: lead[1], last_name: lead[2])
    end
  end
end
```

We've taken the loop that processes the file in our controller, and just put it inside of `perform`. Make sure to `require 'csv'` at the top, and update the `CSV.foreach` to work with `leads_file`.

Now, in our controller, we want to tell it to run this worker rather than process the file inline:

```ruby
# controllers/customers_controller.rb
class CustomersController < ApplicationController
 
  def index
    @customers = Customer.all
  end
 
  def upload
    LeadsWorker.perform_async(params[:leads].path)
    redirect_to customers_path
  end
end
```

And that's it. We're now set up to run the file upload in a background worker.

To see it in action, you'll first need to start Redis: `redis-server` (on OS X you can also follow the instructions that brew info redis prints to start Redis automatically using launchctl.) Then run your Rails server (don't forget to restart) in one tab, and open a new Terminal tab to run Sidekiq with this command:

```
bundle exec sidekiq
```

Then go to `/customers` and try it out. Uploading the `db/customers.csv` file should immediately redirect you to `/customers`, where you can continue your work, and periodically refresh to see new entries!

If you watch the tab running Sidekiq, you'll see something like:

`LeadsWorker JID-bca14e512da7b759ad6cf37b INFO: start`

That's how you know the job has started, and you can start refreshing the page.

####SUMMARY
We've seen how to improve the user's experience and keep our application responsive by using Sidekiq to offload long-running tasks into a background worker.

##What is an API?
####OBJECTIVES
1. Understand the difference between transmission of data vs data and presentation.
2. Understand the value of APIs.

####LESSON
Let's say you're building an app that allows users to find the nearest coffee shop that is most likely to have an open table where they can sit and work on their novel.

You need to be able to search for coffee shops and find out things like how many people have recently gone there.

One of the great things about the web is that we can find all kinds of data and use it in new and interesting ways. In this case, we know that Foursquare deals with exactly that sort of thing, so we'll try to get data from them.

####SCREEN SCRAPING
One thing we could do is *screen-scrape* Foursquare. Screen scraping is a technique where you request a URL in the same way a web browser would, but you do so in code so that instead of seeing rendered HTML, you have to *parse* the HTML to see what you want.

So, we could go to Foursquare and use their search feature and look at the URL generated by the search to figure out how to create our own request. In this case, searching for coffee shops in New York would generate a URL like this:

`https://foursquare.com/explore?mode=url&near=New%20York&q=coffee%20shops`

We could then use [Nokogiri](https://github.com/sparklemotion/nokogiri) and Ruby to request that page and parse the data.

```ruby
# irb
doc = Nokogiri::HTML(open('https://foursquare.com/explore?mode=url&near=New%20York&q=coffee%20shops'))
```

This will give us the full search results page from Foursquare. We could inspect that source and figure out that the results are all in `<li>` elements with a `singleRecommendation` class, and do something like:

```ruby
# irb
doc.css('li.singleRecommendation').each do |recommendation|
  # ...
end
```

And basically keep drilling in from there, trying to figure out how to reliably extract a name, address, ID, and so on, from each result. Many hours later we can feel good about getting this data.

Now what happens when Foursquare does a minor CSS tweak and changes `.singleRecommendation` to `.recommendationCard`.

Our whole app is broken! And we won't know about it until a user gets an error. And Foursquare has no responsibility to us because scraping their page is not an intended use of their system, they wouldn't know that we're doing it, and wouldn't be able to tell us about the change even if they wanted to.

Okay, instead of spending hours and hours parsing their new HTML, let's see if they have an API.

####APIS
An API (Application Programming Interface) is a way for one system to interact with another via a well-defined *interface*. An interface is any endpoint that can be used to take actions and consume data on a given application. The HTML response of every web site is an interface, for instance.

The web interface provides HTML, which is a combination of *data* (the dynamic values like venue names, addresses, and check-ins) and *presentation* (HTML tags, CSS classes, JavaScript effects) meant to be consumed by a web browser. This is great for end users who want to see something visually compelling.

Some applications, knowing that their *data* might be of use to other applications, go a step further, providing an API as well as the standard web interface.

The benefit of an API is that it's put together explicitly for the purpose of being used by another system, rather than being viewed by a human, so the *data* that it returns is unencumbered by the *presentation*, preventing the consuming code from having to wade through HTML just to find values. A good API just provides data in the easiest format possible for code to digest.

####JSON
So if a web interface provides HTML, what does an API provide?

Most modern web APIs use JavaScript Object Notation, or JSON, as a standard way of describing and defining data.

**Note**: Some APIs, usually older ones, will use XML rather than JSON to describe data, though that is increasingly rare. However, the remnants of that great stone age are found in AJAX, which stands for Asynchronous JavaScript and *XML*, XML being the standard when AJAX hit the scene. When we use AJAX today we're almost always using JSON rather than XML, but we haven't changed the name because *AJAJ* would be a weird acronym.

JSON looks a lot like a Ruby hash, and as a result, figuring out how to access a given piece of data is a snap.

```ruby
{
  "name": "Cuppa Joe's",
  "address": "123 Not A Fake Address Street",
  "city": "Real City",
  "state": "New York",
  "zip": "10015"
}
```

Much better than trying to find all that in a bunch of HTML tags, right?

**//Flat-fact**: JSON is canonically pronounced like the name "Jason".

####FINDING AND USING APIS
Many websites have APIs that you can consume with your applications. The easiest way to find out if the site you want to use provides an API is to Google `sitename api`, or just go to the site and look for a link to a "Developers" or "API" section.

For instance, if we go to [Foursquare](http://foursquare.com/) and look in the navigation links at the bottom, we'll see a [Developer](https://developer.foursquare.com/) link that we can start to explore, and from there we can find a [venue search](https://developer.foursquare.com/docs/venues/search) API endpoint that will allow us to do exactly what we were trying to do above with our screen scraping.

Most good APIs will have extensive documentation about how to use the API, what functions are available, and what data you can access. Be sure to read through the documentation very carefully. Every API is a little bit different, and documentation quality varies, so you'll need to be analytical and methodical, and prepared for some trial and error, when you're learning to consume a new API.

Most good APIs also use REST, just like Rails, so finding your way around should start to feel familiar and consistent as you learn what resources are available. Keep in mind, though, that you will only have access to what the API creator allows. Not all applications open up all features for API access, so make sure what you need is available ahead of time.

####SUMMARY
We've looked at getting data from other websites via screen scraping, and how cumbersome and brittle it is trying to integrate data with presentation, like an HTML page. We've learned what an API is, how it differs from an interface meant to be consumed by a browser, and how to find out if a site offers an API.

##APIs And Postman
####OBJECTIVES
1. Use Postman to explore an API
2. Read API documentation
3. Understand the value of and how to use Client IDs and Secrets

####LESSON
We're going to explore the Foursquare API and learn how to read API documentation.

####TOOLS
The first thing we want to do is install a couple of tools to make our lives a lot easier.

[Postman](https://www.getpostman.com/) is a Chrome app that we can use to easily make API requests. Check out the docs and install the app from [here](https://www.getpostman.com/docs/).

[JSONView](https://chrome.google.com/webstore/detail/jsonview/chklaanhfefbnpoihckbnefhakgolnmc) is a Chrome extension that will automatically make JSON data much more readable in Chrome.

####FOURSQUARE VENUE API
Okay, let's try to get venue data on Foursquare. We'll check out the [Venues endpoint](https://developer.foursquare.com/docs/venues/venues) in the documentation.

Here's how we read this page:

**Venue Detail** is the name of the function, or *endpoint*, that we are going to use, and its URI takes the format: `https://api.foursquare.com/v2/venues/VENUE_ID`. This RESTful URI looks a lot like one we'd use in a Rails app to allow access to a given venue.

The document tells us that we will need to access this endpoint with the HTTP `GET` verb, and that `VENUE_ID` is a parameter that we will need to provide.

**Top-tip**: Generally speaking, you will use `GET` for endpoints that are read-only, and `POST`, for endpoints that alter data, but be sure to read the documents carefully because not every API is implemented the same.

It then links to a *response* documentation that tells us each field we can expect in the JSON response, and what it means.

Enough talk, let's try it! In your browser address bar, enter `https://api.foursquare.com/v2/venues/40a55d80f964a52020f31ee3` and see what we get.

Oh. We get an error code.

####API CREDENTIALS
If we'd paused to read the [introductory documentation](https://developer.foursquare.com/start), we'd see that we need to create an app on Foursquare to use the API. Creating an app will give us a client ID and a secret that we can use as credentials for the API.

Most API providers require a similar process to use their API. Even though you can publicly access much of this data through the website, API providers want to know who is accessing their API. There are several reasons for this, but an important one is quality control.

Let's say one bad application is using the API in unintended ways, perhaps making too many requests in a short period of time. This is problematic because it can negatively affect other API users who are behaving appropriately. By requiring a client ID/secret authorization, Foursquare can know who the offender is and turn off their access, ensuring quality for other API users.

Okay, but why can't we just log in to Foursquare with our normal account and use it that way?

While there are some functions in an API that may require an individual user to be authenticated, the client ID/secret pair authorizes the *application* access the API. You wouldn't want to embed your personal account information in the application, allowing users to perform actions as if they were you, would you?

This client ID/secret pair isn't actually a "login". It's just a way of securely identifying and allow an application to make requests.

Let's follow the instructions to [create a Foursquare app](https://foursquare.com/developers/apps) so that we can get a client ID and secret, then.

**Hint**: You can use `http://localhost:3000` as your web address.

Once you've created your app, you should see your Client ID and Client Secret on your app page.

####A FULL API REQUEST
Now that we have them, we can try that request again with our app credentials. Enter the URL with your client id and secret into your browser:

`https://api.foursquare.com/v2/venues/40a55d80f964a52020f31ee3?client_id=YOUR_CLIENT_ID&client_secret=YOUR_SECRET&v=20150201`

If you put in your client ID and secret, you should see some JSON that looks like this:

```json
response: {
  venue: {
    id: "40a55d80f964a52020f31ee3",
    name: "Clinton St. Baking Co. & Restaurant",
    contact: {
    phone: "6466026263",
    formattedPhone: "(646) 602-6263"
  },
  location: {
    address: "4 Clinton St",
    crossStreet: "at E Houston St",
    lat: 40.72107924768216,
    lng: -73.98394256830215,
    postalCode: "10002",
    cc: "US",
    city: "New York",
    state: "NY",
    country: "United States",
    formattedAddress: [
      "4 Clinton St (at E Houston St)",
      "New York, NY 10002",
      "United States"
    ]
  },
// ...
```

**Note**: Remember that with a `GET` request, we pass parameters via a *querystring* by putting a `?` after the url and then formatting parameters like this: `param=value&param2=value`.

Typing all that into the URL bar of our browser every time is tedious, and makes it easy to make a mistake, so we can use Postman, which willmake it much easier to deal with parameters, headers, and anything else we might need to do to explore an API.

Open postman, then enter the base URL in the URL field: `https://api.foursquare.com/v2/venues/40a55d80f964a52020f31ee3`. Then click `Params`, and you can enter each parameter separately below. Enter the following three things in the appropriate columns:

```
URL Parameter Key        Value
client_id               YOUR CLIENT ID
client_secret           YOUR SECRET
v                       20160201
```

We'll talk about what `v` does in a minute. Hit "Send", and you should get the same JSON result as we did in the browser.

####API VERSIONING
The `v` parameter we passed in was a version parameter that the Foursquare API requires. Try taking it out by deleting the `v` parameter and value from Postman and hitting "Send" again.

You should see an error about the API requiring a version parameter. You can read the included link to learn more.

So what is versioning?

Versioning is an important tool in API use. When an API changes, either because of new features or changed endpoints, not all API clients will be able to keep up with those changes immediately. If you just changed the API and released it, any application using the API would run the risk of being broken until they updated their code.

This is akin to the problem we discovered with screen scraping when the website changes.

By versioning the API, you can guarantee that anyone using the current version can keep using it, and upgrade to the new version when they can. You leave the old endpoints in place (e.g. `https://api.foursquare.com/v1/venues`) and roll out a new one under a new version namespace (e.g. `https://api.foursquare.com/v2/venues`). This way everyone on v1 can keep working, and the API provider doesn't break every application that uses the site.

In Foursquare's case, they require the `v` parameter as a way of you saying "I am prepared for code as of this date". This versioning parameter becomes a form of contract between the API provider and consumer.

####SUMMARY
We've looked at consuming an API with Postman, using an application Client ID/Secret to authorize your API calls, and the importance of API versioning.

Spend some time poking around the Foursquare API documentation and use Postman to try out the various endpoints.

##Hitting APIs with Faraday
####OBJECTIVES
+ Send GET requests to an API
+ Translate api requests in Postman to api requests with Faraday
+ Handle the response of a request

####LESSON
Let's use the [search](https://developer.foursquare.com/docs/venues/search) venue API to find a place to grab some coffee. Read through the documentation and use Postman to construct an API call to find coffee shops near you.

We want to pass parameters of `near` and `query`, along with our `client_secret`, `client_id`, and `v` parameters.

If we did it right, we should get a JSON response with an array of `venues`, each of which should conform to the [venue](https://developer.foursquare.com/docs/responses/venue) documentation.

Great! Now that we know how to query the API and get the results we want with Postman, how do we actually do this in code?

We're going to use [Faraday](Faraday) to access the Foursquare API from our Rails application. The basic app is set up, and you can code along as we add the feature to search for nearby coffee shops.

####FARADAY
Faraday is an HTTP client library that abstracts and standardizes some lower-level HTTP functions and makes it easy to build requests and get responses from an API.

**Advanced**: In this lesson, and indeed in most cases, you'll use the built-in [Net::HTTP](http://ruby-doc.org/stdlib-2.3.0/libdoc/net/http/rdoc/Net/HTTP.html) library for HTTP functions. However, other libraries offer different advantages in categories like performance ([Patron](http://toland.github.io/patron/)) or multi-threading capability ([Typhoeus](https://github.com/typhoeus/typhoeus#readme)). Using a library like Faraday, that wraps and abstracts these lower-level libraries, allows you to maintain the same client code even if you change the underlying library later.

Add Faraday to the `Gemfile` and run `bundle install`:

```ruby
gem 'faraday'
```

Our `searches/search.html.erb` is already set up to post a `:zipcode` param to our `searches_controller`, so let's get in there and add the Foursquare API call with Faraday:

```ruby
# searches_controller.rb
  def foursquare
    Faraday.get 'https://api.foursquare.com/v2/venues/search' do |req|
      req.params['client_id'] = client_id
      req.params['client_secret'] = client_secret
      req.params['v'] = '20160201'
      req.params['near'] = params[:zipcode]
      req.params['query'] = 'coffee shop'
    end
    render 'search'
  end
```

Let's break this down. We use `Faraday.get(url)` to make a `request` to the API endpoint.

We know we need to set some `params` from our tests in Postman, and Faraday gives us an easy way to do this by passing a block to the `get` method and adding any parameters we need through the request object via a hash of `params`, very similar to the way we use params in Rails.

To keep it simple, we're just going to render the `search` template again with the result.

**Hint**: Remember to replace `client_id` and `client_secret` with your own ID and secret!

Let's run our Rails server and navigate to `/search`, and make a search!

Okay. Anticlimactic.

####THE RESPONSE OBJECT
When we're working with Faraday, any time we make a *request* (such as `Faraday.get`), the returned object represents a *response*. The two most interesting parts of the response object are the `body`, which is where the server response, such as error messages or JSON results will be, and the `status`, which is the [HTTP code](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes) the server returns from the request.

So let's make a change and get that response into a variable we can use:

```ruby
# searches_controller.rb
  def foursquare
    @resp = Faraday.get 'https://api.foursquare.com/v2/venues/search' do |req|
      req.params['client_id'] = client_id
      req.params['client_secret'] = client_secret
      req.params['v'] = '20160201'
      req.params['near'] = params[:zipcode]
      req.params['query'] = 'coffee shop'
    end
    render 'search'
  end
```

And in our template, let's take a look at that response `body`.

```html
# views/searches/search.html.erb
<h1>Search for Coffee Shops Near Me</h1>
<%= form_tag '/search' do %>
  <%= label_tag :zipcode %>
  <%= text_field_tag :zipcode %>
  <%= submit_tag "Search!" %>
<% end %>
<div>
  <%= @resp.body%>
</div>
```

Let's run another search and see what we get.

Great! A lot of JSON! That's progress.

####PARSING THE RESPONSE
We know we're getting back JSON, and we can see from the [documentation](https://developer.foursquare.com/docs/venues/search) that we can expect a response that includes an array of `venues`.

Looking at this response `body`, we see something like this:

```
{"meta":{"code":200,"requestId":"56c0be42498e71008fcc8cc1"},"response":{"venues":[{"
```

Okay, we see the `response` node, and we see it has a `venues` node, so that looks right. We also see a `meta` node that gives us some information that's, well, metadata on our request. We'll look at this more later.

So lets get those `venues` out of this JSON object and into a thing we can use in Ruby. We'll do that with the built-in `JSON.parse` method:

```ruby
# searches_controller.rb
# ...
    @resp = Faraday.get 'https://api.foursquare.com/v2/venues/search' do |req|
      req.params['client_id'] = client_id
      req.params['client_secret'] = client_secret
      req.params['v'] = '20160201'
      req.params['near'] = params[:zipcode]
      req.params['query'] = 'coffee shop'
    end
 
    body_hash = JSON.parse(@resp.body)
    @venues = body_hash["response"]["venues"]
    render 'search'
```

So we parse the `body` of the response into a variable called `body_hash`, which is now a Ruby `Hash`. We can then access the `venues` under the `response` key.

In our `search.html.erb`, we can change `<%= @resp.body %>` to `<%= @venues %>` and re-run our search, and we can see the venues are now represented as an `Array` of `Hash`es that we can further parse.

We'll look at the documentation again and decide what fields we want, and come up with something like this:

```html
# search.html.erb
# ...
<div>
  <% if @venues %>
    <ul>
    <% @venues.each do |venue| %>
      <li>
        <%= venue["name"] %><br>
        <%= venue["location"]["address"] %><br>
        Checkins: <%= venue["stats"]["checkinsCount"] %>
      </li>
    <% end %>
    </ul>
  <% end %>
</div>
```

We're now successfully using the Foursquare API to get coffee shops near the user!

####HANDLING ERRORS
Okay, let's search again, but this time don't enter a zipcode. Oof. That's ugly.

Now we could add some validation here and make sure that there's a zipcode, but we can't necessarily prevent every possible error with the API, because we aren't in control of the API.

So we need to understand how the API reports errors, and then handle them.

We said before that the two interesting pieces of the `response` object are the `body`, where all the data is, and the `status`. The status contains the HTTP code.

In most cases, a good API call will result in a status of `200`, which is the HTTP equivalent of the thumbs-up emoji.

If we make this request in Postman without a value in the `near` parameter, we'll get something like this:

```json
{
  "meta": {
    "code": 400,
    "errorType": "param_error",
    "errorDetail": "Must provide parameter ll",
    "requestId": "56c0cccd498e39675a357932"
  },
  "response": {}
}
```

And there will be a message that says `Status 400 bad request`. 

So we have a `status` of 400, a meta node with some error information, and a `response` node that's empty, which makes sense, because we haven't made a good request.
Using this information, we can add some error handling to our application.

```ruby
# searches_controller.rb
  @resp = Faraday.get 'https://api.foursquare.com/v2/venues/search' do |req|
    req.params['client_id'] = client_id
    req.params['client_secret'] = client_secret
    req.params['v'] = '20160201'
    req.params['near'] = params[:zipcode]
    req.params['query'] = 'coffee shop'
  end
 
  body = JSON.parse(@resp.body)
  if @resp.success?
    @venues = body["response"]["venues"]
  else
    @error = body["meta"]["errorDetail"]
  end
  render 'search'
```

**Note**: We could have checked for `@resp.status == 200` as well, but Faraday provides us with a nice abstraction in `.success?`, which abstracts the low-level HTTP details into something readable and *intention-revealing*, which is a hallmark of good code.

And in our view, let's handle the possibilities:

```html
# search.html.erb
# ...
<div>
  <% if @error %>
    <p><%= @error %></p>
  <% elsif @venues %>
    <ul>
    <% @venues.each do |venue| %>
      <li>
        <%= venue["name"] %><br>
        <%= venue["location"]["address"] %><br>
        Checkins: <%= venue["stats"]["checkinsCount"] %>
      </li>
    <% end %>
    </ul>
  <% end %>
</div>
```

Now we're using the API's error response to display what went wrong. We might decide that the default error message is not as friendly as we'd like, and choose to display a friendlier message to our users. That's one of many considerations when using an API - how much to alter the data you receive, but it's also one of the best things about using an API, because you get to make those choices.

**Top-tip**: Remember, not all APIs will structure their responses the same way. Always read the documentation!

####HANDLING TIMEOUTS
One thing you must always be aware of when accessing resources across the internet on servers out of your control is the possibility of a request timeout.

Request timeouts can happen for any number of reasons, from network outages to code on the API server that just takes too long to execute.

To handle the timeout error, we just need to `rescue Faraday::TimeoutError` in our controller.

Luckily, we can also force a timeout by setting the request's `timeout` value. Normally you'd set this to a higher number than the default if the resource you were requesting was consistently too slow, however, if we set it to `0`, we can make it timeout to test our error handling:

```ruby
# searches_controller.rb
# ...
  begin
    @resp = Faraday.get 'https://api.foursquare.com/v2/venues/search' do |req|
        req.params['client_id'] = client_id
        req.params['client_secret'] = client_secret
        req.params['v'] = '20160201'
        req.params['near'] = params[:zipcode]
        req.params['query'] = 'coffee shop'
        req.options.timeout = 0
      end
      body = JSON.parse(@resp.body)
      if @resp.success?
        @venues = body["response"]["venues"]
      else
        @error = body["meta"]["errorDetail"]
      end
 
    rescue Faraday::TimeoutError
      @error = "There was a timeout. Please try again."
    end
    render 'search'
```

If we run our search again, we should see our timeout error.

Now let's delete that `req.options.timeout = 0` line, because we would never want to actually force a timeout on every request. Now we have a Foursquare API search that successfully finds coffee shops near the user, handles response errors, and guards against timeouts!

####SUMMARY
We've seen how to use Faraday to consume the Foursquare API from within our Rails app, how to use the documentation to parse the response, and how to handle for common errors. Time to celebrate!

##Using OAuth With APIs
####OBJECTIVES
1. Understand what OAuth is and why we use it.
2. Use OAuth with the Foursquare API.

####LESSON
We're going to expand on our Coffee Shop example to use OAuth with the Foursquare API to perform actions on behalf of an individual user.

####API USER AUTHENTICATION
Until now, we've been authenticating to APIs at the *application* level using a Client ID/Secret pair with each request.

As we learned, this level of application authentication is important in that it allows the API provider to ensure good behavior by any client application.

Application-level authentication like this gives us access to application-level functions, for instance, venue search. This is an application-level function because you don't need to be any particular user to search for a coffee shop. In fact, if we look at the [venue search](https://developer.foursquare.com/docs/venues/search) API documentation, we see an entry: **Requires Acting User: No** at the top.

Compare that to the documentation for [list friends](https://developer.foursquare.com/docs/users/friends), and we see that this function does require an acting user, which makes sense, because the application doesn't have friends, an individual user does.

So, if we want to show our user what their friends are doing, we need a way of authenticating the user to Foursquare through our application.

It's not enough for a user to be logged in to Foursquare and use our app, because Foursquare needs to know that we are acting on *behalf* of a user and that the user has *allowed* that action.

We could write some code to log in on behalf of the user, but that would require us to ask the user for their Foursquare login credentials. Think about that as if you're the user. Are you going to give some random website your login and password for Foursquare? The very thought should make you very suspicious of our motives.

Beyond the security issues, there's the problem of keeping things up to date. If we ask the user to log in to Foursquare on every request, that's a horrible experience.

However, if we store the user's Foursquare login info, we have whole other problems.

The first is that we'd have to store them unencrypted, in plain text, because Fourquare expects a person to log in with their unencrypted credentials. So we have another security problem. Now we have a database full of unencrypted Foursquare credentials waiting for anyone to get in there and grab them all.

Beyond the security issue is a logistical one. If the user changes her password on Foursquare, we'll never know about it, and our app will break. Now she has to update her Foursquare credentials in two places, which is cumbersome to say the least.

####ENTER OAUTH
Okay, how do we authenticate a user without them entering their username and password into our application? [OAuth](https://en.wikipedia.org/wiki/OAuth).

OAuth provides a way for one application to authenticate to another on behalf of a user by means of a revokable, expirable token. If our application has a Foursquare token for the user, and someone gets access to our database of tokens, all of them can be revoked and a hacker never gets access to individual user credentials.

An OAuth token also provides a standard way for our application to tell Foursquare "hey, this user says we can do things for her", because part of implementing OAuth requires the user to take action on the provider (Foursquare, in this case), explicitly authorizing our application.

####OAUTH AUTHENTICATION FLOW
OAuth authentication workflow involved three steps at a high level.

1. Request access from the provider site (often via redirect to a special form)
2. Redirect back to our site with a code
3. Request a token from the provider using the code

Take a look at the [Access token section of the Foursquare API Documentation](https://developer.foursquare.com/overview/auth), specifically the second subsection, under **Code (preferred)**. It describes how to do those three steps with Foursquare.

####CHECKING AUTHENTICATION
The first thing we want to do is figure out if a user has already authenticated to Foursquare in this session.

Ultimately, users will be considered "logged in" if they have an access token stored in their session. So, let's create a private method `#logged_in?` in your `ApplicationController` that will return false if `session[:token]` is `nil` and `true` otherwise:

```ruby
# application_controller.rb
 
private
  def logged_in?
    !!session[:token]
  end
```

####REDIRECT USERS TO REQUEST FOURSQUARE ACCESS
The first step in the OAuth flow is to direct the user to Foursquare to request access if we haven't already.

According to the [docs](https://developer.foursquare.com/overview/auth), that URL looks like this:

```
https://foursquare.com/oauth2/authenticate
    ?client_id=YOUR_CLIENT_ID
    &response_type=code
    &redirect_uri=YOUR_REGISTERED_REDIRECT_URI
```

Part of our URL includes passing a `redirect_uri` parameter, so we'll need to set that up. Update your [Foursquare app](https://foursquare.com/developers/apps) and add a Redirect URI. Let's set it to `http://localhost:3000/auth` and save the app.

Because we're going to be using our client ID/secret a lot, instead of always typing it in, let's use a `.env` [file](https://github.com/bkeepers/dotenv) to hold our `FOURSQUARE_CLIENT_ID` and `FOURSQUARE_SECRET` values. Once that's set up, we'll be able to access these values as `ENV['FOURSQUARE_CLIENT_ID']` and `ENV['FOURSQUARE_SECRET']`, which is much easier to keep track of. Follow the instructions to set up Dotenv and add your app's client ID and secret. Don't forget to restart your server after you change any values in `.env`!

**Top-tip**: Dotenv is a great way to keep configuration variables for development, but always remember to add `.env` to your `.gitignore` so you don't share your secrets with everyone else!

Okay, that should handle everything we need to make this first request, so now we need to set up the redirect.

Write another private method `#authenticate_user` that will redirect the user to `https://foursquare.com/oauth2/authenticate` *if* the user is not already logged in. Then we'll set up a `before_action` to check authentication.

```ruby
# application_controller.rb
before_action :authenticate_user
 
private
 
  def authenticate_user
    client_id = ENV['FOURSQUARE_CLIENT_ID']
    redirect_uri = CGI.escape("http://localhost:3000/auth")
    foursquare_url = "https://foursquare.com/oauth2/authenticate?client_id=#{client_id}&response_type=code&redirect_uri=#{redirect_uri}"
    redirect_to foursquare_url unless logged_in?
  end
 
  def logged_in?
    !!session[:token]
  end
```

Once you've implemented `#authenticate_user`, set the authentication as a `before_action` in your `ApplicationController`. In your `SessionsController`, skip the `before_action` with `skip_before_action :authenticate_user, only: :create`. Now, whenever users do not have an access token stored in their session, they will be redirected to the Foursquare authorization URL.

Let's try it out. Start your Rails server and try to hit the `/search page`. You should get redirected to Foursquare! Hit the "Allow" button and let's see what happens.

Error. Okay. That's good. That means it's working so far. Now we need to implement step two.

####FOURSQUARE REDIRECTS BACK TO OUR SITE WITH A CODE
When you registered your application, you set your redirect URL to `http://localhost:3000/auth`. This is where Foursquare is sending users after the login process. Now we need to handle that request.

In your `routes.rb` file, add a route for `get '/auth', to: 'sessions#create'`. This will route that redirect to our `SessionsController` and a `create` action, which is where we'll get the token. So now let's implement that.

####REQUEST A TOKEN FROM FOURSQUARE WITH THE CODE
Back to our handy [documentation](https://developer.foursquare.com/overview/auth)! Foursquare redirects users with a code that can be accessed through `params` and exchanged for an access token with a second GET request. We'll need to provide our ID, secret, and the code we just got.

We're going to use [Faraday](https://github.com/lostisland/faraday) to send this request in our controller action. If all goes well, according to the docs we can expect a JSON response with an `access_token`, so we'll parse that and put it in our `session[:token]`.

```ruby
# sessions_controller.rb
skip_before_action :authenticate_user
 
def create
  resp = Faraday.get("https://foursquare.com/oauth2/access_token") do |req|
    req.params['client_id'] = ENV['FOURSQUARE_CLIENT_ID']
    req.params['client_secret'] = ENV['FOURSQUARE_SECRET']
    req.params['grant_type'] = 'authorization_code'
    req.params['redirect_uri'] = "http://localhost:3000/auth"
    req.params['code'] = params[:code]
  end
 
  body = JSON.parse(resp.body)
  session[:token] = body["access_token"]
  redirect_to root_path
end
```

**Top-tip**: Make sure to skip the `authenticate_user` `before_action` when you're creating a session, otherwise you'll end up in an infinite loop of trying to figure out who the user is, which is a very existential bug.

####USE THE ACCESS TOKEN TO ACCESS THE API
Now that users have their API tokens, they can make calls to all of the API endpoints as long as those tokens are included in the request. Back in our [Foursquare auth docs](https://developer.foursquare.com/overview/auth), under the **Requests** section, we see that all we have to do now is add a `oauth_token` parameter to any request with the user's token.

Let's look again at the [friends](https://developer.foursquare.com/docs/users/friends) documentation and add a friends list to our application. First, let's add a route to `/friends`:

```ruby
# routes.rb
# ...
get '/friends', to: 'searches#friends'
```

And then handle that in our controller:

```ruby
# searches_controller.rb
 
def friends
  resp = Faraday.get("https://api.foursquare.com/v2/users/self/friends") do |req|
    req.params['oauth_token'] = session[:token]
    # don't forget that pesky v param for versioning
    req.params['v'] = '20160201'
  end
  @friends = JSON.parse(resp.body)["response"]["friends"]["items"]
end
```

**Top-tip**: Like many API providers, Foursquare gives you a way to try API calls right from the documentation. Try it with the [friends list](https://developer.foursquare.com/docs/explore#req=users/self/friends) to examine the response JSON.

Finally, let's set up our view:

```html
# views/searches/friends.html.erb
<ul>
  <% @friends.each do |friend| %>
    <li><%= "#{friend['firstName']} #{friend['lastName']}" %></li>
  <% end %>
</ul>
```

Now load `/friends` and, just like that, they'll be there for you!

##Posting to APIs
####OBJECTIVES
1. Understand what POST params are
2. Send POST requests with params via Postman and Faraday
3. Understand the difference between POST params and JSON in the body

####LESSON
We're going to keep working with our Foursquare application and allow users to add tips to venues. The solution from the OAuth reading is included.

**Note**: Don't forget to set up your `.env` file (or copy it from the previous lesson)!

####WORKING WITH A POST API ENDPOINT
If we look at the [Add Tip documentation](https://developer.foursquare.com/docs/tips/add), we'll see that we need to make this request via POST. Up until now we've been making GET requests, because we're just asking for data. But once we start changing data on the server, we're in POST territory.

According to the docs, we'll need to POST to `https://api.foursquare.com/v2/tips/add` with a `venueId`, some `text`, and, since it requires an acting user, our `oauth_token`. If you need a refresher on how to do OAuth with Foursquare, click the [learn more link](https://developer.foursquare.com/overview/auth) next to where it says **Requires Acting User**.

####POSTING WITH POSTMAN
Let's try this out in Postman first.

We'll need our oauth token to do this. Fortunately we already have this in our app, so to get it into Postman, we're going to temporarily display it on the page so we can copy and paste it:

```html
# searches\search.html.erb
<%= session[:token] %>
# ...
```

Output `session[:token]` to the search page, run your Rails server, and browse to /search. It should look something like: `B2JPA4ZSC5244X1Q1C2OPWYGTZ2LSALF1Z`

**Note**: We would *never* actually output the token in a real-world scenario, we just need it to play around in Postman. We'll delete that line shortly.

Next we need a venue ID. If you go into Postman, you should see a list of previous queries on the left side under **History**. Choose one where you successfully queried the Venue Search API and run it. Grab the `id` value from the first venue result.

Armed with those, let's start a new POST request (make sure to change the selection from GET to POST to the left of the URL field).

So, we're going to POST to `https://api.foursquare.com/v2/tips/add`, then we need to set our params per the docs:

```
venueId      YOUR_VENUE_ID
text         "Love this place!"
oauth_token  YOUR_TOKEN
v            20160201
```

Don't forget that pesky `v` parameter for Foursquare versioning!

If we've done it right, when we hit "Send", we should get a response that looks something like this:

```json
"response": {
    "tip": {
      "id": "56c223e1498e82b67c98f548",
      "createdAt": 1455563745,
      "text": "This place is great!",
      "type": "user",
      "canonicalUrl": "https://foursquare.com/item/56c223e1498e82b67c98f548",
      "likes": {
        "count": 0,
        "groups": []
      },
// ...
```

Just like that, we've created a tip!

####POSTING IN RAILS WITH FARADAY
Okay, we've seen it work in Postman, now let's get it into our Rails application. We want to create a way to POST a tip on a venue after we've searched for it.

Let's start with a form in our search results:

```html
# search.html.erb
<h1>Search for Coffee Shops Near Me</h1>
<%= form_tag '/search' do %>
  <%= label_tag :zipcode %>
  <%= text_field_tag :zipcode %>
  <%= submit_tag "Search!" %>
<% end %>
<div>
  <% if @error %>
    <p><%= @error %></p>
  <% elsif @venues %>
    <ul>
    <% @venues.each do |venue| %>
      <li>
        <%= venue["name"] %><br>
        <%= venue["location"]["address"] %><br>
        Checkins: <%= venue["stats"]["checkinsCount"] %><br>
        <%= form_tag '/tips' do %>
          <%= hidden_field_tag :venue_id, venue["id"] %>
          Add tip: <%= text_field_tag :tip %><%= submit_tag "Add Tip" %>
        <% end %>
      </li>
    <% end %>
    </ul>
  <% end %>
</div>
```

We need to keep track of the venue ID for when we create the tip, so we'll hold onto it in a `hidden_field_tag` on the tip form.

**Note**: Now is a good time to delete the line that outputs `session[:token]`.

Now we need to set up a route for our form to POST to, and a route to see our tips after we've added them:

```ruby
# routes.rb
# ...
resources :tips, only: [:index, :create]
```

Let's get into the `TipsController` and set up the `create` action:

```ruby
# tips_controller.rb
#...
  def create
    resp = Faraday.post("https://api.foursquare.com/v2/tips/add") do |req|
      req.params['oauth_token'] = session[:token]
      req.params['v'] = '20160201'
      req.params['venueId'] = params[:venue_id]
      req.params['text'] = params[:tip]
    end
 
    redirect_to tips_path
  end
```

This looks similar to other calls we've made, except we're doing `Faraday.post` instead of `Faraday.get`. We're passing in our values as `params` just like with a GET request (more on this in a minute), and we get a response.

In this case, we want to show that it worked, so we're going to redirect to `tips_path` and show the user their tips.

Read the [List Detail](https://developer.foursquare.com/docs/lists/lists) document and see if you can set up the `index` action of our `TipsController`.

```ruby
# tips_controller.rb
def index
  resp = Faraday.get("https://api.foursquare.com/v2/lists/self/tips") do |req|
    req.params['oauth_token'] = session[:token]
    req.params['v'] = '20160201'
  end
  @results = JSON.parse(resp.body)["response"]["list"]["listItems"]["items"]
end
# ...
```

Looks very similar to our `friends` action.

It seems a little crazy that we have to dig so deep (`["response"]["list"]["listItems"]["items"]`) just to get to the actual tips list, but that is how deep they are nested in the response. You'll need to pay careful attention to the documentation and the response to make sure you're grabbing what you need from the response body.

And let's round it out by displaying the tips in our `index.html.erb` template:

```html
# tips/index.html.erb
<h1>My Tips</h1>
<ul>
<% @results.each do |item| %>
  <li><strong>><%= item["venue"]["name"] %></strong><br>
    <%= item["tip"]["text"] %></li>
<% end %>
</ul>
```

Now we can load our Rails server, go to `/search`, search for coffee shops, add a tip to one we like, and see our tips!

####POST PARAMS VS POST BODY
The Foursquare API endpoints we've been using all have used *parameters* to pass data. For a GET request, those are querystring parameters. For a POST, they are `www-form-urlencoded` parameters - that is, they act as if we had created a `form` and clicked `submit`.

Other API providers, like Github, may require that POST data instead be JSON-formatted text in the request `body`. A request has a `body` just like a response does.

Fortunately, Faraday makes it easy to POST with JSON in the body of the request. Instead of doing:

```ruby
Faraday.post("https://url/to/api") do |req|
  req.params['my_param'] = my_value
end
```

You can do this:

```ruby
Faraday.post("https://url/to/api") do |req|
  req.body = "{ "my_param": my_value }"
end
```

All you have to do is assign the request `body` to a properly formatted JSON string!

In Postman, there's a section under the URL entry that has a place where you can enter a **Body** as well, so you can test APIs that take params or JSON in the body equally well.

Pay close attention to the documentation of the API provider to make sure you're setting your request data in the right place!
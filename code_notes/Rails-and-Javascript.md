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
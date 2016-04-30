#Layouts and Partials
##Layouts And Templates in Rails
####OBJECTIVES
After this lesson, you'll be able to...

1. Identify the default application layout.
2.Yield to view templates from a layout.
3. Specify a custom layout in ActionController on a controller level using the `layout` macro and on the action level using the `render :layout => "custom"` option.
4. Use `:layout => fals`e to shut off the layout and only render the view

####LIFE WITHOUT LAYOUTS
Imagine that you're tasked to build an online store app with Rails.

You would probably have a few different views in this app, for example:

1. A list of products
2. A detail view that shows more info for a selected product
3. A shopping cart

Across all these views you would want a consistent look. This consistent look perhaps contains something like a logo, navigation links, a search bar, and a footer at the bottom that contains some info about the shop.

You start building the list of products and you end up with an action template that looks something like this. It has a nav component, a list of products and a footer. It would be the worst online shop ever, but let's keep it simple for now.

```html
<!-- app/views/products/index.html.erb -->
 
<!DOCTYPE html>
<html>
<head>
  <title>Flatiron Store</title>
  <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track' => true %>
  <%= javascript_include_tag 'application', 'data-turbolinks-track' => true %>
  <%= csrf_meta_tags %>  
</head>
<body>
    <div class="navigation">
      <ul>
        <a href="/products">Products</a>
        <a href="/cart">Cart</a>
      </ul>
    </div>
 
    <h1>The Product List</h1>
 
    <ul>
        <% @products.each do |job| %>
          <li><%= link_to 'Show', job %></li>
    </ul>  
 
    <div class="footer">
      <p>This shop promises the lowest prices in widgets!</p>
    </div>    
</body>
</html>
```

Great, so you have a list of products now! Any shop should have that, shouldn't it? Next you decide to build the view that will show the product details when customers click on a link for that product.

You've got done all the work to sort out the structure with a nav and a footer. You open up app/views/products/index.html.erb, select all the code, and copy it on to your clipboard. Now you are ready to paste it into your new template and customize it to show product info.

```html
<!-- app/views/products/show.html.erb -->
 
<!DOCTYPE html>
<html>
<head>
  <title>Flatiron Store</title>
  <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track' => true %>
  <%= javascript_include_tag 'application', 'data-turbolinks-track' => true %>
  <%= csrf_meta_tags %>  
</head>
<body>
    <div class="navigation">
      <ul>
        <a href="/products">Products</a>
        <a href="/cart">Cart</a>
      </ul>
    </div>
 
    <h1><%= @product.name%></h1>
 
    <p>
      <strong>Name:</strong>
      <%= @product.name %>
    </p>
 
    <p>
      <strong>Category:</strong>
      <%= @product.category %>
    </p>
 
    <p>
      <strong>Price:</strong>
      <%= @product.price %>
    </p>
 
    <div class="footer">
      <p>This shop promises the lowest prices in widgets!</p>
    </div>    
</body>
</html>
```

You call over your colleagues, all excited that you've managed to get it working so quickly. The designer takes one look and says: "you know what, the navigation is all wrong. You need to make it use the orange class, and make it pop by using the pop class".

Oh no! You have to go and change it in two files! Imagine having tens, or even hundreds of templates like this. Having to go make the same change over and over again would be very time consuming and prone to errors. In other words, it'll be a total drag. Because we've violated the DRY principle, we have to make changes in more than one place. Layouts apply the same principle as methods to generating HTML code.

####LAYOUTS TO THE RESCUE
Luckily you don't need to copy content from one template file to the next, because layouts in Rails are enabled by default, and when you generate a new Rails app, it generates a layout for you.

To find the generated layout, go and have a look in your Rails app at the following path. When you render a template for an action without specifying a different layout to use, Rails will use the layout found at this location: `app/views/layouts/application.html.erb`.

When you first generate a Rails app, depending on your version of Rails, the automatically generated layout file will look similar to this. The `application.html.erb` file is a very good place to start adding common components like the navigation, search, and footer from the example above.

```html
<!-- app/views/layouts/application.html.erb -->
 
<!DOCTYPE html>
<html>
<head>
  <title>Flatiron Store</title>
  <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track' => true %>
  <%= javascript_include_tag 'application', 'data-turbolinks-track' => true %>
  <%= csrf_meta_tags %>
</head>
<body>
 
<%= yield %>
 
</body>
</html>
```

####YIELD
Let's say you code up a new layout from scratch, and you end up with something like this:

```html
<!-- app/views/layouts/application.html.erb -->
 
<!DOCTYPE html>
<html>
<head>
  <title>Flatiron Store</title>
</head>
<body>
    <h1>Welcome To The Flatiron Store!</h1>
</body>
</html>
```

**Note**: usually you should include links to assets like style sheets and JavaScript files in your layouts, which was omitted in the code above to keep it simpler.

Other than the missing links to common assets, this layout is missing something **terribly important**. To see what it is, have a look at the following example that uses this layout.

This is an example of an action that we expect to result in our action's template rendered within the layout defined above.

```ruby
#/app/controllers/static_controller.rb
 
class StaticController < ApplicationController
  def about
  end
end
```

There should also be an associated route in the `/config/routes.rb` file to route a request to `/about` to the about action in the static controller above.

```ruby
#/config/routes.rb
 
Rails.application.routes.draw do
  get 'about', to: 'static#about'
end
```

And this is the **template** for the about action, with a simple message in it, which we would want to display in our layout from above.

```html
<!-- /app/views/static/about.html.erb -->
 
<p>Hello!</p>
```

When you load up the `static#about` route in your browser, you will be greeted by a very bold message saying: **Welcome To The Flatiron Store!**, but you won't see the **Hello!** from the about action's template at `/app/views/static/about.html.erb`.

This is happening because the layout file at `app/views/layouts/application.html.erb` does not have a `yield` statement in it.

`yield` is what Rails uses to decide where in the layout to render the content for the action. If you don't put `yield` in your layout, only your layout will render for each action, instead of your layout with the contents of the action template in the correct place in the layout.

To fix this issue, just change the layout file at `app/views/layouts/application.html.erb` to include `yield`:

```html
<!-- app/views/layouts/application.html.erb -->
 
<!DOCTYPE html>
<html>
<head>
  <title>Flatiron Store</title>
</head>
<body>
    <h1>Welcome To The Flatiron Store!</h1>
    <%= yield %>    
</body>
</html>
```

Now when you hit up the `static#about` route in your browser, you will see **Welcome To The Flatiron Store!**, followed by **Hello!**. This means that when the layout rendered, it pulled the action specific template into the correct place, where we added the `yield` statement!

Those of you that remember Ruby blocks might be thinking that this looks very similar to how Ruby yields to blocks in a method call. The same principle is at work here, we're yielding to a chunk of code and don't know what it will do in advance. We always want to run the layout part of the method, and we let any other chunk of code that produces HTML (a template in this case) run at that point in the method call.

####HOW LAYOUTS AND TEMPLATES ARE STITCHED TOGETHER
At it's simplest level, this is what happens when a request is made to your rails application:

1. Rails finds the template for your action based on either convention, or any other options you passed to the render method in your controller action.

2. It finds the correct layout to use, in a very similar fashion as finding the action's template, through convention, or specific options that you provided.

3. Rails uses the action template that you provided to generate the content specific to the action (this template might be composed of partial views, which you'll learn about a bit later)

4. It then looks for the `yield` statement in the layout, and inserts the result of the action template in the layout at the correct spot

So this means that for every request handled by rails, at most one layout will be used, and only one action template. The action template can call out to other templates, called partials, to render itself. Partials are covered in upcoming lessons, so don't worry too much about it for now.

####HOW RAILS DECIDES WHICH LAYOUT TO USE
Think about the example from earlier in this lesson, the Flatiron store app. As mentioned before, it would make sense to have the same layout for the product list, product detail pages, and cart, because you would want some common elements in the same places between those different views.

But when you add administration functionality to this online store, in order to allow someone to add new products to the site, update prices, and perhaps draw reports, you probably want to use a different layout, which is quite easy to do with Rails.

####DECIDING ON A LAYOUT THROUGH CONVENTION
Rails uses a simple convention to find the correct layout for your request. If you have a controller called `ProductsController`, it'll see whether there is a layout for that controller at `layouts/products.html.erb`. Similarly, if you have a controller called `AdminController`, it'll look for a layout at `layouts/admin.html.erb`. If it can't find a layout specific to your controller, it'll use the default layout at `app/views/layouts/application.html.erb`.

With the exception of the admin section of a site, most applications use the default layout for everything, so there's no need to have a layout for each controller. You want to have a consistent look and feel throughout your site, and use a different layout only if the situation really warrants it.

####OVERRIDING CONVENTIONS
If you need to override the conventions explained above, you can easily do so. For example, if you have a controller called `ShoppingCartController`, and you want to use the layout at `layouts/products.html.erb`, you have two options:

1. If you want to use the products layout for every action, simply specify that you want to use the products layout using the `layout` method in your controller, passing it a string that it can use to find the layout:

```ruby
class ShoppingCartController < ApplicationController
  layout "products"
end
```

2. If you want to use the products only for a particular action, simply use the render method in the controller action, and specify the layout you want it to use like this:

```ruby
class ShoppingCartController < ApplicationController
  def list
    render :layout => "static"
  end
 
  # the rest  of the actions will use standard conventions
end
```

3. If you want to render your action template without a template, you can do the following:

```ruby
class ShoppingCartController < ApplicationController
  def list
    render :layout => false
  end
 
  # the rest  of the actions will use standard conventions
end
```

**Note**: It's pretty unusual to not render the layout in a standard action. However, once you start using AJAX (Javascript) it's quite common. Keep this in the back of your mind when you get to Javascript.

####RECAP
Use Layouts to provide a common look and feel between different views of your app. As much as possible use and adapt only the default layout, until you have a good reason to use a different layout for a different section or action.

##Simple Partials
####INTRODUCTION
As you know, while coding we are generally trying to not repeat our code. If we see a repeated chunk of code in different methods, we sometimes extract that chunk of code into its own method, which we can than reference in multiple places.

We can apply a similar tool to reduce repetition in html. Partials are view-level files, that only form one part of an html page (get it? part, partial??). By using a partial we can remove repeated pieces of html and add better organization to the code in our views.

Let's look at an example to see what this means.

####EXAMPLE
Before we get started, make sure that you run `rake db:seed` to seed the database. This will give us some posts and authors. Because we want to focus on partials, you'll notice some hard-coding in the controller. In the Posts controller create action, we've hard-coded that every new post created is linked to the very first author in the database.

Ok, let's dive in!

This is the code in the new form `app/views/posts/new.html.erb`

```html
<%= form_tag posts_path do %>
  <label>Post title:</label><br>
  <%= text_field_tag :title %><br>
 
  <label>Post Description</label><br>
  <%= text_area_tag :description %><br>
 
  <%= submit_tag "Submit Post" %>
<% end %>
```

And this is the code in the edit file `app/views/posts/edit.html.erb`

```html
<h3>Post Form</h3>
 
<%= form_tag post_path(@post), method: "put" do %>
  <label>Post title:</label><br>
  <%= text_field_tag :title %><br>
 
  <label>Post Description</label><br>
  <%= text_area_tag :description %><br>
 
  <%= submit_tag "Submit Post" %>
<% end %>
```

Except for the first line of the form, the code is pretty much the same! The labels and field tags are the same. All of that duplication is not good in code. Duplication means twice the amount of code to maintain, twice the opportunity for bugs, and two differing forms where our interface should be consistent.

Instead of duplicating all of that code, we just want to write that code once in a file that contains all of that code (our partial) and call it from both our edit and show files. Here's how:

First, let's create a new file in the `app/views/posts/` called `_form.html.erb`

To demarcate that this file is a partial, and only part of a larger view, we precede the filename with an underscore.

Second, let's remove the repeated code in `app/views/posts/edit.html.erb` so now the file should look like:

```html
<h3>Post Form</h3>
 
<%= form_tag post_path(@post), method: "put" do %>
<% end %>
```

Note that we left in the non-duplicated code. Now, let's also remove the duplicated code in the
`app/views/posts/new.html.erb` file. Now this file should look like:

```
<%= form_tag posts_path do %>
<% end %>
```

So once again, we left the code that is particular to the view, and we removed the code inside the `form_tag` block, as that code is duplicated.

So now what? It looks like we are missing a bunch of code in our 'posts/new' and 'posts/edit' files. Not to worry, that's where our partial comes in handy.

First, we'll place the duplicated code in a new file called `app/views/posts/_form.html.erb`. The file should look like the following:
`app/views/posts/_form.html.erb`

```html
  <label>Post title:</label><br>
  <%= text_field_tag :title %><br>
 
  <label>Post Description</label><br>
  <%= text_area_tag :description %><br>
 
  <%= submit_tag "Submit Post" %>
```

Now we need to render the code into the posts/edit and posts/new pages by placing `<%= render "form" %>` where we want the code in the partial to be rendered. Notice that while the file name of our partial starts with an underscore, when we reference our partial there is no underscore.

So now our posts/new file should look like this:
`app/views/posts/new.html.erb`

```html
<%= form_tag posts_path do %>
  <%= render 'form' %>
<% end %>
```

And our posts/edit file should look like this:
`app/views/posts/edit.html.erb`

```html
<h3>Post Form</h3>
 
<%= form_tag post_path(@post), method: "put" do %>
  <%= render 'form' %>
<% end %>
```

Finally, our partial, the posts/form file looks like the following:
`app/views/posts/_form.html.erb`

```html
<label>Post title:</label><br>
<%= text_field_tag :title %><br>
 
<label>Post Description</label><br>
<%= text_area_tag :description %><br>
 
<%= submit_tag "Submit Post" %>
```

Ok - all done!

Just a couple of things to note.

1. Notice that even though the last line of the form, the `<% end %>` tag, is duplicated code, we didn't move it into the partial. The reason for this is because it closes the beginning of the form_tag block, which DOES differ from form to form. So we didn't want to open our form_tag block in one file, and close in a different file. This is a stylistic point that you will get a feel for over time.

2. We could have named the partial whatever we wanted to. The only requirement is that it start with an underscore, and then reference that partial without the underscore. But just like method names, it's good to make the names of our partials as semantic as possible.

3. We were able to reference the partial by just calling `<%= render 'form' %>`. Notice that we didn't specify the folder that my partial lived in like `<%= render 'posts/form' %>`. The reason we didn't need this (while it would have worked if we did include it), is because both my `posts/new` and my `posts/edit` files are referencing a partial from the same folder they live in, the `app/views/posts` folder. When referencing a partial from a different folder, we must include the folder name as well (eg. `<%= render 'posts/form' %>` as opposed to just `<%= render 'form' %>`).

####RENDERING A PARTIAL FROM A DIFFERENT FOLDER
Let's take a look at our `authors/show.html.erb` file.

```html
<%= @author.name %>
<%= @author.hometown %>
```

And now look at the code in `posts/show.html.erb`

```html
<%= @post.author.name %>
<%= @post.author.hometown %>
 
<h1><%= @post.title %></h1>
<p><%= @post.description %></p>
```

See the repetition? In both places we are using the author object to call the name and hometown methods. The first thing we have to fix is the slight difference between the templates. Let's make the beginning portion of the posts show template match the authors show template.

`posts/show.html.erb`

```html
<%= @author.name %>
<%= @author.hometown %>
 
<h1><%= @post.title %></h1>
<p><%= @post.description %></p>
```

Then let's make a new partial called `app/views/authors/_author.html.erb` and place the repeated code in the file so that it looks like the following:

`app/views/authors/_author.html.erb`

```html
<%= @author.name %>
<%= @author.hometown %>
```

Now we can just render this partial in our authors/show page by doing the following:

`app/views/authors/show.html.erb`

```html
<%= render 'author' %>
```

We can make the same change in `app/views/posts/show.html.erb`

`app/views/posts/show.html.erb`

```html
<%= render 'author' %>
 
<h1><%= @post.title %></h1>
<p><%= @post.description %></p>
```

Uh oh. This won't work, because if we don't specify the folder name, rails will assume that the partial lives in the same folder as the view that is calling that partial. In this case, it looks for a file in the posts directory called `_author.html.erb` and doesn't find it. So we need to tell rails to go outside the folder, by being explicit about the folder and file name that it is rendering. We do that by changing the code to the following:

`app/views/posts/show.html.erb`

```html
<%= render 'authors/author' %>
 
<h1><%= @post.title %></h1>
<p><%= @post.description %></p>
```

We're almost there! One more problem is that our partial assumes there is an instance variable called `@author`. It needs it to work! We'll need to change the posts controller to have it set an instance variable called `@author`.

Change the `posts#show` action in the controller to look like the following:

`app/controllers/posts_controller.rb`

```ruby
  def show
    @post = Post.find(params[:id])
    @author = @post.author
  end
```

##Partial with Locals
####INTRODUCTION
Partials help us break our code up into reusable chunks. They also often have implicit dependencies that can lead to bugs. To make the implicit explicit, use locals whenever your partials depend on data to work.
In the following example, we'll unpack exactly what locals are and how they're used.

####LESSON
Take a look at the included repo. You should notice the same piece of view code in a few places.

```html
<ul>
  <li> <%= @author.name %></li>
  <li> <%= @author.hometown %></li>
</ul>
```

You'll find that code (or very similar code) in the following pages: `app/views/authors/show.html.erb`, `app/views/authors/index.html.erb`, `app/views/posts/show.html.erb`.

Looks like we've got some work to do. Let's start with the author show page.

Let's remove the code from our `app/views/authors/show.html.erb` page. Now our file should be empty:
`app/views/authors/show.html.erb`

```

```
 
We can move the removed code into a partial `app/views/authors/_author.html.erb` that now has the following code.
`app/views/authors/_author.html.erb`

```html
<ul>
  <li> <%= @author.name %></li>
  <li> <%= @author.hometown %></li>
</ul>
```

To keep our code in the show page rendering out the same content, we call the partial from the `app/views/authors/show.html.erb file`. Doing this, the `app/views/authors/show.html.erb` file now looks like the following.

```html
<%= render 'author' %>
```

Great! Now let's take a look at the `app/views/posts/show.html.erb` file. It currently looks like the following:

`app/views/posts/show.html.erb`

```html
Information About the Post
<ul>
  <li> <%= @author.name %></li>
  <li> <%= @author.hometown %></li>
</ul>
<%= @post.title %>
<%= @post.content %>
```

You can see that the first two lines are exactly the same as the code in our authors/author partial. Let's remove the repetition in our codebase by using that partial instead. By using the partial, our code will look like the following:

`app/views/posts/show.html.erb`

```html
Information About the Post
<%= render 'authors/author' %>
<%= @post.title %>
<%= @post.content %>
```

Note that because we are calling a partial from outside the current `app/views/posts` folder, we must specify the folder that our author partial is coming from by calling `render 'authors/author'`.
This code may look ok at first, but it poses some problems. When we render the partial authors/author, we are not being explicit about that partial's dependencies.

A dependency is data that the code requires in order to work (like passing a variable into a method). In this case, the dependency of the author partial is the instance variable `@author`. Without that instance variable, the code won't work. Unfortunately, that dependency is defined far away in the controller.

This is not so hot. Imagine that our team's designer tells us we no longer want to display author information with each post - just the post itself.

We can just delete the line `<%= render 'authors/author' %>` right? Unfortunately, we forgot to remove the `@author` instance variable in the controller as well, because the dependency was not explicit. This mistake likely would have been avoided if whenever we called the partial, we also specified the data that the code relied on by passing through local variables.

Let's see how local variables make our code more explicit.
This is what the entire show view looks like:
`app/views/posts/show.html.erb`

```html
Information About the Post
<%= render partial: "authors/author", locals: {post_author: @author} %>
<%= @post.title %>
<%= @post.content %>
```

Notice that rendering the authors/author partial without passing through local variables the second line of code looked like `<%= render 'authors/author' %>`. Now with passing through locals: `<%= render partial: "authors/author", locals: {post_author: @author} %>`.

Notice a few things. First, we are no longer passing the render method a string, now we're passing a hash. That hash has two key value pairs.

The first key value pair tells rails the name of the partial to render. The second key-value pair contains a key of locals which points to a hash of variables to pass into the partial. The key is the name of the variable and its value is the value you'd like it to have in the parial. For the values of your locals, you can use instance variables set in the controller.

When we use locals we need to make sure that the variables we refer to in our partial have the same names as the keys in our locals hash.

In our example, the partial `app/views/author/_author.html.erb` we need to change our code from:

```html
<ul>
  <li> <%= @author.name %></li>
  <li> <%= @author.hometown %></li>
</ul>
```

to
`app/views/author/_author.html.erb`

```html
<ul>
  <li> <%= post_author.name %></li>
  <li> <%= post_author.hometown %></li>
</ul>
```

In other words, the way we use locals with a partial is similar to how we pass arguments into a method. In the locals hash `{post_author: @author}`, the key to the hash, is the argument name, and the value of that argument is the corresponding value to the key. We can name the keys whatever we want (and would probably name it author in a real application), but we wanted to demonstrate that name of the key has no special powers.

Now notice that if we choose to delete the line `<%= render partial: "authors/author", locals: {post_author: @author} %>` from the posts/show view, we will also see that calling the partial required me to pass in data about the author, and that line in our controller may no longer be needed.

In fact, with locals, we can eliminate `@author = @post.author` line in the `posts#show` action in the controller completely, by instead only accessing that data in where we need it, in the partial.

Let's remove that line of code in our controller, and in the view pass through the author information by changing our code to the following:

`app/controllers/posts_controller`

```ruby
  ...
  def show
    @post = Post.find(params[:id])
  end
```
 
`app/views/posts/show.html.erb`

```html
Information About the Post
<%= render partial: "authors/author", locals: {post_author: @post.author} %>
<%= @post.title %>
<%= @post.content %>
```

`app/views/posts/index.html.erb`

```html
<%= @posts.each do |post| %>
<%= render partial: "authors/author", locals: {post_author: post.author} %>
  <%= post.title %>
  <%= post.content %>
<% end %>
```

`app/views/authors/index.html.erb`

```html
<% @authors.each do |author| %>
<%= render partial: "author", locals: {post_author: author} %>
<% end %>
```

`app/views/authors/show.html.erb`

```html
<%= render partial: "author", locals: {post_author: @author} %>
```

This code is much better. We are being more explicit about our dependencies, reducing lines of code in our codebase, and reducing the scope of the author variable.

Don't worry if you find the syntax for rendering a partial hard to remember - it is. You can always re-reference this guide or use the Rails Guides.

##Rendering Collections

####OVERVIEW
Up until now our only way to render collections was somewhat manually. We could iterate over an array and render the partial for each object in the array. Let's see how Rails can abstract this into a nicer syntax.

####RENDERING COLLECTIONS
Currently, our `posts#index` view is manually rendering the partial in a loop.

```html
<% @posts.each do |post| %>
  <%= render :partial => "post", {:locals => {:post => post}} %>
<% end %>
```

Rails offers a great way to render a collection using a partial by passing the collection option to the render method.

```html
<%= render :partial => "post", :collection => @posts %>
```

Our code is tighter and both more abstract and more clear.
Another even more abstract method Rails gives us to do this is passing an array directly to the render method.

```html
<%= render @posts %>
```

This approach is a bit more abstract. Under the hood Rails uses the convention that you will have a partial with the name of the models in the collection. Rails will even render a collection of heterogeneous models ([customer, order, customer]) calling the correct partial for each one.

####EMPTY COLLECTIONS
What happens if the collection you pass to your render call is empty? If you don't handle this exception the render method will return nil and nothing will appear on the screen. A useful trick is to use the `||` operator to print something to the screen to alert the user to this.

```html
<%= render(@posts) || "There are no blog posts!" %>
```

####CONCLUSION
As always, Rails has tried to abstract commonly used functionality into more terse and implicit code.

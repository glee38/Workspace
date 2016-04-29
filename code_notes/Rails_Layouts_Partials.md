#Layouts and Partials
##Layouts And Templates in Rails
####OBJECTIVES
After this lesson, you'll be able to...
Identify the default application layout.
Yield to view templates from a layout.
Specify a custom layout in ActionController on a controller level using the layout macro and on the action level using the render :layout => "custom" option.
Use :layout => false to shut off the layout and only render the view
LIFE WITHOUT LAYOUTS
Imagine that you're tasked to build an online store app with Rails.
You would probably have a few different views in this app, for example:
A list of products
A detail view that shows more info for a selected product
A shopping cart
Across all these views you would want a consistent look. This consistent look perhaps contains something like a logo, navigation links, a search bar, and a footer at the bottom that contains some info about the shop.
You start building the list of products and you end up with an action template that looks something like this. It has a nav component, a list of products and a footer. It would be the worst online shop ever, but let's keep it simple for now.
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
Great, so you have a list of products now! Any shop should have that, shouldn't it? Next you decide to build the view that will show the product details when customers click on a link for that product.
You've got done all the work to sort out the structure with a nav and a footer. You open up app/views/products/index.html.erb, select all the code, and copy it on to your clipboard. Now you are ready to paste it into your new template and customize it to show product info.
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
You call over your colleagues, all excited that you've managed to get it working so quickly. The designer takes one look and says: "you know what, the navigation is all wrong. You need to make it use the orange class, and make it pop by using the pop class".
Oh no! You have to go and change it in two files! Imagine having tens, or even hundreds of templates like this. Having to go make the same change over and over again would be very time consuming and prone to errors. In other words, it'll be a total drag. Because we've violated the DRY principle, we have to make changes in more than one place. Layouts apply the same principle as methods to generating HTML code.
LAYOUTS TO THE RESCUE
Luckily you don't need to copy content from one template file to the next, because layouts in Rails are enabled by default, and when you generate a new Rails app, it generates a layout for you.
To find the generated layout, go and have a look in your Rails app at the following path. When you render a template for an action without specifying a different layout to use, Rails will use the layout found at this location: app/views/layouts/application.html.erb.
When you first generate a Rails app, depending on your version of Rails, the automatically generated layout file will look similar to this. The application.html.erb file is a very good place to start adding common components like the navigation, search, and footer from the example above.
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
YIELD
Let's say you code up a new layout from scratch, and you end up with something like this:
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
Note: usually you should include links to assets like style sheets and JavaScript files in your layouts, which was omitted in the code above to keep it simpler.
Other than the missing links to common assets, this layout is missing something terribly important. To see what it is, have a look at the following example that uses this layout.
This is an example of an action that we expect to result in our action's template rendered within the layout defined above.
#/app/controllers/static_controller.rb
 
class StaticController < ApplicationController
  def about
  end
end
There should also be an associated route in the /config/routes.rb file to route a request to /about to the about action in the static controller above.
#/config/routes.rb
 
Rails.application.routes.draw do
  get 'about', to: 'static#about'
end
And this is the template for the about action, with a simple message in it, which we would want to display in our layout from above.
<!-- /app/views/static/about.html.erb -->
 
<p>Hello!</p>
When you load up the static#about route in your browser, you will be greeted by a very bold message saying: Welcome To The Flatiron Store!, but you won't see the Hello! from the about action's template at /app/views/static/about.html.erb.
This is happening because the layout file at app/views/layouts/application.html.erb does not have a yield statement in it.
yield is what Rails uses to decide where in the layout to render the content for the action. If you don't put yield in your layout, only your layout will render for each action, instead of your layout with the contents of the action template in the correct place in the layout.
To fix this issue, just change the layout file at app/views/layouts/application.html.erb to include yield:
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
Now when you hit up the static#about route in your browser, you will see Welcome To The Flatiron Store!, followed by Hello!. This means that when the layout rendered, it pulled the action specific template into the correct place, where we added the yield statement!
Those of you that remember Ruby blocks might be thinking that this looks very similar to how Ruby yields to blocks in a method call. The same principle is at work here, we're yielding to a chunk of code and don't know what it will do in advance. We always want to run the layout part of the method, and we let any other chunk of code that produces HTML (a template in this case) run at that point in the method call.
HOW LAYOUTS AND TEMPLATES ARE STITCHED TOGETHER
At it's simplest level, this is what happens when a request is made to your rails application:
Rails finds the template for your action based on either convention, or any other options you passed to the render method in your controller action.
It finds the correct layout to use, in a very similar fashion as finding the action's template, through convention, or specific options that you provided.
Rails uses the action template that you provided to generate the content specific to the action (this template might be composed of partial views, which you'll learn about a bit later)
It then looks for the yield statement in the layout, and inserts the result of the action template in the layout at the correct spot
So this means that for every request handled by rails, at most one layout will be used, and only one action template. The action template can call out to other templates, called partials, to render itself. Partials are covered in upcoming lessons, so don't worry too much about it for now.
HOW RAILS DECIDES WHICH LAYOUT TO USE
Think about the example from earlier in this lesson, the Flatiron store app. As mentioned before, it would make sense to have the same layout for the product list, product detail pages, and cart, because you would want some common elements in the same places between those different views.
But when you add administration functionality to this online store, in order to allow someone to add new products to the site, update prices, and perhaps draw reports, you probably want to use a different layout, which is quite easy to do with Rails.
DECIDING ON A LAYOUT THROUGH CONVENTION
Rails uses a simple convention to find the correct layout for your request. If you have a controller called ProductsController, it'll see whether there is a layout for that controller at layouts/products.html.erb. Similarly, if you have a controller called AdminController, it'll look for a layout at layouts/admin.html.erb. If it can't find a layout specific to your controller, it'll use the default layout at app/views/layouts/application.html.erb.
With the exception of the admin section of a site, most applications use the default layout for everything, so there's no need to have a layout for each controller. You want to have a consistent look and feel throughout your site, and use a different layout only if the situation really warrants it.
OVERRIDING CONVENTIONS
If you need to override the conventions explained above, you can easily do so. For example, if you have a controller called ShoppingCartController, and you want to use the layout at layouts/products.html.erb, you have two options:
If you want to use the products layout for every action, simply specify that you want to use the products layout using the layout method in your controller, passing it a string that it can use to find the layout:
class ShoppingCartController < ApplicationController
  layout "products"
end
If you want to use the products only for a particular action, simply use the render method in the controller action, and specify the layout you want it to use like this:
class ShoppingCartController < ApplicationController
  def list
    render :layout => "static"
  end
 
  # the rest  of the actions will use standard conventions
end
If you want to render your action template without a template, you can do the following:
class ShoppingCartController < ApplicationController
  def list
    render :layout => false
  end
 
  # the rest  of the actions will use standard conventions
end
Note: It's pretty unusual to not render the layout in a standard action. However, once you start using AJAX (Javascript) it's quite common. Keep this in the back of your mind when you get to Javascript.
RECAP
Use Layouts to provide a common look and feel between different views of your app. As much as possible use and adapt only the default layout, until you have a good reason to use a different layout for a different section or action.
Next, we'll take what we've learned here and see it working in a lab.
##Refresher On MVC
####OBJECTIVES
1. Understand the value of placing logic in expected places.
2. Know what presentation logic is and how it differs from business logic.

####SEPARATION OF CONCERNS
Separation of Concerns is a design principle that helps us keep code logically organized by ensuring that each part of an application concerns itself with a specific job.

Remember the restaurant analogy? In a restaurant, there is a separation of concerns among the staff - A *server* takes your order, a *cook* prepares your order, a *food runner* brings it to your table, and a *busser* cleans up after you - and all of them work together to provide the cohesive experience of eating out.

Sure, the cook could do everything. In a small restaurant with only a few customers, it might be manageable for the same person who takes your order to be the one that cooks it. But, if that restaurant is bigger than say, a food truck, there's a good chance that things would get out of control and the cook would never know what she or he is supposed to do at any given time. Likewise, nobody else at the restaurant would know what the cook is supposed to be doing, and everyone would go looking at the cook for everything, which seems like kind of a nightmare!

####MODEL-VIEW-CONTROLLER REVIEW
In Rails, the Model-View-Controller paradigm helps us separate our concerns and know where to put certain kinds of code.

We could put all of our code in one file, but then the file is in the same spot as that overworked cook - doing so much that nobody knows what it's supposed to be doing. In a tiny application with only one developer, this might work for a while, but as the application and the team grow, everyone needs to know where to go to find certain kinds of code.

With that in mind, let's look again at the components of the MVC pattern:

+ **Models**: Provide the *business logic* of the application and access and store data. Models do most of the 'heavy lifting' of manipulating data and enforcing the 'rules' of the application.

+ **Views**: Provide the *presentation logic* of the application and allow for user interaction. Views only care about showing a user formatted data and giving them ways to interact with it.

+ **Controllers**: Provide communications between the models and the views and help *control* the flow of data.

Okay, models are responsible for *business logic* and views are responsible for *presentation logic*, but what does that mean?

**Business logic** is the code that deals with the data, and the "business" or "real-world" rules that govern an application. This is also sometimes called *domain* logic because it's all the stuff that's specific to the domain of the application. Sticking with the restaurant analogy, the business logic governs things like recipes and prices and how old you have to be to order a margarita.

**Presentation logic** is the code that deals with what the user sees. If business logic tells us what food the restaurant has and how much it costs, presentation logic puts all that together into a menu that looks great and helps a customer choose what to eat (and also entice them to get that margarita).

To put that into context with Rails MVC, picture a blog application. The view is only concerned with the presentation of blog posts. That's what it knows how to do, and that's all it knows how to do. The model is concerned with the content of all of the posts. The controller is concerned with which blog post you want to see. By keeping them separate, we get to do things like create a single view that can be used to display any post by having the controller ask for a specific one from a model.

By knowing which component is responsible for which concern, we can know where to go to find or add code based on what it does. If we need to write code to hit the database and search for specific blog posts, that seems like business logic and belongs in the model. If we need to write code to display those search results in a nice way and make sure the post date is formatted properly, that's presentation logic and belongs in the view.

####SEPARATING EVEN MORE CONCERNS
We know the view is responsible for presentation logic, but what happens when our application grows and some presentation logic needs to be shared across more than one view? That's where `helpers` come in, and we'll be tackling that in the next lesson.

##Refactoring Views With Helpers
####OBJECTIVES
1. Understand how and when to use helper methods
2. Create helper methods for specific controllers
3. Create application-wide helpers

####A STORY AS OLD AS STRFTIME
Let's imagine we're working on a blog application. In fact, we don't even have to imagine it, because there's a blog app included in the lesson. Use it to follow along and try out the code yourself.

We've already added the presentation logic to show a blog post:

```html
# views\posts\show.html.erb
 
<h1><%= @post.title %></h1>
<p><%= @post.description %></p>
```

However, our readers really want to know *when* the post was posted or edited. Fair enough.

We know our `Post` model has a timestamp field called `updated_at`, so we decide to display that.

```
# views\posts\show.html.erb
 
<h1><%= @post.title %></h1>
<p>Last updated: <%= @post.updated_at %></p>
<p><%= @post.description %></p>
```

We use `rails s` to run the application, browse to a post, and see the time, but it's not very pretty. We want a specific, readable format, so we look to our old friend `strftime`.

**Note**: reading about `strftime` in the [docs](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) is great, but playing with it live is even more fun, so check out [For a Good Strftime](http://www.foragoodstrftime.com/).

Okay, armed with `strftime` and having read the ancient runes that help us understand the different format directives (e.g. `%A`, `%p`, etc.), we end up with this:

```html
# views\posts\show.html.erb
 
<h1><%= @post.title %></h1>
<p><%= @post.updated_at.strftime("Last updated %A, %b %e, at %l:%M %p") %></p>
<p><%= @post.description %></p>
```

**Top-tip**: Notice how we included the text and punctuation in the argument to `strftime`. It will very nicely ignore anything that isn't one of the format directives and pass it through as part of the final string. This is helpful because it saves us from the awkward business of having to try to parse out the time string and concatenate parts of it together with other strings in order to create more readable output.

This is great! Now people know when a blog entry was last updated.

Except they only know it on the `show` template. What if they also need to see that date on the post list page, in line with each post title? We can add it to the `index` template. Simple enough, right?

```html
# views\posts\index.html.erb
<% @posts.each do |post| %>
  <div>
    <%= post.title %> - <%= post.updated_at.strftime("Last updated %A, %b %e, at %l:%M %p") %>
  </div>
<% end %>
```

Done and done. Now it's on the `show` and `index` views. But now we're thinking that it would be nice to see this when we are editing a blog post, just for our reference. Okay, we can do that. Let's add it to the `edit` template.

```html
# views\posts\edit.html.erb
 
<%= form_for(@post) do |f| %>
  <label><%= @post.updated_at.strftime("Last updated %A, %b %e, at %l:%M %p") %></label><br>
  <label>Post title:</label><br>
  <%= f.text_field :title %><br>
 
  <label>Post Description</label><br>
  <%= f.text_area :description %><br>
 
  <%= f.submit %>
<% end %>
```

At this point there may be little red flags shooting up in your head, but let's go one step further. A few weeks after making this change, we decide that the format should be in military (24-hour) time and that we don't want to display the day of the week anymore. It's our blog, we can do what we want.

But now we have a problem. We have to remember (or worse, search for) every place we wrote that code to display the last update, and then we have to make the same change to all of those places. We have a small application with only a few views right now, but as it grows, this kind of repitition is a productivity killer, not to mention a great way to introduce bugs into the system when we forget to make the changes in all of the places.

####DON'T REPEAT YOURSELF (DRY)
DRY is a software design principle that, like Separation of Concerns, helps us make decisions about how to organize our code. The formal, very dry statement of DRY is: "Every piece of knowledge must have a single, unambiguous, authoritative representation within a system."

Put more simply for our blog application: "We shouldn't have to look in three places to know how to display the last updated date and time of a post."

How do we DRY up our views for this last update feature? If we have a goal of manipulating the `updated_at` field in only a single place, where can we do that?

What about the model? We only ever get `updated_at` from the model, so it's tempting to do something like:

```ruby
# models\post.rb
 
def last_updated
  updated_at.strftime("Last updated %A, %b %e, at %l:%M %p")
end
```

and then use it like `@post.last_updated` on the view. That would certainly solve our DRY problem, but if we think back on MVC and Separation of Concerns, we've created a new problem. Model code isn't concerned with *presentation logic*. Displaying a formatted string isn't the job of the component that talks to the database and handles the business logic. It's the job of the view. So we need a way to keep that code in the view, but also make it DRY.

####HELPERS ARE HERE TO, WELL, HELP
Rails provides us with a great way to extract common presentation logic from multiple views: helpers! Helpers are methods that are available to your views that encapsulate a common bit of code. If you've used a `link_to` or a `text_field` then you've already come across helpers. Rails has a ton of them built-in to help keep you from having to repeat all the code necessary to build a link to a given route or display an image or create a form.

We can also create our own helpers to solve our own problems.

For our post's last-updated label, we want to create a method that does the `strftime` bit so that we can use it in our three views. But where do we put it?

Helpers are generally organized by controller. If you use `rails g scaffold` or `rails g controller`, Rails will create a helper for your controller and put it in the `app\helpers` directory. If we look in our helpers directory, we'll see `application_helper.rb`, `authors_helper.rb` and `posts_helper.rb`. This matches what we have in our `controllers` directory — `application_controller.rb`, `authors_controller.rb` and `posts_controller.rb`.

Let's use Separation of Concerns to decide where to make our helper. Displaying the date of the last time the post was updated isn't an application-wide concern. It's only a post concern. So let's put it in the posts helper.

```ruby
# helpers\posts_helper.rb
 
def last_updated(post)
  post.updated_at.strftime("Last updated %A, %b %e, at %l:%M %p")
end
```

Why are we passing `post` as an argument to the helper? If you look back at our three views, you'll see that sometimes we're dealing with an instance variable like `@post`, and sometimes we're dealing with a local variable like `post`. Our helper doesn't know anything that we don't directly tell it, so it can't assume a specific variable name. We will need to tell it what post we want to act on.

**Note**: Helpers can get a lot more advanced, encapsulating more logic and even HTML. In this case, we just want to get the value and not output any markup - we'll let the view template handle that part. It's not this helper's *concern*.

Now that we have our helper, let's DRY up those views.

```html
# views\posts\show.html.erb
 
<h1><%= @post.title %></h1>
<p><%= last_updated @post %></p>
<p><%= @post.description %></p>
```

```html
# views\posts\index.html.erb
<% @posts.each do |post| %>
  <div>
    <%= post.title %> - <%= last_updated post %>
  </div>
<% end %>
```

```html
# views\posts\edit.html.erb
 
<%= form_for(@post) do |f| %>
  <label><%= last_updated @post %></label><br>
  <label>Post title:</label><br>
  <%= f.text_field :title %><br>
 
  <label>Post Description</label><br>
  <%= f.text_area :description %><br>
 
  <%= f.submit %>
<% end %>
```

Not only have we encapsulated the formatting logic into a single method, but notice how much more readable our views are now.

####USING ANOTHER CONTROLLER'S HELPERS
Our blog also has an author page that lists the posts by that author. What do we do if we want to also show the last updated time on that page? The answer is: the same thing we just did!

```html
# views\authors\show.html.erb
 
<h1><%= @author.name %></h1>
<p>Posts:</p>
<% @author.posts.each do |post| %>
  <div><%= post.title %> - <%= last_updated post %></div>
<% end %>
```

Helpers are *organized* by controller, but they aren't *restricted* to a single controller. Just like you can use a Rails `link_to` helper in any view, you can also use any of your own helpers in any view. Our `last_updated` helper is still a `Post` concern, so we know to look for it in the `posts_helper`, but if an author page or any other page needs to use it, they can. Just like our models, views, and controllers, helpers are separated by concern to help us keep our codebase organized.

####APPLICATION HELPERS
What about things that arent the concern of a single controller and are applicable to the entire application? For these cases, we have the `application_helper`. This helper is created automatically with your Rails project and is where you keep helpers that are related to the application itself rather than any given model or controller.

In applications where users can log in, the application helper will often have a method to expose a `current_user`, something which gets used on almost every view in the system.

For our blog application, we decide we want to dynamically change the `<title>` of each page based on what the user is looking at. On an author's show page, it might be the author name. On a blog post page, it might be the post's title. Since we already know we're going to be repeating the same basic snippet of code across many views, we're going to jump right to creating a helper.

The page `title` isn't strictly the concern of a `Post`, nor is it the concern of an `Author`, even though it may use data from either of those things. Since it's a broader concern than just one controller/model, it's a prime candidate for the application helper.

```ruby
# helpers\application_helper.rb
 
def title(text)
  content_for :title, text
end
```

Note: We use the Rails `content_for` helper within our custom helper here. Helpers on helpers on helpers! What this will do is send our `text` to the place in our application layout that is expecting some content for the `:title`.

```html
# layouts\application.html.erb
<head>
  <title><%= yield :title %></title>
</head>
```

You can read more about `content_for` in the [Layouts and Rendering](http://guides.rubyonrails.org/layouts_and_rendering.html#using-the-content-for-method) RailsGuide.

Now that we have our `title` helper, we can use it everywhere to change the page title based on the content.

```html
# views\authors\show.html.erb
 
<% title @author.name %>
 
<h1><%= @author.name %></h1>
<p>Posts:</p>
<% @author.posts.each do |post| %>
  <div><%= post.title %> - <%= last_updated post %></div>
<% end %>
```

```html
# views\posts\show.html.erb
 
<% title @post.title %>
 
<h1><%= @post.title %></h1>
<p><%= last_updated @post %></p>
<p><%= @post.description %></p>
```

And just like that, we are using an application helper to dynamically set the page title on our post and author pages!

####SUMMARY
When we start repeating the same pieces of presentation logic across more than one view, we want to use the Don't Repeat Yourself (DRY) principle as our guide and extract that code into helpers. Helpers are organized by concern in the same way that models, views, and controllers are, so always make sure you're putting your helper code in the appropriate place. If it's code specific to a given model/controller, it belongs in that controller's helper. If it's code that's broadly applicable to the entire application, then it goes in the` application_helper`.

##Model Class Methods
####OBJECTIVES
1. Know when to use a model class method
2. Create model class methods for custom queries

####FILTERING POSTS BY AUTHOR
We have our list of blog posts, which is great, but our readers would like to be able to filter the list by author, so let's do what every great blog does and pander to the masses!

We want to start by adding some controls to our view to do the filtering:

```html
# views\posts\index.html.erb
 
<h1>Believe It Or Not I'm Blogging On Air</h1>
# add this new code above the @posts.each loop
<div>
  <h3>Filter posts:</h3>
  <%= form_tag("/posts", method: "get") do %>
    <%= select_tag "author", options_from_collection_for_select(Author.all, "id", "name"), include_blank: true %>
    <%= submit_tag "Filter" %>
  <% end %>
</div>
# end new code
 
<% @posts.each do |post| %>
  # ...
<% end %>
```

Now if we refresh `/posts`, we should see a select control with our authors in it, and a button. Pick one and hit "Filter"!

Okay, nothing interesting happened. Rails is magical, but not that magical. We have to take that action and write the code to do the filtering.

Since we're here, we'll just add it right into the view. At the top of our `index` view, let's do this:

```html
# views\posts\index.html.erb
 
# add this code here
<% if !params[:author].blank? %>
  <% @posts = Post.where(author: params[:author]) %>
<% end %>
# end new code
 
<h1>Believe It Or Not I'm Blogging On Air</h1>
# ...
```

And to ensure that our view has access to the controller's `params` hash, let's go into `posts_controller` and add this line near the top:

```ruby
# controllers\posts_controller.rb
 
class PostsController < ApplicationController
  helper_method :params
 
  def index
# ...
end
```

Note: You can use `helper_method` in a controller to expose, or make available, a controller method in your view, but as we'll talk about soon, this isn't always a great idea.

Okay. Let's reload our `/posts` page and try that filter again. It works! Now our readers can filter posts by author.

####FILTERING POSTS BY DATE
Job well done. But before we can even get another cup of coffee, we find out our readers want to be able to filter posts so they can see just the freshest hot takes.

Let's get back into our view and add the new filter to our form:

```html
# views\posts\index.html.erb
 
<%= form_tag("/posts", method: "get") do %>
  <%= select_tag "author", options_from_collection_for_select(Author.all, "id", "name"), include_blank: true %>
  # new code
  <%= select_tag "date", options_for_select(["Today", "Old News"]), include_blank: true %>
  <%= submit_tag "Filter" %>
<% end %>
```

And then let's activate that filter so the new code at the top of our view looks like this:

```html
# views\posts\index.html.erb
 
<% if !params[:author].blank? %>
  <% @posts = Post.where(author: params[:author]) %>
<% elsif !params[:date].blank? %>
  <% if params[:date] == "Today" %>
    <% @posts = Post.where("created_at >=?", Time.zone.today.beginning_of_day) %>
  <% else %>
    <% @posts = Post.where("created_at <?", Time.zone.today.beginning_of_day) %>
  <% end %>
<% end %>
<h1>Believe It Or Not I'm Blogging On Air</h1>
# ...
```

Reload `/posts` and try it out. If they choose an author, they can filter by author, or they can filter by date, or they can have it all. We've pleased everyone!

**Just Between Us**: We haven't pleased everyone because someone will inevitably want to now filter on the combination of an author and a date, and we could do that, but today, in this lesson, we're going to stand against the slings and arrows of [scope creep](https://en.wikipedia.org/wiki/Scope_creep)!

####REFACTORING OUT OF THE VIEW
Okay. We did it! We added our filters. But we understand that in MVC, we separate concerns, and put code in the right place, and looking at that view cluttered with so much business logic got us like:

![Nick Miller NO](http://i.giphy.com/D0psmHNJTFdiE.gif)

So it's time to do some refactoring.

We have some pretty big red flags here:

+ View directly querying the database for posts and authors!
+ View reading `params`, which we had to go out of our way to allow from the controller!
+ View overriding `@posts` which the controller is already creating, fully *doubling* our database requests!

Okay. Let's get to work. First, let's get into that view and kill that filter logic. Just straight up delete everything that comes before the `<h1>` so that it looks like this:

```html
# views\posts\index.html.erb
 
<h1>Believe It Or Not I'm Blogging On Air</h1>
<div>
  <h3>Filter posts:</h3>
  <%= form_tag("/posts", method: "get") do %>
    <%= select_tag "author", options_from_collection_for_select(@authors, "id", "name"), include_blank: true %>
    <%= select_tag "date", options_for_select(["Today", "Old News"]), include_blank: true %>
    <%= submit_tag "Filter" %>
  <% end %>
</div>
<% @posts.each do |post| %>
  <div>
    <h2><%= post.title %></h2>
    <h3>by: <%= link_to post.author.name, author_path(post.author) %></h3>
    <p><%= post.description %></p>
  </div>
<% end %>
```

Don't forget to also change the `select_tag` options from `Author.all` to `@authors`, because our view shouldn't be directly querying the database from that, either. A view's concern is presentation. The controller should give it all the data it needs.

Now let's get into our controller and repurpose our filter code so it will run there.

First, let's get rid of that `helper_method :params` line. We don't need it anymore. Reading `params` is a controller concern, so we don't need to expose it to the view.

Now let's dig into our `index` method and make some changes. First, we need to add this line to satisfy our author select control: `@authors = Author.all`. We're using the same code, just moving it to where it
belongs.

Then let's put in our filter code so that `index` looks like this:

```ruby
# controllers\posts_controller.rb
 
def index
  #provide a list of authors to the view for the filter control
  @authors = Author.all
 
  # filter the @posts list based on user input
  if !params[:author].blank?
    @posts = Post.where(author: params[:author])
  elsif !params[:date].blank?
    if params[:date] == "Today"
      @posts = Post.where("created_at >=?", Time.zone.today.beginning_of_day)
    else
      @posts = Post.where("created_at <?", Time.zone.today.beginning_of_day)
    end
  else
    # if not filters, show them all
    @posts = Post.all
  end
end
```

Great! Now let's reload the `/posts` page and make sure everything still works.

####REFACTORING DATABASE LOGIC OUT OF THE CONTROLLER
This is looking much better. Our view is back to only dealing with presentation logic, and our controller is providing the right data. But we're still not there yet.

If we think about separation of concerns, is the controller concerned with having deep knowledge of the database so that it can query it directly? No. All a controller wants to do is ask a model for what it needs in the simplest way possible.

So having something that looks like this:

```html
@posts = Post.where("created_at >=?", Time.zone.today.beginning_of_day)
```

isn't the best application of MVC and separation of concerns. We want the model to know things like `"created_at >=?", Time.zone.today.beginning_of_day` and the controller to just know to ask for something more like `from_today`.

So we need to move this into the model. Now the question becomes, is this a *class method* on the `Post` model itself, or is it an *instance method* on a specific `post`?

Well, since we are going to be asking for multiple `post` instances from the database, we won't have an instance to begin with, so we'll need to use a class method.

Class methods are commonly used on ActiveRecord models to encapsulate this type of custom query functionality, so let's do that now.

First, we want to add one for the author filter. Let's get into `post.rb` and do it.

```ruby
# models\post.rb
 
def self.by_author(author_id)
  where(author: author_id)
end
```

You'll notice that it's essentially the same code as we had in the controller, but it's now properly encapsulated in the model, so that a controller doesn't have to query the database, it just has to ask for posts `by_author`.

So let's do that. Get back in the `posts_controller` and change the code to use our new class method:

```ruby
if !params[:author].blank?
  @posts = Post.by_author(params[:author])
elsif !params[:date].blank?
# ...
```

**Top-tip**: There are always more ways to accomplish the same thing. You may be wondering why we didn't just `find` the author and return `author.posts`. That also would work. But when we think about constructing an application in a logical way, using principles like Separation of Concerns to guide us, we can conclude that the `posts_controller` is concerned with the `Post` model, so almost everything that controller does will probably flow through that model.

Now that we have that done, we can reload our `/posts` page and make sure it's all still working.

Okay, now let's give the same treatment to our date filter. We want to move the database code from the controller to the model, so let's add a couple of class methods to the `Post` model:

```ruby
# models\posts.rb
 
def self.from_today
  where("created_at >=?", Time.zone.today.beginning_of_day)
end
 
def self.old_news
  where("created_at <?", Time.zone.today.beginning_of_day)
end
```

And then let's update our controller to use them, so that our final `index` method should now look like this:

```ruby
def index
  #provide a list of authors to the view for the filter control
  @authors = Author.all
 
  # filter the @posts list based on user input
  if !params[:author].blank?
    @posts = Post.by_author(params[:author])
  elsif !params[:date].blank?
    if params[:date] == "Today"
      @posts = Post.from_today
    else
      @posts = Post.old_news
    end
  else
    # if not filters, show them all
    @posts = Post.all
  end
end
```

####SUMMARY
We've taken a look at how we could do some features in a few different ways, but by considering separation of concerns, sticking to MVC, and using class methods on our models, we can implement a search or filter feature in a clean, well-organized way.
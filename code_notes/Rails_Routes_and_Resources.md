#Routes and Resources
##Routing and Nested Resources
####OBJECTIVES
1. Understand the value of nested routes
2. Create nested routes
3. Understand how nested resource URL helpers are named

####LESSON
We're going to keep working on our blog application, augmenting it to filter posts by author in a more user-friendly and RESTful way.

####URL AS DATA
You've encountered REST already, but just to review, it stands for REpresentational State Transfer, and it encapsulates a way of structuring a URL so that access to specific resources is predictable and standardized.

In practice, that means if we type `rails s` and run our blog app, we know that to view the index of all `Post` objects, we can browse to `/posts`. And if we want to view a specific `Author`, we can guess the URL for that as long as we have the `id` by going to `/authors/:id`.

**Top-tip**: the `/:id` notation above represents a *dynamic* route segment, which we've touched on before and will be seeing more of in this reading.

Why do we care?

When we added the filter button to our blog, we could choose an author and filter and see that author's posts, but the URL looked something like this:

`http://localhost:3000/posts?utf8=%E2%9C%93&author=1&date=&commit=Filter`

That's the opposite of REST. That makes me *stressed*.

That URL tells us nothing, really, about the resources we're accessing. Not to mention it's uglier than a Geocities site (that's a real old reference), and we might want to present our readers with a link they could understand and share with friends.
Then there's the author, who might want a more presentable link to share that lets people know "hey, these posts belong to me!"

####DYNAMIC ROUTE SEGMENTS
What we'd love to end up with here is something like `/authors/1/posts` for all of an author's posts, and `/authors/1/posts/5` to see an individual post by that author.

We know we can build out a route with dynamic segments, so our first instinct might be to just define these in `routes.rb` like this:

```ruby
# routes.rb
  get 'authors/:id/posts'
  get 'authors/:id/posts/:post_id'
```

After adding those routes, let's load up our blog with `rails s` (don't forget to `rake db:seed`) and check it out by browsing to `/authors/1/posts`.

Oops. Error. Gotta tell those routes explicitly which controller actions will handle them. Okay, let's make it look more like this:

```ruby
# routes.rb
  # ...
  get 'authors/:id/posts', to: 'authors#posts_index'
  get 'authors/:id/posts/:post_id', to: 'authors#post'
```

And to handle our new filtering routes, we'll need to make some changes in our `authors_controller` to actually do the work.

```ruby
  def posts_index
    @author = Author.find(params[:id])
    @posts = @author.posts
    render template: 'posts/index'
  end
 
  def post
    @author = Author.find(params[:id])
    @post = @author.posts.find(params[:post_id])
    render template: 'posts/show'
  end
```

**Advanced**: While a controller action would normally implicitly render a template with the same name as the method, in this case we want to leverage the templates we're already using for posts, so we call `render` explicitly with a template path. Because we're telling `render` that we're using a `template`, we don't need to include the `.html.erb` extensions. Rails figures that out for us.

If we go back to our blog and try to browse to `/authors/1/posts` we should see the posts for that author. And then if we try `/authors/1/posts/1`, we should see that post.

**Note**: If your ids are different and you are having trouble with the URLs, try running `rake db:reset` to reset your ids to the defaults in the seed file.

We did it! We have much nicer URLs now. Are we done? Of course not.

If we look at our `routes.rb`, we can already see it getting messy. Instead of something nice like `resources :authors`, now we're specifying controller actions and HTTP verbs just to do a simple filter of an author's posts.

Beyond that, our DRY (Don't Repeat Yourself) and Separation of Concerns klaxons should be wailing because the code to find all posts, and posts by ids, is essentially repeated in the `authors_controller`. These aren't really the concerns of the `authors_controller`, and we can tell that because we're directly rendering `Post`-related templates.

Seems like Rails would have a way to bail us out of this mess.

####NESTED RESOURCE ROUTES
Turns out, Rails *does* give us a way to make this a lot nicer.

If we look again at our models, we see that an author `has_many :posts` and a post `belongs_to :author`. Since a post can logically be considered a *child* object to an author, it can also be considered a *nested resource* of an author for routing purposes.

Nested resources give us a way to document that parent/child relationship in our routes, and ultimately, our URLs.

Let's get back into `routes.rb` and delete the two routes we just added, and recreate them as nested resources. We should end up with something like this:

```ruby
# config\routes.rb
 
Rails.application.routes.draw do
 
  resources :authors, only: [:show] do
    # nested resource for posts
    resources :posts, only: [:show, :index]
  end
 
  resources :posts, only: [:index, :show, :new, :create, :edit, :update]
 
  root 'posts#index'
end
```

Now we have the resourced `:authors` route, but by adding the `do...end` we can pass it a block of its nested routes.
We can still do things on the nested resources as we do on a non-nested resource, like limit it to only certain actions. In this case, we only want to nest `:show` and `:index` under `:authors`.

Under that, we still have our regular resourced `:posts` routes, because we still want to let people see all posts, create and edit posts, and so on, outside of the context of an author.

Now we need to update our `posts_controller` to handle the nested resource we just set up. Notice how now we are dealing with the `posts_controller` rather than the `authors_controller`. Ultimately, the resource we're requesting is related to posts, so Separation of Concerns tells us to put that code in the `posts_controller`. And, since we already have actions to handle `:show` and `:index`, we won't be repeating ourselves like we did in the `authors_controller`.

Let's update `index` and `show` for the new routes:

```ruby
# controllers\posts_controller.rb
 
  def index
    if params[:author_id]
      @posts = Author.find(params[:author_id]).posts
    else
      @posts = Post.all
    end
  end
 
  def show
    if params[:author_id]
      @post = Author.find(params[:author_id]).posts.find(params[:id])
    else
      @post = Post.find(params[:id])
    end
  end
 
  def new
    # ...
```

We didn't have to make any new methods, or make any calls to render templates explicitly, just add a simple check for `params[:author_id]` to each method and go from there.

Where is `params[:author_id]` coming from? Rails provides it for us through the nested route so we don't have to worry about a collision with the `:id` parameter that `show` is looking for. It takes the parent resource name and appends `_id` so that there's always a predictable way to find the parent resource id.

For good measure, let's go into our `authors_controller.rb `and delete the two actions (`post` and `posts_index`) that we added above, so that it just looks like this:

```ruby
# controllers\authors_controller.rb
class AuthorsController < ApplicationController
 
  def show
    @author = Author.find(params[:id])
  end
 
end
```

**Top-tip**: Keep your application clean and easy to maintain by always removing unused code.

####NESTED ROUTE URL HELPERS
We've got our routes working and the `posts_controller` is handling its business, but how can we present this on the page so that someone knows how to find a link to an author's posts?

Just like any other resourced route, Rails provides named helpers for our nested routes as well. And, just like most other things Rails provides, there's a predictable way to figure out what they are.

If we want to get to the `/authors` page, we know the URL helpers are `authors_path` and `authors_url`. And if we want to get to a single author (`/authors/:id`), we can use `author_path(id)` or `author_path(@author)` in views. Similarly, we have `posts_path` for `/posts` and `post_path(id)` or `post_path(@post)` in views for `/posts/:id`.

So what if we want to get to all posts nested under an author?

We know the URL is `/authors/:author_id/posts`, so we can combine the two conventions and use `author_posts(author_id)`. Remember it's the singular `author` because we are getting one by `id`.

It stands to reason that a single post for an author would combine the conventions for single author path and single post path, leaving us with `author_post(author_id, post_id)`.

Once you become accustomed to breaking it down in that way, it's pretty straightforward to know what our URL helpers will be for a nested route. However, if you're not sure, or if you just want to double-check, you can use `rake routes` on the command line to get a printout of all your named routes, like this:

```
      Prefix Verb  URI Pattern                             Controller#Action
  test_index GET   /test/index(.:format)                   test#index
author_posts GET   /authors/:author_id/posts(.:format)     posts#index
 author_post GET   /authors/:author_id/posts/:id(.:format) posts#show
      author GET   /authors/:id(.:format)                  authors#show
       posts GET   /posts(.:format)                        posts#index
             POST  /posts(.:format)                        posts#create
    new_post GET   /posts/new(.:format)                    posts#new
   edit_post GET   /posts/:id/edit(.:format)               posts#edit
        post GET   /posts/:id(.:format)                    posts#show
             PATCH /posts/:id(.:format)                    posts#update
             PUT   /posts/:id(.:format)                    posts#update
        root GET   /                                       posts#index
```

You can also view this in your browser anytime you type in an incorrect route or if you visit `rails/info/routes`.

If you add `_path` or `_url` to any of the names under "Prefix", you'll have the helper for that route.

**Advanced**: Using `rake routes` can be a lot easier than browsing the `routes.rb` file once a project gets to a certain size, but the output might be overwhelming. Remember that you can `grep` the output of any command to search for what you want. So in the example above, if you just wanted to search for routes related to authors, you could type `rake routes | grep authors` to get a filtered list.

Let's make it easy for our readers to look at the posts for each of our authors using these helpers.

In `posts\index.html.erb`, we already show the author's name, so let's add a link to the list of the author's posts:

```html
# views\posts\index.html.erb
# ...
  <h2><%= post.title %></h2>
  # change the name to a link
  <h3>by: <%= link_to post.author.name, author_posts_path(post.author) %></h3>
  <p><%= post.description %></p>
# ...
```

Let's reload `/posts` and click on an author name. We should be taken to `/author/id/posts`.

Great! Now our URLs properly reflect the relationship of our resources, and read almost like an English sentence: `author/1/posts` = "author number one's posts".

Sometimes people get confused by path helpers and what they take as arguments. For example, why does `posts_path` not take an argument, but `post_path(@post)` does? Posts path refers to all of the posts, so we're not talking about a specific post (no specific id). When we're looking at the `post_path` we're referring to a specific post, and the rails helper needs to know WHICH post so that it can generate the proper url `posts/1` as opposed to `posts/2`.

####CAVEAT ON NESTING RESOURCES MORE THAN ONE LEVEL DEEP
You can nest resources more than one level deep, but that is generally a bad idea.

Imagine if we also had comments in this blog. This would be a perfectly fine use of nesting:

```ruby
resources :posts do
  resources :comments
end
```

We could then link to a post's comments with `post_comments_path`, or `/posts/1/comments`. That makes a lot of sense.

But if we tried to add to our already nested posts 

```ruby
resource, like this:
resources :authors do
  resources :posts do
    resources :comments
  end
end
```

Now we're getting into messy territory. Our comments path helper is now `author_post_comments_path` and our URL is `/authors/1/posts/1/comments`, and we have to handle for that filtering in our controller.

But if we lean on our old friend Separation of Concerns, we can conclude that a post's comments are not the concern of an author, and therefore, don't belong nested two levels deep under the `:authors` resource.

In addition, the reason to put the id of the resource in the URL is so that we have access to it in the controller. If we know we have the post with the id of one, we can use our Active Record relationships to call

```ruby
  @post = Post.find(params[:id])
  @post.author #=> This will tell us who the author of the post was! We don't #need this information in the URL
```

####SUMMARY
Nesting resources is a powerful tool that helps you keep your routes neat and tidy, and are a better tool than dynamic route segments for representing parent/child relationships in your system.

However, as a general rule, you should only nest resources one level deep and ensure that you are considering Separation of Concerns even in your routing.

##Modifying Nested Resources
####OBJECTIVES
1. Understand how to handle modifications (create/update) of nested resources.
2. Handle errors and validate data from nested resources.
3. Review the use of view helpers to keep views clean.

####LESSON
Continuing with our blog application, we're going to extend our nested resources to allow for creating and modifying blog posts by author.

####CREATING NEW POST FOR AN AUTHOR
The first thing we want to do is to create a new post that is automatically linked to an `Author`. We could set up a select box on the post page and make the author choose, but if we're already on the author's page, we know who the author is, so why not do it without forcing the user to choose?

We already used nested resources to view posts by author, so now let's look at nested resources to create posts by author. As usual, we want to start with the route. We want to add `:new` to our nested `:posts` resource:

```ruby
# config/routes.rb
 
  resources :authors, only: [:show, :index] do
    resources :posts, only: [:show, :index, :new]
  end
# ...
```

This gives us access to `/authors/:id/posts/new`, and a `new_author_post_path` helper.

**Top-tip**: Remember to run `rake routes` if you're unsure of the URL helper name.

We have the route, so now we need to update our `posts_controller#new` action to handle for the `:author_id` parameter.

```ruby
# controllers/posts_controller.rb
 
  def new
    @post = Post.new(author_id: params[:author_id])
  end
```

Notice that we're passing the `params[:author_id]` into `Post.new()`. We want to make sure that if we capture an `author_id` through a nested route that we keep track of it and assign the post to that author. We'll actually be carrying this `id` with us for the next few steps, babysitting it through the server request/response cycle.

Now let's get into our author `show` template and add a link to the nested new post page for that author.

```html
# authors/show.html.erb
<h1><%= @author.name %></h1>
 
<%= link_to "New Post", new_author_post_path(@author) %>
 
<p>Posts:</p>
<% @author.posts.each do |post| %>
  <div><%= post.title %></div>
<% end %>
```

Let's launch the app (don't forget to `rake db:seed`), browse to `/authors`, click an author, then click the new post link. Once there, let's make a post.

Something seems off. Where's our author? Looks like we didn't do a great job babysitting that `author_id`. We set it up in the `new` action, but it never made it to the view so that it could get submitted back to the server. Let's fix that. Open up the post form partial and add a hidden field for the `:author_id`.

```html
# posts/_form.html.erb
 
<%= form_for(@post) do |f| %>
  <label>Post title:</label><br>
 
  <%= f.hidden_field :author_id %>
 
  <%= f.text_field :title %><br>
  <label>Post Description</label><br>
  <%= f.text_area :description %><br>
  <%= f.submit %>
<% end %>
```

If we reload the new post page for the author and inspect the source, we should see something like this:

```html
<input type="hidden" value="1" name="post[author_id]" id="post_author_id">
```

Great. That part's working, but we need to carry that `author_id` with us even further.

Remember [Strong Parameters](http://guides.rubyonrails.org/action_controller_overview.html#strong-parameters)? We need to update our `posts_controller` to accept `:author_id` as a parameter for a post. So let's get in there and modify our `post_params` method.

```ruby
# controllers/posts_controller.rb
 
# ...
private
 
  def post_params
    params.require(:post).permit(:title, :description, :author_id)
  end
```

Now we know the `author_id` will be allowed for mass-assignment in the `create` action.

Let's try it out. Go to an author's new post page and make a post. We should see the author's name in the byline now!

Why didn't we have to make a nested resource route for `:create` as well as `:new`?

The `form_for(@post)` helper in `posts/_form.html.erb` will automatically route to `POST posts_controller#create` for a new `Post`. By carrying the `author_id` as we did and allowing it through strong parameters, the existing `create` route and action can be used without needing to do anything else.

####EDITING AN AUTHOR'S POSTS
We can use the same technique to allow us to directly edit an author's posts.

First, we allow the `:edit` action in the nested route:

```ruby
# config/routes.rb
 
  resources :authors, only: [:show, :index] do
    resources :posts, only: [:show, :index, :new, :edit]
  end
# ...
```

We don't have to change any views because `new` and `edit` both use the same `_form` partial that already has the `author_id`.

Now we need to update our post `show` view to give us the new nested link to edit the post for the author.

```html
# posts/show.html.erb
<h1><%= @post.title %></h1>
<p>by <%= link_to @post.author.name, author_path(@post.author) if @post.author %> (<%= link_to "Edit Post", edit_author_post_path(@post.author, @post) %>)</p>
<p><%= @post.description %> </p>
```

And if we try it out, everything should work just fine. Reload your author post, click the edit link, and edit the post.

Pretty easy, right? We didn't even have to change the controller this time. What's the catch?

####HANDLING MISCHIEF AND ERRORS IN OUR URLS
The catch is that we've opened ourselves up to a couple of potential bugs or, worse, opportunities for our more playful users to make a mess of our data. Let's work backward, starting with our recent changes to `edit`.

If you go back to your author post edit page, you'll see a URL something like `http://localhost:3000/authors/1/posts/1/edit`. This tells us that we are editing `Post` with `id: 1` for an `Author` with `id: 1`. But what if we change that `author_id` in the URL? Try browsing to `http://localhost:3000/authors/123456/posts/1/edit` and see what happens.

We end up on the same page! But post `1` belongs to author `1`, not author `123456`. In fact, there is *no* author `123456` in the system. How is this happening?

Remember how we didn't have to change the controller when we added the nested resource route for `:edit`? Well, this is the price we pay for taking shortcuts. What we should do is check to make sure that 1) the `author_id` is valid and 2) the post matches the author. So let's fix that now.

```ruby
# controllers/posts_controller.rb
 
  # ...
  def edit
    if params[:author_id]
      author = Author.find_by(id: params[:author_id])
      if author.nil?
        redirect_to authors_path, alert: "Author not found."
      else
        @post = author.posts.find_by(id: params[:id])
        redirect_to author_posts_path(author), alert: "Post not found." if @post.nil?
      end
    else
      @post = Post.find(params[:id])
    end
  end
```

Here we're looking for the existence of `params[:author_id]`, which we know would come from our nested route. If it's there, we want to make sure that we find a valid author. If we can't, we redirect them to the `authors_path` with a `flash[:alert]`.

If we do find the author, we next want to find the post by `params[:id]`, but instead of directly looking for `Post.find()`, we need to filter the query through our `author.posts` collection to make sure we find it in that author's posts. It may be a valid post `id`, but it might not belong to that author, which makes this an invalid request.

Now if we go back and try our invalid URL (`http://localhost:3000/authors/123456/posts/1/edit`), we should be redirected back to where we belong.

**Top-tip**: One of the downsides of RESTful URL schemes is that curious users can edit the URLs to try to explore the system further. This is how we discovered all the [hidden Netflix genres](http://mashable.com/2016/01/11/netflix-search-codes/#LM6QcfeksZqG). However, this could also lead to security holes in your system, allowing users to potentially mismatch id parameters and cause havoc in your database, so always guard against that by doing what we've done above.

While we're at it, we should fix up our `new` action to ensure that we're creating a new post for a valid author. Let's make it look like this:

```ruby
# controllers/posts_controller.rb
 
  def new
    if params[:author_id] && !Author.exists?(params[:author_id])
      redirect_to authors_path, alert: "Author not found."
    else
      @post = Post.new(author_id: params[:author_id])
    end
  end
```

Here we check for `params[:author_id]` and then check `Author.exists?` to see if the author is real.

Why aren't we doing a `find_by` and getting the author instance? Because we don't need a whole author instance for `Post.new`, we just need the `author_id`. And we don't need to check against the `posts` of the author, because we're just creating a new one. So we use `exists?` to quickly check the database in the most efficient way.

But what if `params[:author_id]` is `nil` in the example above? If we just did `Post.new` without setting `author_id`, it would be nil anyway. So we don't have to do anything special to handle it. It works for us if there is or isn't an `author_id` present.

Which brings us to the last thing we have to do.

####MISSING AUTHORS
When someone creates a new post via our nested route, we automatically assign an author and everything works great. But what about when they create a new post from the regular old `new_post_path`?

We could just eliminate that route and only allow post creation through the nested resource. That might be a valid choice in some applications.

But we've decided we want to be able to select an author at time of posting if we haven't used the nested route.

Since we're already set up to handle `author_id` on the controller, all we have to do is augment our `posts/_form.html.erb partial` to present a list of authors when none is present.

```html
# posts/_form.html.erb
 
<%= form_for(@post) do |f| %>
  <label>Post title:</label><br>
  <% if @post.author.nil? %>
    <%= f.select :author_id, options_from_collection_for_select(Author.all, :id, :name) %><br>
  <% end %>
  <%= f.hidden_field :author_id %>
#...
```

Okay, that gives us a select control if we don't have an author, but we have a problem. We can only have one `:author_id` field. We can put that `hidden_field` in an `else` and that will work, but now we have a whole bunch of logic cluttering up our view. So let's dump it in our `posts_helper` and clean up that form.

```ruby
# helpers/posts_helper.rb
 
module PostsHelper
  def author_id_field(post)
    if post.author.nil?
      select_tag "post[author_id]", options_from_collection_for_select(Author.all, :id, :name)
    else
      hidden_field_tag "post[author_id]", post.author_id
    end
  end
end
```

And back in our form partial:

```html
# posts_form.html.erb
 
<%= form_for(@post) do |f| %>
  <%= author_id_field(@post) %>
  <br>
  <label>Post title:</label><br>
  <%= f.text_field :title %><br>
# ...
```

Now we should have a selector when we browse to `/posts/new` and a hidden `author_id` field when we browse to `/authors/1/posts/new`.

####SUMMARY
We've seen how to create and edit nested resources, handle for errors or mischievous users in our parameters, and use helpers to extend our views to handle for nested and non-nested versions of the resource.

##Namespaced Routes
####OBJECTIVES
1. Understand the use of scope and namespace in routes.rb.
2. Create a module scoped controller.

####LESSON
We're going to explore different ways of routing things in our blog application to help us organize and group certain routes, and controllers, more logically.

####BLOG STATS
We decide that we want to keep track of some basic blog statistics, things like how many posts and authors we have. We start by creating a `stats_controller.rb` with an `index` action, and a corresponding view.

We can't actually browse to it yet, so we need to set up a route. Let's add it to `routes.rb`:

```ruby
# config\routes.rb
 
# ...
get '/stats', to: 'stats#index'
```

Easy enough, but after thinking about it, `/stats` isn't something we want to just hang off the root of our blog URL for anyone to see. It's really just for blog admins, and we want to set up a URL scheme to segregate admin things into their own logical space.

We modify our route:

```ruby
# config\routes.rb
 
# ...
get '/admin/stats', to: 'stats#index'
```

Now we can browse to `/admin/stats` for the stats page, and we can no longer go straight to `/stats`.

####SCOPING ROUTES
Over time, we might decide to add more admin functions, grouping them all together like we did above, until eventually our `routes.rb` looks something like this:

```ruby
# config\routes.rb
 
# ...
get '/admin/stats', to: 'stats#index'
get '/admin/authors/new', to: 'authors#new'
get '/admin/authors/delete', to: 'authors#delete'
get '/admin/authors/create', to: 'authors#create'
get '/admin/comments/moderate', to: 'comments#moderate'
```

As you can see, even with only a few more actions in our `admin` section, our routes are getting ugly. Not to mention we're repeating ourselves a lot by typing in `/admin/` on all these routes. Yes, even routes should be DRY!

What we need is a way to group all these under `/admin` without typing `/admin` all the time. That's where `scope` comes in.

In routing, `scope` allows us to prefix a block of routes under one grouping. So let's change our stats route:

```ruby
# config\routes.rb
 
# ...
scope '/admin' do
  resources :stats, only: [:index]
end
```

Now we reload `/admin/stats` and it still works. Notice our new route is resourced. Now that we don't have to manually prefix `/admin` we can go back using resourced routes under this scope.

If you run `rake routes`, you'll see that the new `/admin/stats` helpers are `stats_path` and `stats_url`.

####SCOPING WITH MODULES
Scoping works nicely to group our URLs together logically, but what happens when we have a bunch of controllers that are handling admin functions? As the application grows, it's going to be harder and harder to keep track of which controllers are for regular blog functions and which are for admin functions.

We want to group all our admin controllers logically to make it easier to maintain and add to the app, so let's add a `/admin` directory under `/controllers` where all the admin controllers will go:

`mkdir app/controllers/admin`

Now let's move our `stats_controller.rb` into the `/admin` folder.

When you create a new folder under `/controllers`, Rails will automatically pick that up as a `module`, and expect you to namespace the controller accordingly, so we need to modify our `admin/stats_controller.rb` to look like this:

```ruby
# controllers/admin/stats_controller.rb
 
class Admin::StatsController < ApplicationController
  def index
     # ...
  end
end
```

Now that we have our controller in a module, Rails will expect the views to match, so let's create `/app/views/admin/stats/` and move our `stats/index.html.erb` into it, so that we have an `/app/views/admin/stats/index.html.erb`.

**Top-tip**: The `views` folder for a controller module, in this case `/admin`, still expects a subfolder structure that matches the names of the controllers, so in this case, `/admin/stats`.

If we try to reload `/admin/stats` now, we will get an error, because we need to tell our routes about our new module.

```ruby
# routes.rb
 
  scope '/admin', module: 'admin' do
    resources :stats, only: [:index]
  end
```

We're telling `scope` that we want to use `/admin` as a URL prefix, and that all of the included routes will be handled by controllers in the `admin` module.

If we reload `/admin/stats`, everything should work just like it did, but now we are logically organizing our controllers.

####NAMESPACE
Right now, our route is scoped like this: `scope '/admin', module: 'admin'`, which is fine, but maybe a little less DRY than we'd like.

Fortunately, Rails gives us a shortcut here so that when we want to route with a module *and* use that module name as the URL prefix, we can use the `namespace` method instead of `scope`, `module`.

```ruby
# routes.rb
 
  namespace :admin do
    resources :stats, only: [:index]
  end
```

If we reload `/admin/stats`, everything still works, but we've simplified the declaration of the routes. The `namespace` method makes the assumption that the prefix and the module should match, saving us some typing.

**Top-tip**: There is one important difference between `scope '/admin', module: 'admin'` and `namespace :admin`, and it's in the URL helpers. Remember above that using `scope` gave us a `stats_path` helper. But now that we are using `namespace`, run `rake routes` again. You'll see that the helper is now prefixed with `admin_`, so `stats_path` becomes `admin_stats_path`. So if you switch from `scope` to `namespace`, take care to update any URL helpers you have in use!

####SUMMARY
We learned how to organize our URLs by using `scope` to group them with an `/admin` prefix, and to further organize our controllers using directories and modules and `scope module` or `namespace` in our routes.
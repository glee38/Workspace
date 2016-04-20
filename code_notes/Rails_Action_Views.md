##Rails URL Helpers

####PATHS VS ROUTE HELPERS
What's a real world difference between using hard coded paths compared with route helper methods? Let's imagine that you have a meeting in NYC and you want to get from one side of the city to the other. You have a couple different options:

1. Traverse the streets on foot
2. Taking an Uber or taxi

Walking is like hard coding your route's path. Technically it can work, however: it's slow, it can be error prone (one small mistake can lead to the wrong part of town), and if the meeting location changes it will require quite a bit of manual work to adjust and walk to the new destination.

Taking an Uber or taxi is like using a route helper, you can simply provide the address to the driver and let him navigate the city streets for you. It is faster than walking and if the address for the meeting changes while you're driving it's not difficult to adjust.

Don't worry if it's still a little fuzzy, here's an example of what it looks like in code:

+ **Hard coded path**: `"/posts/#{@post.id}"`
+ **Route helper**: `post_path(@post)`

So why would we want to use route helper methods as opposed to hard coding paths into the application? There are a number of reasons, below are a few of the key rationales:

+ Route helpers are more dynamic since they are methods and not simply strings, this means that if something changes with the route, there are many cases where the code itself doesn't even need to be changed

+ Route helper methods help clean up the view and controller code and assist with readability. On a side note, you cannot use these helper methods in your model files.

+ It's more natural to be able to pass arguments into a method as opposed to using string interpolation. For example, using `post_path(post, opt_in: true)` is more readable than using `"posts/<%= post.id %>?opt_in=true"`

+ Route helpers translate directly into HTML friendly paths, in other words if you have any weird characters in your urls the route helpers will convert them so they can be read properly by browsers. This includes items such as spaces or characters such as `&`, `%`, etc.

####IMPLEMENTING ROUTE HELPERS
To begin, we're going to start with an application that has the MVC setup for `posts`, with `index` and `show` actions currently in place. The route call looks like this:

```ruby
# config/routes.rb
resources :posts, only: [:index, :show]
```

We briefly discussed this `resources` method in the dynamic routing lesson, this will create routing methods for posts that we can utilize in our views and controllers. By running `rake routes` in the terminal it will give the following output:

```
  Prefix Verb URI Pattern          Controller#Action
   posts GET  /posts(.:format)     posts#index
    post GET  /posts/:id(.:format) posts#show
```

These four columns tell us everything that we'll need in order to use the route helper methods. The breakdown is below:

+ **Column 1** - This column gives the prefix for the route helper methods. In the current application `posts` and `post` are the prefixes for the methods that you can use throughout your applications. The two most popular method types are `_path` and `_url`. So if we want to render a relative link path to our posts' index page the method would be `posts_path` or `posts_url`. The difference between `_path` and `_url` is that `_path` gives the relative path and `_url` renders the full URL. If you open up the rails console in the sample app you can test these route helpers out. Run `app.posts_path` and see what the output is. You can also run `app.posts_url` and see how it prints out the full path instead of the relative path. **In general, it's best to use the `_path` version so if your server domain changes, nothing breaks**.

+ **Column 2** - This is the HTTP verb

+ **Column 3** - This column shows what the path for the route will be and what parameters need to be passed to the route. As you may notice the second row for the show route calls for an ID. When you pass the `resources` method the `:show` argument it will automatically create this route and assumes that you will need to pass the `id` into the URL string. Whenever you have `id` parameters listed in the path like this you will need to pass the route helper method an id, so an example of what our show route code would look like this: `post_path(@post)`. Notice how this is different than the `index` route of `posts_path`. Also you can ignore the `(.:format)` text for now. If you open up the rails console again you can call the route helpers. If you have a `Post` with an `id` of `3` you can run `app.post_path(3)` and see what the resulting output is. Running route helpers in the rails console is a great way of testing out routes to see what their exact output will be.

+ **Column 4** - This column shows the controller and action with the syntax of `controller#action.`

One of the other nice things about utilizing route helper methods is that they create predictable names for the methods. Once you get into day to day Rails development you will only need to run `rake routes` to find custom paths. Let's imagine that you takeover a legacy Rails application that was built with traditional routing conventions. If you see CRUD controllers for: newsletters, students, sales, offers, and coupons; you don't have to lookup the routes to know that you could call the index URLs for each resource below:

+ Newsletters - `newsletters_path`
+ Students - `students_path`
+ Sales - `sales_path`
+ Offers - `offers_path`
+ Coupons - `coupons_path`

####LINK_TO METHOD
All of our tests are currently passing, let's create a new Capybara spec. The scenario we will be building is to ensure that a link from the index page will point to that post's respective show page view template. The scenario is below:

```ruby
describe 'index page' do
  it 'links to post page' do
    second_post = Post.create(title: "My Title", description: "My post description")
    visit posts_path
    expect(page).to have_link(second_post.title, href: post_path(second_post))
  end
end
```

This matcher will fail since our index page doesn't currently link to the show page. To fix this let's update the index page like so:

```html
<% @posts.each do |post| %>
  <div><a href='<%= "/posts/#{post.id}" %>'><%= post.title %></a></div>
<% end %>
```

Wow, is this 2004? That is some ugly code, let's use a `link_to` method to clean this up and get rid of multiple `ERB` calls on the same line.

```html
<% @posts.each do |post| %>
  <div><%= link_to post.title, "/posts/#{post.id}" %></div>
<% end %>
```

This works and gets the tests passing, however it can be refactored. Instead of hardcoding the path and using string interpolation, let's use `post_path` and pass in the post argument.

```html
<% @posts.each do |post| %>
  <div><%= link_to post.title, post_path(post.id) %></div>
<% end %>
```

This is much better, but to be thorough, let's make one last refactor: Rails is smart enough to know that if you pass in the `post` object as an argument, it will automatically use the ID attribute, so we'll use this implementation code:

```html
<% @posts.each do |post| %>
  <div><%= link_to post.title, post_path(post) %></div>
<% end %>
```

If you run the tests now you'll see that they're all still passing.
We're using the `link_to` method to automatically create an HTML `a` tag. Now all of the tests are passing. If you open the browser and inspect the HTML element of the link you would see the following:

![Link To](https://s3.amazonaws.com/flatiron-bucket/readme-lessons/link_to.png)

As you will see, even though we never added HTML code for the link, such as: `<a href="..."></a>` the `link_to` method rendered the correct tag for us.

####USING THE :AS OPTION
If for any reason you don't like the naming structure for the methods or paths you can customize them quite easily. A common change is updating the path users go to in order to register for a site, out of the box the standard path would be:

```
/users/new
```

However we want something a little more readable like:

```
/register
```

In order to do this, let's update the `routes.rb` file and add the following
line:

```ruby
get '/register', to: 'users#new', as: 'register'
```

Now the application can let users navigate to `/register` to sign up and you, as the developer, can utilize your own custom `register_path` route helper throughout the application.

####SUMMARY

Now you will notice the beauty of using route helper methods. If you run our tests you will notice something interesting: all of the tests are still passing! If we had hardcoded the URLs in the links in our views we would have had a major issue: all of our links to the show pages would have broken, along with our Capybara tests. However, by using the built in helper methods the links all updated automatically.

##Rails form_tag
####RAILS FORMS
Welcome to the world of forms in Rails which will give users the ability to submit data into form fields. This can be used for: creating new database records, building a contact form, integrating a search engine field, and pretty much every other aspect of the application that requires user input. When it comes to forms in Rails you will discover that you will have the flexibility to utilize:

+ Built in form helper methods
+ Plain HTML form elements

This lesson is going to begin with integrating HTML form elements and then slowly start to refactor the form using Rails methods. It would be very easy to integrate form helpers (and we could have our form working in a few minutes), however, to fully understand what Rails is doing behind the scenes is more important than getting this form working. We're going to build the system from the ground up so when we're finished you should be able to understand all of the processes that are necessary in order to process forms in an application properly and securely.

####RENDERING THE FORM VIEW
Today we'll be giving the user the ability to create a new post in our BlogFlash application. Let's first create a Capybara spec to ensure that going to `posts/new` takes us to our form. If you remember back to the rails route helper lesson, we now know we don't need to hard code the route into our tests any longer. Let's use the standard RESTful convention of `new_post_path` for the route helper name:

```ruby
# specs/features/post_spec.rb
 
describe 'new post' do
  it 'ensures that the form route works with new action' do
    visit new_post_path
    expect(page.status_code).to eq(200)
  end
end
```

As expected this results in a failure saying that we don't have a `new_post_path` method, so let's create that in our route file:

```ruby
resources :posts, only: [:index, :new]
```

Now it gives the failure `The action 'new' could not be found for PostsController`. To correct this, let's add a `new` action in the `post` controller:

```ruby
def new
end
```

Lastly, it says we're missing a template. Let's add `app/views/posts/new.html.erb`. Now, all our tests are passing for our routing; let's add a matcher spec to make sure the form itself is being shown on this page:

```ruby
# specs/features/post_spec.rb
 
describe 'new post' do
  it 'has the form render with the new action' do
    visit new_post_path
    expect(page).to have_content("Post Form")
  end
end
```

Running this spec gets a matcher error. We can get this passing by adding the HTML `<h3>Post Form</h3>` to the `new.html.erb` view template.

####BUILDING THE FORM IN HTML
Our first pass at the form will be in plain HTML. In this reading we're not concerned with creating any records in the database, our focus is simply on the form process. We'll simply be printing out the submitted form params on the show page.

Let's create a spec for this. It's going to take a while for this to pass since we're going to be spending some time on the HTML creation process, but it's a good practice to ensure all new features are tested before the implementation code is added.

As you are updating the code, make sure to test it out in the browser – don't just rely on the tests. It's important to see the errors in both the tests and the browser since you will want to become familiar with both types of failure messages.

```ruby
# specs/features/post_spec.rb
 
it 'shows a new form that submits content and redirects to new page and prints out params' do
  visit new_post_path
 
  fill_in 'post_title', with: "My post title"
  fill_in 'post_description', with: "My post description"
 
  click_on "Submit Post"
 
  expect(page).to have_content("My post title")
end
```

This fails for obvious reasons, let's follow the TDD process and let the failures help build our form. The first error says that it can't find the field `post_title`. Let's add the following HTML form items into the view template:

```html
<h3>Post Form</h3>
 
<form>
  <label>Post title:</label><br>
  <input type="text" id="post_title" name="post_title"><br>
 
  <label>Post Description</label><br>
  <textarea id="post_description" name="post_description"></textarea><br>
 
  <input type="submit" value="Submit Post">
</form>
 
<%= params.inspect %>
```

In looking at both of the input elements, I'm using the standard Rails convention:

+ `id` - This will have the model name followed by an underscore and then the attribute name

+ `name` - This is where Rails looks for the parameters and stores it in a params Hash. In a traditional Rails application this will be nested inside of the model with the syntax `model[attribute]`, however we will work through that in a later lesson.

You'll also notice that I'm printing out the params to the page. This is because our Capybara tests will need to have the content rendered onto the page in order to pass. In a normal application, the page would redirect to a `show` or `index` page and this wouldn't be necessary.

Ok, so the spec was able to fill in the form elements and submit, but it's giving an error because this form doesn't actually redirect to any page. Let's first update the form so that it has an action:

```html
<form action="<%= posts_path %>">
```

This will now redirect to `/posts`, however we also need to add a method so that the application knows that we are submitting form data via the POST HTTP verb:

```html
<form action="<%= posts_path %>" method="post">
```

If you open up the browser and try this you will get an error since the `create` route doesn't exist yet.

Next, we need to draw a route so that the routing system knows what to do when a POST request is sent to the `/posts` resource:

```ruby
resources :posts, only: [:index, :new, :create]
```

If you run rake routes, you will see we now have a `posts#create` action:

```
Prefix    Verb    URI                   Controller#Action
posts     GET     /posts(.:format)      posts#index
          POST    /posts(.:format)      posts#create
new_post  GET     /posts/new(.:format)  posts#new
```

Now let's add in a `create` action in the posts' controller and have it grab the params, store them in an instance variable and then redirect to the new page (you can ignore how I'm passing the `@post` instance variable through the route, that is simply so the view can have access to the submitted form params):

```
def create
  @post = params
  redirect_to new_post_path(post: @post)
end
```

If you run the rails server and go to the `posts/new` page and fill in the title and description form elements and click submit you will find a new type of error:

![InvalidAuthenticityToken](https://s3.amazonaws.com/flatiron-bucket/readme-lessons/InvalidAuthenticityToken.png)

Which leads us to a very important part of Rails forms: CSRF.

####WHAT IS CSRF?
First and foremost, CSRF is an acronym for: Cross-Site Request Forgery (CSRF). Instead of giving a boring explanation of what happens during a CSRF request, let's walk through a real life example of a Cross-Site Request Forgery hack:

1. You go to your bank website and login; you check your balance and then open up a new tab in the browser and go to your favorite meme site.

2. Without you knowing, the meme site is actually a hacking site that has scripts running in the background as soon as you land on their page.

3. One of the scripts on the site hijacks the banking session that you have open in the other browser and submits a form request to transfer money to their account.

4. The banking form can't tell that the form request wasn't made by you, so it goes through the process as if you were the one who made the request.

This is a Cross-Site Request Forgery request; one site makes a request to another site via a form. Rails blocks this from happening by default by requiring that a unique authenticity token is submitted with each form. This authenticity token is stored in the session and can't be hijacked by hackers since it performs a match check when the form is submitted and will throw an error if the token isn't there or doesn't match.

To fix this `ActionController::InvalidAuthenticityToken` error we can integrate the `form_authenticity_token` helper into the form as a hidden field:

```html
<h3>Post Form</h3>
 
<form action="<%= posts_path %>" method="post">
  <label>Post title:</label><br>
  <input type="text" id="post_title" name="post[title]"><br>
 
  <label>Post Description</label><br>
  <textarea id="post_description" name="post[description]"></textarea><br>
 
  <input type="hidden" name="authenticity_token" value="<%= form_authenticity_token %>">
  <input type="submit" value="Submit Post">
</form>
```

If you refresh the page you will see that not only is the error fixed, but the elements are now also being printed out on the page! Running the specs you will see that our spec is now passing, so our form is working. However, this might be one of the ugliest Rails forms I've ever seen, so let's do some re-factoring.

####USING FORM HELPERS
The `ActionView` has a number of methods we can use to streamline our form. What's `ActionView`? `ActionView` is a sub-gem of Rails that has a number of helper methods that we can use in a Rails application that assist with streamling view template code. Let's start by integrating a Rails `form_tag` element:

```html
<%= form_tag posts_path do %>
  <label>Post title:</label><br>
  <input type="text" id="post_title" name="post[title]"><br>
 
  <label>Post Description</label><br>
  <textarea id="post_description" name="post[description]"></textarea><br>
 
  <%= hidden_field_tag :authenticity_token, form_authenticity_token %>
  <input type="submit" value="Submit Post">
<% end %>
```

Running the tests you will see that all of the tests are still passing. If you go and look at the HTML that this generates you will see the following:

```html
<form action="/posts" accept-charset="UTF-8" method="post"><input name="utf8" type="hidden" value="&#x2713;" /><input type="hidden" name="authenticity_token" value="7B5m/x8roxG6bc1vbRxRhr/z+ql6D261+j6LwHeGjeial7hxyW+5zk6CTGLpY1aKUj9hzLPdtRhWfcX5i047Iw==" />
  <label>Post title:</label><br>
  <input type="text" id="post_title" name="post[title]"><br>
 
  <label>Post Description</label><br>
  <textarea id="post_description" name="post[description]"></textarea><br>
 
  <input type="hidden" name="authenticity_token" id="authenticity_token" value="JC6cMiNK9k8nuWJdKp/3E29C/bALDkK9N6it3TeFEb1Sp0K89Q7skNNW41Cu4PAfgo5m1cLcmRCb6+Pky02ndg==" />
  <input type="submit" value="Submit Post">
</form>
```

The `form_tag` Rails helper is smart enough to know that we want to pass the form params using the POST method and it automatically renders the HTML that we were writing by hand before.

Now let's integrate some other form helpers to let Rails generate the input elements for us, for this form we'll be using the `text_field_tag` and `text_area_tag` tag and pass them the attributes with symbols. It's important to realize that form helpers aren't magic, they are simply Ruby methods that have arguments, which are the inputs and additional parameters related to the form elements. In addition to updating the form fields, we'll also replace the HTML tag for the submit button with the `submit_tag`. Lastly, we can remove the manual authenticity token call since that is generated automatically through the `form_tag` helper:

```html
<%= form_tag posts_path do %>
  <label>Post title:</label><br>
  <%= text_field_tag :title %><br>
 
  <label>Post Description</label><br>
  <%= text_area_tag :description %><br>
 
  <%= submit_tag "Submit Post" %>
<% end %>
```

So what HTML does this generate for us? Below is the raw HTML:

```html
<form action="/posts" accept-charset="UTF-8" method="post"><input name="utf8" type="hidden" value="&#x2713;" /><input type="hidden" name="authenticity_token" value="uQUOmh8edu8S/x5mVqp4aMpqEkHGbBYCdY6NVrvBut7PjNAUyVpsMOYQn2vS1X9kJ6aJJA++za/ZzcNvRwkMFQ==" />
  <label>Post title:</label><br>
  <input type="text" name="title" id="title" /><br>
 
  <label>Post Description</label><br>
  <textarea name="description" id="description"></textarea><br>
 
  <input type="submit" name="commit" value="Submit Post" />
</form>
```

Notice how the `name` and `id` elements are different from what we needed to use when we manually built out the form. By utilizing the form tag helpers, Rails streamlined the naming structure for the `name` and `id` values since we were able to simply provide a symbol of the attribute the input was associated with.
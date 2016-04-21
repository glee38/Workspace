##Create Action
So we have a form that can accept data and then print out the the values to the screen, which is great for learning how forms work in Rails. However, in a real life application we'll most likely want to add the data as a new record in the database. In the `CRUD` lifecycle, this is where the `C` comes in and we `create` a new record.

Before implementing this functionality, let's first open up a Rails console session and create a record manually:

```ruby
post = Post.new
post.title = "Title Goes Here"
post.description = "Desc goes here..."
post.save
```

This syntax will let you manually create a new `Post` record with a `title` and `description` attribute. After running the `save` method in the console you will see output similar to the following:

```
 (0.1ms)  begin transaction
SQL (0.3ms)  INSERT INTO "posts" ("title", "description", "created_at", "updated_at") VALUES (?, ?, ?, ?)  [["title", "Title Goes Here"], ["description", "Desc goes here..."], ["created_at", "2015-11-23 22:26:43.799742"], ["updated_at", "2015-11-23 22:26:43.799742"]]
 (1.2ms)  commit transaction
=> true
```
 
As you can see, the `save` method generates a SQL script that inserts a new record into the database, passing the `title` and `description` parameters into the statement and then returning the newly created `Post` object. At a high level, this is what the `create` method in our controller will be doing.

Open up the `posts_controller.rb` file. Let's do a few things to replicate the behavior we had in the console:

1. Create a new `Post` instance
2. Pass in the parameters from the form
3. Save the record

To build in this behavior, initially you can copy and paste the code that we ran in the console. The only key difference is that now we want to pull in the form data and have that populate the `title` and `description` attributes. You can access each of the form elements by using the hash syntax to grab the elements from the `params` hash that is submitted with the form. The new code in the `create` method should look something like this:

```ruby
def create
  post = Post.new
  post.title = params[:title]
  post.description = params[:description]
  post.save
end
```

If you go to `/posts/new` and fill out the form and submit it, you'll get the error shown below, but this is ok, Rails is simply complaining that we don't have a view template since by default it's trying to render a view template called `create.html.erb` that doesn't exist. Remember that Rails tries to map the controller action directly to a template, but in cases like `create` we wouldn't want to have a view template, it simply is communicating with the database.

![Missing Create Template Error](https://s3.amazonaws.com/flatiron-bucket/readme-lessons/template_error_create.png)

If you open up the console you will see that even though we ran into an error page the record was successfully created in the database, so the form and `create` action are working properly. How do you know if the record was successfully created? There are a couple of ways:

1. If you look at the record in the console by running `Post.last` it will show the record that was created and we can look at the `created_at` attribute to ensure the timestamp is current.

2. If you scroll up through the Rails server logs you can see that it prints out the SQL showing that the record was successfully created (example is below)

```
   (0.1ms)  begin transaction
  SQL (0.7ms)  INSERT INTO "posts" ("title", "description", "created_at", "updated_at") VALUES (?, ?, ?, ?)  [["title", "My Post"], ["description", "My desc"], ["created_at", "2015-12-26 18:00:31.393419"], ["updated_at", "2015-12-26 18:00:31.393419"]]
   (2.2ms)  commit transaction
```

To fix the error we simply need to redirect the user after they've filled out the form, let's do two refactors:

+ Update the code with a redirect that leverages a route helper method
+ Refactor the `post` variable to be an instance variable

The revised `create` method should look something like this:

```ruby
def create
  @post = Post.new
  @post.title = params[:title]
  @post.description = params[:description]
  @post.save
  redirect_to post_path(@post)
end
```

In this `create` action I'm following the standard convention of redirecting to the `show` page for the resource since it makes sense that you would want to see the completed record that was just created. With that being said, this page flow is completely up to you, and we could have had the `create` action redirect to the `index` action just as easily.

So everything is working and now users are able to create records in the database using the HTML form and automatically be redirected to a `show` page where they can see the data that they entered in. There are a number of refactors that we will implement in the future, such as `strong parameters`, `error handling`, and tasks such as that, but we'll leave for a future lesson.

##Edit/Update Action

####RAILS CONTROLLER CONVENTIONS
Now that you know how to implement a create action, the next logical step will be to integrate an edit/update action. As you may have noticed, there is a trend in Rails conventions where the logic for rendering a form is separate from the action that manages the database record alteration. For example:

+ The `new` action in the controller simply renders the `new` form

+ The `create` action is what actually handles the process of inserting the form data into the database

In like fashion, the `edit` and `update` actions have a similar convention:

+ The `edit` action will handle rendering the `edit` form

+ The `update` action will be the method that updates the database record itself

####RENDERING THE `EDIT` FORM
To start off, let's draw a `get` route for our edit form, since the form will need to know which record is being edited this will need to be a dynamic route that accepts an `:id` as a parameter so it can be accessed by the controller:

```ruby
get '/posts/:id/edit', to: 'posts#edit'
```

We still need to draw one additional route to handle the `update` action, this route will also need to be dynamic and accept an `:id` as a parameter so the `update` action will know what record is being altered. If you're curious on what HTTP verb should be selected, consider that we're sending data to the server, so we know it's not `GET`, and since we're not creating a new record it shouldn't be `POST`, so `PUT` should be the HTTP verb.

```ruby
put 'posts/:id', to: 'posts#update'
```

On a side note, as a shortcut you could also simply add the `edit` and `update` actions to the resources call in the routes file and that would accomplish the same goal that these two lines do.

If you run `rake routes` you will see we have two new routes:

```
Verb    URI Pattern                 Controller#Action
GET     /posts/:id/edit(.:format)    posts#edit
PUT     /posts/:id(.:format)         posts#update
```

With our routes in place, let's add in the controller actions:

```ruby
def edit
end
 
def update
end
```

And then create the edit view template `app/views/posts/edit.html.erb`

Let's just copy and paste the form code from the `new` form:

```html
<h3>Post Form</h3>
 
<%= form_tag posts_path do %>
  <label>Post title:</label><br>
  <%= text_field_tag :title %><br>
 
  <label>Post Description</label><br>
  <%= text_area_tag :description %><br>
 
  <%= hidden_field_tag :authenticity_token, form_authenticity_token %>
 
  <%= submit_tag "Submit Post" %>
<% end %>
```

If you open the browser and go to the `edit` page it will now display the form, but you may have noticed a pretty big flaw, it doesn't load the record's data into the form, there are a few tasks that we'll need to do in order to implement this behavior. First let's have our `edit` action store the `post` record in an instance variable:

```ruby
def edit
  @post = Post.find(params[:id])
end
```

Now that the `edit` view template will have access to the `@post` object, we need to refactor the form so that it auto fills the form fields with the data from the `post` record. This is done below:

```html
<% # app/views/posts/edit.html.erb %>
 
<h3>Post Form</h3>
 
<%= form_tag posts_path do %>
  <label>Post title:</label><br>
  <%= text_field_tag :title, @post.title %><br>
 
  <label>Post Description</label><br>
  <%= text_area_tag :description, @post.description %><br>
 
  <%= submit_tag "Submit Post" %>
<% end %>
```

This will now populate the form, but if you tried to submit the form you may notice that it's redirecting to the show page and not changing the values. Notice how the `post_path` route helper method is used for the `show`, `update`, `edit`, and `delete` method? How can we let the app know that we want to use the `update` method in this case? We need to first make a change to the form so that it knows what route the data should be passed through as well as what HTTP verb needs to be called, make the following change to the `form_tag` line:

```html
<%= form_tag post_path(@post), method: "put" do %>
```

By explicitly stating the route and method, the form will now be routed to the update action. Before we try to implement the update action, let's first make sure the data is being routed properly. Enter the following code inside of the update method:

```ruby
def update
  raise params.inspect
end
```

The `raise` method will cause the application to pause and print out the `params` on an error page. You could also see the `params` if you called `puts params.inspect`; using `puts` would simply require you to track down the data in the rails server log.

If you open up the browser and navigate to an edit page, such as: `localhost:3000/post/5/edit`, and change some elements in the form and submit it, it should take you to an error page that prints out the params from the form, such as the below image:

![Raised Exception for Update Action](https://s3.amazonaws.com/flatiron-bucket/readme-lessons/update_raised_exception.png)

As you can see, the parameters are being passed to the update action. With that in mind, let's implement the functionality needed inside of the `update` action so that it will take the form data and update the specified record. Let's write some pseudo code for what the update action should do:

+ Query the database for the `Post` record that matched the :id passed to the route
+ Store the query in an instance variable
+ Update the values passed from the form (the `update` method here is the update method supplied from `ActiveRecord`, not the `update` method we're creating). **The update method takes a hash of the attributes for the model as its argument, e.g. `Post.find(1).update(title: "I'm Changed", description: "And here too!")**
+ Save the changes in the database
+ Redirect the user to the `show` page so they can see the updated record

We'll take advantage of the `update` method so that we're not manually assigning each attribute:

```ruby
def update
  @post = Post.find(params[:id])
  @post.update(title: params[:title], description: params[:description])
  redirect_to post_path(@post)
end
```

Now if you go to the edit page and make changes to the `title` or `description` form elements you will see they are changed when the form is submitted, so the `edit` and `update` functions are working properly!

####EXTRA CREDIT
+ When only one form element is updated, such as the `title`, does the `description` also get updated?
+ How could we refactor this form code? You may notice that we have a form for the `new` and `edit` actions, is there a better way of doing this?

##form_for on Edit
If you know how to utilize the `form_tag` method for creating forms in Rails you may wonder why you need to learn a new form building process. Let's imagine that you've been tasked with creating the world's first pet hamster social network, and one of the requirements is that the hamster profile page needs to have about 100 different form fields that can be edited. If you are using the `form_tag` method, your application will be technically resubmitting all 100 fields each time you edit the data. Your form view templates will also have 100 calls to the `@hamster` instance variable and each of the hamster attributes. Thankfully `form_for` is here and will help clean up the form view template and provide some additional benefits that we'll explore in this lesson.

####RECAP OF `FORM_TAG`
To review, the `form_tag` helper method allows us to automatically generate HTML form code and integrate data to both auto fill the values as well as have the form submit data that the controller can use to either create or update a record in the database. It allows for you to pass in: the route for where the parameters for the form will be sent, the HTTP method that the form will utilize, and the attributes for each field.

####ISSUES WITH USING `FORM_TAG`
Before we get into the benefits and features of the `form_for` method, let's first discuss some of the key drawbacks to utilizing `form_tag`:

+ Our form has to manually be passed the route for where the form parameters will be submitted

+ The form has no knowledge of the form's goal; it doesn't know if the form is meant to create or update a record

+ You're forced to have duplicate code throughout the form; it's hard to adhere to DRY principles when utilizing the `form_tag`

####DIFFERENCE BETWEEN FORM_FOR AND FORM_TAG
The differences between `form_for` and `form_tag` are subtle, but important. Below is a basic breakdown of the differences. We'll start with talking about them at a high level perspective and then get into each one of the aspects on a practical/implementation basis:

+ The `form_for` method accepts the instance of the model as an argument. Using this argument, `form_for` is able to make a bunch of assumptions for you.

+ `form_for` yields an object of class `FormBuilder`

+ `form_for` automatically knows the standard route (it follows RESTful conventions) for the form data as opposed to having to manually declare it

+ `form_for` gives the option to dynamically change the `submit` button text (this comes in very handy when you're using a form partial and the `new` and `edit` pages will share the same form, but more on that in a later lesson)

A good rule of thumb for when to use one approach over the other is below:

+ Use `form_for` when your form is directly connected to a model. Extending our example from the introduction, this would be our Hamster's profile edit form that connects to the profile database table. This is the most common case when `form_for` is used

+ Use `form_tag` when you simply need an HTML form generated. Examples of this would be: a search form field or a contact form

####IMPLEMENTATION OF `FORM_FOR`
Let's take the `edit` form that utilized the `form_tag` that we built before for `posts` and refactor it to use `form_for`. As a refresher, here is the `form_tag` version:

```html
<% # app/views/posts/edit.html.erb %>
<h3>Post Form</h3>
 
<%= form_tag post_path(@post), method: "put" do %>
  <label>Post title:</label><br>
  <%= text_field_tag :title, @post.title %><br>
 
  <label>Post Description</label><br>
  <%= text_area_tag :description, @post.description %><br>
 
  <%= submit_tag "Submit Post" %>
<% end %>
```

Let's take this refactor one element at a time. Since we already have access to the `@post` instance variable we know that we can pass that to the `form_for` method. We also can remove the path argument and the `method` call since `form_for` will automatically set these for us. How does `form_for` know that we want to use `PUT` for the form method? It's smart enough to know that since it's dealing with a pre-existing record that you want to utilize `PUT` over `POST`.

```html
<%= form_for(@post) do |f| %>
```

The `|f|` is an iterator variable that we can use on the new form object that will allow us to dynamically assign form field elements to each of the `post` data attributes, along with auto filling the values for each field. We get this `ActionView` functionality because we're using the `form_for` method and that gives us access to the `FormBuilder` module in Rails [(Documentation)](http://api.rubyonrails.org/classes/ActionView/Helpers/FormBuilder.html). Inside of the form we can now refactor the fields:

```html
<label>Post title:</label><br>
<%= f.text_field :title %><br>
 
<label>Post Description</label><br>
<%= f.text_area :description %><br>
```

Isn't that much cleaner? Notice how we no longer have to pass in the values manually? By passing in the attribute as a symbol (e.g. `:title`) that will automatically tell the form field what model attribute to be associated with. It also is what auto fills the values for us. Now let's refactor the submit button, instead of `<%= submit_tag "Submit Post" %>` we can change it to:

```html
<%= f.submit %>
```

Lastly, `form_for` also automatically sets the `authenticity_token` value for us, so we can remove the `<%= hidden_field_tag :authenticity_token, form_authenticity_token %>` line completely. Our new form will look something like this:

```html
<h3>Post Form</h3>
 
<%= form_for(@post) do |f| %>
  <label>Post title:</label><br>
  <%= f.text_field :title %><br>
 
  <label>Post Description</label><br>
  <%= f.text_area :description %><br>
 
  <%= f.submit %>
<% end %>
```

Our refactor work isn't quite done. If you had previously created a `PUT` route like we did in the `form_tag` lesson, we'll need to change that to a `PATCH` method since that is the HTTP verb that `form_for` utilizes. We can make that change in the `config/routes.rb` file:

```
patch 'posts/:id', to: 'posts#update'
```

What's the difference between `PUT` and `PATCH`? The differences are pretty subtle. On a high level `PUT` has the ability to update the entire object, whereas `PATCH` simply updates the element that was changed. Many developers choose to utilize `PATCH` as much as possible because it requires less overhead, however, it is pretty rare when you will need to distinguish between the two verbs and they are used interchangeably quite often.

You can also put in an additional argument if you're using the `resources` method for `update` and this will all happen automatically.

Now if you start the Rails server and go to an edit page you'll see that the data is loaded into the form and everything appears to be working properly, however if you change the value of one of the form fields and click `Update post` you will see that the record isn't updated. So what's happening? When I run into behavior like this I'll usually look at the console logs to see if it tells me anything. Below is the screenshot of what I see after submitting the form:

![Unpermitted Parameters](https://s3.amazonaws.com/flatiron-bucket/readme-lessons/unpermitted_params.png)

Because `form_for` is bound directly with the `Post` model, we need to pass the model into update method in the controller. So let's change: `@post.update(title: params[:title], description: params[:description])` to:

```ruby
@post.update(params.require(:post))
```

So why do we need to `require` the `post`? If you look at the old form the params would look something like this:

```ruby
{
  "title": "My Title",
  "description": "My description"
}
```

With the new structure for the `form_for` the params look like this:

```ruby
{
  "post": {
            "title": "My Title",
            "description": "My description"
          }
}
```

Notice how the attributes are now encapsulated in the `"post"` object? That's the main reason we needed to add the `require` method.

Now if you go back to the `edit` page and submit the form, the record will be updated in the database sucessfully.

##Strong Params Basics
####WHAT ARE STRONG PARAMS?
To understand the goal of strong params, let's pretend that you run a pharmacy. What would happen if you let all prescription orders come through without checking for: valid prescriptions, driver licenses, etc.? (Spoiler alert: you'd probably end up in jail). It would be insane to run a pharmacy without verifying that orders were legitimate. In the same way Rails application (starting in Rails 4+) wanted to shore up some security vulnerabilities and now require that you whitelist the parameters that are permitted when you're sending form data to the database.

####SETUP
To prevent confusion, in previous lessons I manually turned off the strong parameter requirement. Let's discover first why strong params were created and then work with them.

####WHY STRONG PARAMS
In the rails app in this lesson there is our blog application with Strong Params *disabled*. Create a new Post by going to `/posts/new`. Once you have created that post, go ahead and edit it at `/posts/1/edit`. You'll notice there is no Description field! In this case, I don't want the user to be able to modify the description of a post once it's been created. This happens in all kinds of different cases. You wouldn't want a bank user to be able to edit their account number or balance, would you? But! `balance` is still a field on the account class. In this case, `description` is still an attribute for the post class. Let's see if a user could "hack" our form to be able to modify the `description`.

1. Right click and inspect the page
2. Find the input for title. it should look like this: `<input type="text" value="asdferwer" name="post[title]" id="post_title">`
3. Right click on choose "Edit as HTML"
4. Add in the following new Description field:

```html
<br>
<label>Description:</label>
<br>
<input type="text" value="malicious description" name="post[description]" id="post_description">
```

1. Click somewhere else and look! a wild description field appears.
2. Now type in some message into the new field.
3. Click submit and you'll notice that the description has been updated. What a nefarious hack!

That is the problem that strong params was created to fix. We want to make sure that when users submit a form we only let the field we want get by.

####CODE IMPLEMENTATION
Let's go and enable Strong Params. To do this, open up `config/application.rb` and delete the line that says: `config.action_controller.permit_all_parameters = true`. Now restart your rails server and navigate to `localhost:3000/posts/new`, once there fill out the form and click `submit`. You'll see we get the following `ForbiddenAttributesError`:

![ForbiddenAttributesError](https://s3.amazonaws.com/flatiron-bucket/readme-lessons/ForbiddenAttributesError.png)

What this means is that Rails needs to be told what parameters are allowed to be submitted through the form to the database. The default is to let *nothing* through.

The same error would occur if you were trying to update a record. So how do we fix this? Let's update the `create` and `update` methods to look like the code below:

```ruby
# app/controllers/posts_controller.rb
 
def create
  @post = Post.new(params.require(:post).permit(:title, :description))
  @post.save
  redirect_to post_path(@post)
end
 
def update
  @post = Post.find(params[:id])
  @post.update(params.require(:post).permit(:title))
  redirect_to post_path(@post)
end
```

If you go back to the web browser and click refresh you'll see everything is working for both the `create` and `update` actions. Running the Rspec tests reveals that our specs are now passing again as well. You'll notice that our `update` only has a `:title` in the `permit` method. This is because, given our forms, we only want the `title` to be submitable! If you go and do your nefarious hack again, it won't work. Thwarted!!

####PERMIT VS. REQUIRE
What is the deal with the `#permit` vs `#require`? The `#require` method is the most restrictive. It means that the `params` that gets passed in **must** contain a key called "post". If it's not included then it fails and the user gets an error. The `#permit` method is a bit looser. It means that the `params` hash **may** have whatever keys are in it. So in the `create` case, it may have the `:title` and `:description` keys. If it doesn't have one of those keys it's no problem, the hash just won't accept any other keys.

####DRYING UP STRONG PARAMS
The code we wrote above is great if you only have a `create` method in your controller. However, if you have a standard CRUD setup you will also need to implement the same code in your `update` action. In our example we had different code for `create` and `update`, but generally you have the same items. It's a standard Rails practice to remove code repetition, so let's abstract the strong parameter call into its own method in the controller:

Now, both our `create` and `update` methods in the `posts` controller can simply call `post_params`.

```ruby
# app/controllers/posts_controller.rb
 
def create
  @post = Post.new(post_params)
  @post.save
  redirect_to post_path(@post)
end
 
def update
  @post = Post.find(params[:id])
  @post.update(post_params)
  redirect_to post_path(@post)
end
 
private
 
  def post_params
    params.require(:post).permit(:title, :description)
  end
```

This is a very helpful method since if you duplicated the strong parameter call in both the `create` and `update` methods you would need to change both method arguments every time you change the database schema for the `posts` table... and that sounds like a bad way to live. However by creating this `post_params` method we can simply make one change and both methods will automatically be able to have the proper attributes whitelisted.

Test this out in the browser and you can see that you can now create and updated posts without any errors. And you will also notice that all of the Rspec tests are still passing.

##Rails Forms Overview

Imagine that you're on a roadtrip across the country (I'm already jealous), your starting point is Santa Barbara and your final destination is New York, NY. If you enter the addresses into Google Maps you'll be shown multiple routes, and each route has an associated duration.

How do you select which route to take? Some of the points that should be included in your decision are below:

+ Duration
+ Cities that you go through
+ Landmarks that you want to drive through

Forms in Rails are similar to your roadtrip. Rails supplies a few different options to choose from when creating forms. How do you know the right option to select? Just like your roadtrip you consider the strengths of each form option and see how well it aligns with your intended behavior and the application requirements.

In this lesson we will review:

+ Both of the main form implementations in Rails
+ Discuss when one type of form should be selected over the other
+ Walk through the different form options

####FORM_TAG
Attributes of the `form_tag` helper:

+ Most basic form helper that's available in Rails
+ Uses tag form elements to build out a form
+ Unlike the `form_for` helper, it does not use a form builder

Let's build out a form that lets users enter in their cat's name and their associated color:

```html
<%= form_tag("/cats") do %>
  <%= label_tag('cat[name]', "Name") %>
  <%= text_field_tag('cat[name]') %>
 
  <%= label_tag('cat[color]', "Color") %>
  <%= text_field_tag('cat[color]') %>
 
  <%= submit_tag "Create Cat" %>
<% end %>
```

This will build a form and auto generate the following HTML:

```html
<form accept-charset="UTF-8" action="/cats" method="POST">
  <label for="cat_name">Name</label>
  <input id="cat_name" name="cat[name]" type="text">
  <label for="cat_color">Color</label>
  <input id="cat_color" name="cat[color]" type="text">
  <input name="commit" type="submit" value="Create Cat">
</form>
```

####FORM_FOR
Attributes for the `form_for` form helper method:

+ More magical form helper in rails
+ `form_for` is a ruby method into which a ruby object is passed, this means that a form that utilizes `form_for` is directly connected with an `ActiveRecord` model
+ `form_for` yields a form builder object that lets you create form elements that correspond to attributes in the model

So what does this all mean? When you're using the `form_for` method, the object is passed as a `form_for` parameter, it creates corresponding inputs with each of the attributes. For example, if you have `form_for(@cat)`, the form field params would look like `cat[name]`, `cat[color]`, etc.

Let's refactor our cat form that we discussed in the previous section, with `form_for` it can be simplified to look like this:

```html
<%= form_for(@cat) do |f| %>
  <%= f.label :name %>
  <%= f.text_field :name %>
  <%= f.label :color %>
  <%= f.text_field :color %>
  <%= f.submit %>
<% end %>
```

The `form_for` above will auto generate the following HTML:

```html
<form accept-charset="UTF-8" action="/cats" method="post">
  <label for="cat_name">Name</label>
  <input id="cat_name" name="cat[name]" type="text" />
  <label for="cat_color">Color</label>
  <input id="cat_color" name="cat[color]" type="text" />
  <input name="commit" type="submit" value="Create" />
</form>
```

####DIFFERENCES BETWEEN `FORM_FOR` AND `FORM_TAG`
Getting back to our roadtrip example, in order to make an informed decision on what route to take we need to know everything possible about both options. In like manner, in order to make a good choice for which form element to use, it's vital to understand what the differences are for each of them, the differences are subtle but extremely important to understand:

+ `form_for` is essentially an advanced form helper that will yield an object (form builder) that you use to generate your form elements (text field, labels, submit button, etc)

+ `form_tag` is a lower-level form helper that simply generates a `form` element. To simply add fields to the `form_tag` block, you add form element tags, such as `text_field_tag`, `number_field_tag`, `submit_tag`, etc

+ `form_tag` makes no assumptions about what you're trying to do, and you're responsible for specifying exactly what the form is supposed to do (sending a `post` request, `patch` request, etc)

+ `form_for` handles the retrieval of values from your object model, and will also try to route the form to the appropriate action specified in the controller.

So when would you choose one over the other? Below are some real world examples:

+ `form_for` - this works well for forms that manage CRUD. Imagine that you have a blog posting application, a great fit for the `form_for` method would be the `Post` model. The reason is because the `Post` model would have the standard `ActiveRecord` setup, and therefore it's smart to take advantage of the functionality built into `form_for`.

+ `form_tag` - this works well for forms that are not directly connected with models. For example, let's say that our blog posting application has a search engine, the search form would be a great fit for using `form_tag`.

####OTHER FORM ELEMENTS
#####`COLLECTION_CHECK_BOXES`

+ `collection_check_boxes` assumes that there is a `many-to-many` relationship in place. Let's imagine that you have a book publishing application: `books` have many `authors`, `authors` have many `books`

+ `collection_check_boxes` typically are used with a form builder element in a `form_for`

Below is an example on how you can implement `collection_check_boxes`:

```html
<%= form_for(@book) do |f| %>
  <%= f.label :title %>
  <%= f.text_field :title %>
 
  <%= f.collection_check_boxes :author_ids, Author.all, :id, :name %>
 
  <%= f.submit %>
<% end %>
```

So what HTML does this generate for us?

```html
<form class="new_book" id="new_book" action="/books" accept-charset="UTF-8" method="post"><input name="utf8" type="hidden" value="&#x2713;" /><input type="hidden" name="authenticity_token" value="m/SHRwLJ+S90q4QP20l44ljhlpbjrkWK3g+BVBtUzOjtfVnJ1I3j8IBEBQJfNn/utS0N8yp8nidyTM9t55x6Iw==" />
 
  <label for="book_title">Title</label>
  <input type="text" name="book[title]" id="book_title" />
 
  <input type="checkbox" value="1" name="book[author_id][]" id="book_author_id_1" /><label for="book_author_id_1">Stephen King</label><input type="hidden" name="book[author_id][]" value="" />
 
  <input type="submit" name="commit" value="Create Book" />
</form>
```

This form has a `collection_check_boxes` field, and there are several parameters passed in:

+ The first parameter of the method is `:author_ids`: this is a collection of all of the `:author_id`s that will be passed in if the corresponding checkbox is checked

+ The second parameter is `Author.all`: this is collection of all of the possible check box options that will be available in the form

+ The third parameter is `:id`: this is the parameter of an author that will be passed into the `:author_ids` collection once a checkbox is checked

+ The final parameter is `:name`: this is the attribute of author that will be rendered on the page next to the checkbox

#####`COLLECTION_SELECT`

+ `collection_select` assumes that there is a one-to-many relationship in place. Imagine that you are creating a competitor for IMDB.com, you would have the model of a `movie` that has one `director`, and the `director` model would be setup to have many movies

Below is a sample implementation on how `collection_select` can be used in an application:

```html
<%= form_for(@movie) do |f| %>
  <%= f.label :name %>
  <%= f.text_field :name %>
 
  <%= f.collection_select :director_id, Director.all, :id, :name %>
 
  <%= f.submit %>
<% end %>
```

Here is the HTML that this code generates:

```html
<form class="new_movie" id="new_movie" action="/movies" accept-charset="UTF-8" method="post"><input name="utf8" type="hidden" value="&#x2713;" /><input type="hidden" name="authenticity_token" value="bTialQ7whOvgjtJVtBL1RUaJ4lF+avBNhZNmp8LciQIbsUQb2LSeNBRhU1gwbfJJq0V5NLe4K+Ap0CiePhQ/yQ==" />
  <label for="movie_name">Name</label>
  <input type="text" name="movie[name]" id="movie_name" />
 
  <select name="movie[director_id]" id="movie_director_id"><option value="1">ALFRED HITCHCOCK</option></select>
  <select name="movie[director_id]" id="movie_director_id"><option value="2">ORSON WELLES</option></select>
  <select name="movie[director_id]" id="movie_director_id"><option value="2">JOHN FORD</option></select>
 
  <input type="submit" name="commit" value="Create movie" />
</form>
```

This form has a `collection_select` field, and there are several parameters being passed in:

+ The first is the `:director_id`: this is the association of the movie object that you will be making once the form is submitted

+ The second is `Director.all`: this will provide a collection of all of the directors in the Directors table in the database for the drop-down element in the `collection_select` field

+ The third is `:id`: this is what `:director_id` will be set to once a director is selected from the list, and once the submit button is clicked, it will save that association to the directors-movies joins table

+ The final parameter is `:name`: this is the attribute that's shown in the dropdown list of directors (it would be confusing if only the ids of the directors were shown in the list, so we're specifying that it should specifically show the director's name here)

####RESOURCES IN ROUTES.RB

+ Probably the biggest difference between Sinatra and Rails

+ In Sinatra, in `app.rb` you had blocks that corresponded to each path (fuses the router and the controller together)

+ In Rails, you have this convention that separates the routes from the controllers

+ Via the `resources` parameter, Rails metaprograms several routes for you that correspond to specific actions (`get`, `post`, `patch`)

##Rails Generators
If you go down the list on all of the tasks necessary to build out CRUD functionality in an application, it's quite extensive. From creating database tables, configuring views, and drawing individual routes, the feature builds can be time consuming and bug prone. Wouldn't it be nice if there was a more efficient way of integrating standard features instead of having to build them manually each time?

A primary goal of the Rails team was to make it efficient to build core application functionality. The Rails system has a number of generators that will do some of the manual work for us.

As nice as it is to use the generators to save time, they also provide some additional extra benefits:

+ They can setup some basic specs for an application's test suite. They won't write our complex logic tests for us, but they will provide some basic examples.

+ They are setup to work the same way each time. This helps standardize your code and enables your development to be more efficient since you don't have to worry as much about bugs related to spelling, syntax errors, or anything else that can occur when writing code manually.

+ They follow Rails best practices, which includes utilizing RESTful naming patterns, removing duplicate code, using partials and a number of other best of breed design patterns (if you don't know what all of these are, don't worry, we will cover them shortly).

####ABUSING GENERATORS
So if generators are so amazing, why wouldn't we always want to use them all of the time? I'll answer your question with another question: a chainsaw is a wonderful tool, why wouldn't you use it for every job you have around the house? The answer to both questions is the same: great tools are only great tools if they are matched with the right task. In the same way that you should only use a chainsaw when you have a job that requires it, generators should only be used when they are needed.

Extending our example from above, why wouldn't you use a chainsaw to build a model airplane?

![Chainsaw Example](https://s3.amazonaws.com/flatiron-bucket/readme-lessons/chainsaw_example.jpg)

In the same way as our chainsaw example, certain generators create quite a bit of code, and if that code is not going to be used it will clutter the application code and cause confusion for future developers. A few years ago I took over as the lead developer for a large legacy Rails application. The previous developer had relied on generators, even when they shouldn't have been used, and the end result was that it took months to simply figure out what code was being used and what was 'garbage' code that simply came from the generators.

So when is the right time to use a generator? After we've gone over the actions of each of the generators I think the answer to this query will become readily apparent and we'll walk through some case studies to help understand when each type of generator is beneficial.

####RAILS GENERATE
Each of the generators are entered into the terminal and will follow the syntax:

```
rails generate <name of generator>
```

For efficiency sake, Rails created an alias method for `generate` and allows you to simply use `g`, so the CLI command above could be shortened to:

```
rails g <name of generator>
```

####DIFFERENT TYPES OF GENERATORS
Below are the main generators that Rails offers. We'll go through examples of each of them in their own section:

+ Migrations
+ Models
+ Controllers
+ Resources

####MIGRATION GENERATORS
Up until this point we have been creating our migrations by hand; this has been beneficial since it's important to understand how migrations work. However, Rails has a great set of migration generators with conventions that can help make managing the database schema very efficient.

Let's start using database migrations in our case study application and update the `posts` table. To add a new column called `published_status`, we can use the following command:

```
rails g migration add_published_status_to_posts published_status:string
```

In the terminal you will see it creates a migration file for us: `db/migrate/20151127174031_add_published_status_to_posts.rb`. Since migration file names need to be unique, the generator prepends a timestamp before the file name. In the case of the migration I just ran, it added `20151127174031`. You can break this timestamp down as follows: `year: 2015, month: 11, date: 27, and then the time itself`.

Ready to see something pretty cool? Open up the file it created, you can find it in the `db/migrate` directory, I've placed what it created for the example app below:

```ruby
class AddPublishedStatusToPosts < ActiveRecord::Migration
  def change
    add_column :posts, :published_status, :string
  end
end
```

Notice what the generator did? It automatically knew that we wanted to add a new column and built out the `add_column` method call. How did this happen? It turns out that the way that you name the migration file is very important. By prepending the `add_` text to the name it gave a signal to the migration generator that the purpose of this schema change will be to add a column(s) to the table. How did it know the table we wanted to add to? By appending the `_posts` text to the end of the migration name it tells Rails that the table we want to change is the `posts` table. Lastly, by adding the `published_status:string` text at the end of the command tells the generator that the new column name will be `published_status` and the data type will be of type `string`.

To update the database schema you can run `rake db:migrate` and the schema will reflect the change.

Oh no, we made a mistake, let's get rid of that column name with another migration:

```
rails g migration remove_published_status_from_posts published_status:string
```

If you open up this migration file, you will see the following code:

```ruby
class RemovePublishedStatusFromPosts < ActiveRecord::Migration
  def change
    remove_column :posts, :published_status, :string
  end
end
```

So we can add and remove columns automatically by running migration generators. What else can we do? Let's walk through a real world scenario:

```
rails g migration add_post_status_to_posts post_status:boolean
```

With this migration we'll add the column `post_status` with the data type of boolean. While adding this new attribute to one of the forms we discover that the column really needs to be of type `string` instead of being a `boolean`. Let's see if we can use the same syntax for the generator:

```
rails g migration change_post_status_data_type_to_posts post_status:string
```

This won't automatically create the `change_column` method; the file will look something like this:

```ruby
class ChangePostStatusDataTypeToPosts < ActiveRecord::Migration
  def change
  end
end
```

We can simply add in the `change_column` method like this: `change_column :posts, :post_status, :string` and after running `rake db:migrate` our schema will be updated.

[Full migration documentation](http://api.rubyonrails.org/classes/ActiveRecord/Migration.html)

####MODEL GENERATORS
This is a generator type that I use regularly. It does a great job of creating the core code needed to create a model and associated database table without adding a lot of bloat to the application. Let's add a new model to the app called `Author` with columns `name` and `genre`, we can use the model generator with the following CLI command:

```
rails g model Author name:string genre:string bio:text
```

Running this generator will create the following files for us:

```
invoke  active_record
create    db/migrate/20151127225446_create_authors.rb
create    app/models/author.rb
invoke    rspec
create      spec/models/author_spec.rb
invoke      factory_girl
create        spec/factories/authors.rb
```

At a high level, this has created:

+ A database migration that will add a table and add the columns `name`, `genre`, and `bio`.

+ A model file that will inherit from `ActiveRecord::Base`

+ Some mocked RSpec test files

After running `rake db:migrate` it will add the table to the database schema. Let's test this out in the console:

```
Author.all
=> #<ActiveRecord::Relation []>
 
Author.create!(name: "Stephen King", genre: "Horror", bio: "Bio details go here")
=> #<Author id: 1, name: "Stephen King", genre: "Horror", bio: "Bio details go here", created_at: "2015-11-27 22:59:14", updated_at: "2015-11-27 22:59:14"> 
```

So it looks like our model has been created properly. As you can see, this particular generator created a few different pieces of functionality with a single command and it did it with creating minimal code bloat.

####CONTROLLER GENERATORS
Controller generators are great if you are creating static views or non-CRUD related features (we'll walk through why this is the case shortly). Let's create an admin controller that will manage the data flow and view rendering for our `admin` dashboard pages:

```
rails g controller admin dashboard stats financials settings
```

This will create a ton of code! Below is the full list:

```
create  app/controllers/admin_controller.rb
 route  get 'admin/settings'
 route  get 'admin/financials'
 route  get 'admin/stats'
 route  get 'admin/dashboard'
invoke  erb
create    app/views/admin
create    app/views/admin/dashboard.html.erb
create    app/views/admin/stats.html.erb
create    app/views/admin/financials.html.erb
create    app/views/admin/settings.html.erb
invoke  rspec
create    spec/controllers/admin_controller_spec.rb
create    spec/views/admin
create    spec/views/admin/dashboard.html.erb_spec.rb
create    spec/views/admin/stats.html.erb_spec.rb
create    spec/views/admin/financials.html.erb_spec.rb
create    spec/views/admin/settings.html.erb_spec.rb
invoke  helper
create    app/helpers/admin_helper.rb
invoke    rspec
create      spec/helpers/admin_helper_spec.rb
invoke  assets
invoke    coffee
create      app/assets/javascripts/admin.coffee
invoke    scss
create      app/assets/stylesheets/admin.scss
```

So what got added here? Below is a list that is a little more high level:

+ A controller file that will inherit from `ApplicationController`

+ A set of routes to each of the generator arguments: `dashboard`, `stats`, `financials`, and `settings`

+ A new directory for all of the view templates along with a view template file for each of the controller actions that we declared in the generator command

+ A number of view based tests

+ A view helper method file and spec helper file

+ A Coffeescript file for that specific JavaScripts for that controller

+ A `scss` file for the styles for the controller
As you can see, this one generator created a large number of files and code. This is a generator to be careful with – it can create a number of files that are never used and can cause wasted files in an application.

So why are controller generators not the best for creating CRUD based features? What would have happened if we wanted to create a controller that managed the CRUD flow for managing accounts? Here would be one implementation:

```
rails g controller accounts new create edit update destroy index show
```

Immediately you may notice that this would create wasted code since it would create view templates for `create`, `update`, and `destroy` actions, so they would need to be removed immediately. They would also be setup with `get` HTTP requests, which would not work at all. In the next section we're going to cover a better option for creating CRUD functionality.

####RESOURCE GENERATORS
If you are building an API, using a front end MVC framework, or simply want to manually create your views, the `resource` generator is a great option for creating the code. Since we didn't create the `Account` controller we mentioned before, let's build it here:

```
rails g resource Account name:string payment_status:string
```

This creates quite a bit of code for us. Below is the full list:

```
invoke  active_record
create    db/migrate/20151127233150_create_accounts.rb
create    app/models/account.rb
invoke    rspec
create      spec/models/account_spec.rb
invoke      factory_girl
create        spec/factories/accounts.rb
invoke  controller
create    app/controllers/accounts_controller.rb
invoke    erb
create      app/views/accounts
invoke    rspec
create      spec/controllers/accounts_controller_spec.rb
invoke    helper
create      app/helpers/accounts_helper.rb
invoke      rspec
create        spec/helpers/accounts_helper_spec.rb
invoke    assets
invoke      coffee
create        app/assets/javascripts/accounts.coffee
invoke      scss
create        app/assets/stylesheets/accounts.scss
invoke  resource_route
route    resources :accounts
```

So what does our app have now due to the generator? Below is the summary list:

+ A migration file that will create a new database table for the attributes passed to it in the generator

+ A model file that inherits from `ActiveRecord::Base`

+ Tests

+ A controller file that inherits from `ApplicationController`

+ A view directory, but no view template files

+ A view helper file

+ A Coffeescript file for that specific JavaScripts for that controller

+ A `scss` file for the styles for the controller

+ A full `resources` call in the `routes.rb` file

The `resource` generator is a smart generator that creates some of the core functionality needed for a full featured resource without much code bloat. Looking over the files I can't find one file that I need to remove, so that's a good sign.

The last item that was added may not look familiar to you. `resources :accounts` is considered a 'magic' route that entails the full set of RESTful routes needed to perform CRUD in an application. So what does `resources :accounts` translate into?

There's an easy way to find out. Let's run `rake routes` with a filter so it only shows us the routes for accounts:

```
rake routes | grep account
```

This `rake` command will produce the following output in the console:

```
accounts      GET    /accounts(.:format)          accounts#index
              POST   /accounts(.:format)          accounts#create
new_account   GET    /accounts/new(.:format)      accounts#new
edit_account  GET    /accounts/:id/edit(.:format) accounts#edit
account       GET    /accounts/:id(.:format)      accounts#show
              PATCH  /accounts/:id(.:format)      accounts#update
              PUT    /accounts/:id(.:format)      accounts#update
              DELETE /accounts/:id(.:format)      accounts#destroy
```

`resources` automatically creates each of these routes and makes them available to the controller. If you open up the `accounts_controller.rb` file you may notice something interesting: none of the actions shown in the route list are even there! However, I actually like this because it creates the minimal amount of code possible and then lets me add only the features that the app needs. We'll get into a full review of each of the options avaiable with the `resources` method in a later lesson. For right now just know that by default it creates the full suite of CRUD routes.

##Resource Generator/ Routes
####INTRO
In a previous lesson we reviewed each of the popular generators in Rails. I purposefully left one out: the Rails `scaffold` generator. The reason for this is mainly due to the fact that it's not considered a good practice to use scaffolds in a production application. With that being said, I do think it's important to study scaffolds since they can be a great reference for how we can build CRUD functionality into our apps.

First lets discuss why it's not a great idea to use scaffolds in real world development. Let's start with a case study to see what a scaffold actually creates. Run this command in the terminal:

```
rails g scaffold Article title:string body:text
```

Let's review the console log to see what this creates for us:

```
invoke  active_record
create    db/migrate/20151128001950_create_articles.rb
create    app/models/article.rb
invoke    rspec
create      spec/models/article_spec.rb
invoke      factory_girl
create        spec/factories/articles.rb
invoke  resource_route
 route    resources :articles
invoke  scaffold_controller
create    app/controllers/articles_controller.rb
invoke    erb
create      app/views/articles
create      app/views/articles/index.html.erb
create      app/views/articles/edit.html.erb
create      app/views/articles/show.html.erb
create      app/views/articles/new.html.erb
create      app/views/articles/_form.html.erb
invoke    rspec
create      spec/controllers/articles_controller_spec.rb
create      spec/views/articles/edit.html.erb_spec.rb
create      spec/views/articles/index.html.erb_spec.rb
create      spec/views/articles/new.html.erb_spec.rb
create      spec/views/articles/show.html.erb_spec.rb
create      spec/routing/articles_routing_spec.rb
invoke      rspec
create        spec/requests/articles_spec.rb
invoke    helper
create      app/helpers/articles_helper.rb
invoke      rspec
create        spec/helpers/articles_helper_spec.rb
invoke    jbuilder
create      app/views/articles/index.json.jbuilder
create      app/views/articles/show.json.jbuilder
invoke  assets
invoke    coffee
create      app/assets/javascripts/articles.coffee
invoke    scss
create      app/assets/stylesheets/articles.scss
invoke  scss
create    app/assets/stylesheets/scaffolds.scss
```

If you remember back to the lesson on generators you will remember that the other generators (`migrations`, `controllers`, and `resources)` created a structure and backend functionality for our code. However, scaffolds actually go beyond the other generators and create both the front and backend code needed for CRUD features. If you startup the rails server and navigate to `localhost:3000/articles` you will see the screens below:

![Scaffold Screen](https://s3.amazonaws.com/flatiron-bucket/readme-lessons/scaffolds.png)

Showing scaffolds to someone new to Rails is a great way to amaze them, within less than a minute the scaffold system built an entire CRUD based feature and we didn't write a single line of code!

What did the scaffold build for us? If you go through the files that got printed out in the console you can see:

+ A migration file
+ A Model file
+ A scaffold
+ View templates for each of the controller actions that render a view
+ The full set of RESTful routes
+ And every other component needed to perform CRUD functionality

One thing I really like about using the scaffold generator to teach Rails is how they setup the controller. Below are the contents of the `articles_controller.rb` file:

```ruby
class ArticlesController < ApplicationController
  before_action :set_article, only: [:show, :edit, :update, :destroy]
 
  # GET /articles
  # GET /articles.json
  def index
    @articles = Article.all
  end
 
  # GET /articles/1
  # GET /articles/1.json
  def show
  end
 
  # GET /articles/new
  def new
    @article = Article.new
  end
 
  # GET /articles/1/edit
  def edit
  end
 
  # POST /articles
  # POST /articles.json
  def create
    @article = Article.new(article_params)
 
    respond_to do |format|
      if @article.save
        format.html { redirect_to @article, notice: 'Article was successfully created.' }
        format.json { render :show, status: :created, location: @article }
      else
        format.html { render :new }
        format.json { render json: @article.errors, status: :unprocessable_entity }
      end
    end
  end
 
  # PATCH/PUT /articles/1
  # PATCH/PUT /articles/1.json
  def update
    respond_to do |format|
      if @article.update(article_params)
        format.html { redirect_to @article, notice: 'Article was successfully updated.' }
        format.json { render :show, status: :ok, location: @article }
      else
        format.html { render :edit }
        format.json { render json: @article.errors, status: :unprocessable_entity }
      end
    end
  end
 
  # DELETE /articles/1
  # DELETE /articles/1.json
  def destroy
    @article.destroy
    respond_to do |format|
      format.html { redirect_to articles_url, notice: 'Article was successfully destroyed.' }
      format.json { head :no_content }
    end
  end
 
  private
    # Use callbacks to share common setup or constraints between actions.
    def set_article
      @article = Article.find(params[:id])
    end
 
    # Never trust parameters from the scary internet, only allow the white list through.
    def article_params
      params.require(:article).permit(:title, :body)
    end
end
```

If you look through the code you'll see a few familiar methods, such as: `index`, `new`, `edit`, `update`, and `show`. If you remember prior lessons you may remember that we had some duplicate code, for example if we had: `show`, `edit`, and `update` actions in the controller we had three different calls such as:

```ruby
@article = Article.find(params[:id])
```

This was necessary so that we could grab the `/:id` parameter from the URL string, but as you may have noticed, the scaffold implemented an elegant solution to remove the duplicate code by placing the code:

```ruby
before_action :set_article, only: [:show, :edit, :update, :destroy]
```

the `show`, `edit`, `update`, and `destroy` actions will all have the `set_article` method called before any other code in the respective actions is run. If you go down and look for what the `set_article` method does, it's declared below:

```ruby
def set_article
  @article = Article.find(params[:id])
end
```

As you can see, the method returns the `@article` instance variable that each of the controller actions will automatically have because of the `before_action`. Pretty cool, right?

Another way that scaffolds show how to DRY up controller code can be found in the other `private` method:

```
def article_params
  params.require(:article).permit(:title, :body)
end
```

So even though scaffolds are great for learning how CRUD works in Rails, it's still considered a bad practice to use them in production applications. The main reason why scaffolds are discouraged in production is because they create so much code and so many files that they can be hard to manage.

From my personal development experience, I've found that my best applications were built by following TDD principles where I created features one element at a time, whereas scaffolds build dozens of processes instantly. Truth be told, when I use a scaffold it usually takes me more time going through each file that was created to remove code/files that I'm not going to be using than it would have taken to build the feature from scratch!

####SCAFFOLD VS RESOURCE GENERATORS
Since we've already discussed that it's discouraged to utilize scaffolds in production applications, it's fair to ask what a good alternative is. That's where the `resource` generator comes into play.

We've discussed in detail what the `resource` generator does, so what makes it a better solution than scaffolds?

The scaffold generator tends to be very 'opinionated' with the code that it builds. By opinionated I mean that it builds out the system in a very specific manner, which is rarely the way that you would want to build your application.

With modern development practices, a large number of Rails apps are leveraging client side MVC setups such as Backbone or AngularJS. These frameworks render the view templates for a Rails application pointless, so if you rely on using scaffolds you're going to have to be removing quite a bit of code after each `generate` command.

If you compare this with the `resource` generator, the code from the `resource` generator will build out the base setup required for the new feature, but it will let you control the implementation.

Let's pretend that you're integrating the ReactJS framework into your Rails application. If you use a scaffold you will instantly have to go through the code and remove a large percentage of the code along with around 50% of the files themselves. Whereas if you run the `resource` generator, it simply creates the: migrations, model, routes, controller, and asset pipeline files. This means that you will be able to instantly start implementing the ReactJS components instead of having to be concerned on which elements need to be deleted.

An analogy that is helpful for me is going on a road trip:

+ A `scaffold` generator is like driving double the speed limit and driving past your exit by 50 miles, forcing you to backtrack all the way back to get on the right road

+ A `resource` generator is like going the speed limit and taking the right exit; the `scaffold` may have flown by you initially, but with the `resource` option you'll end up getting to the final destination in one piece

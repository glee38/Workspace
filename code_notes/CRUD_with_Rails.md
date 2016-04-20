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
If you know how to utilize the form_tag method for creating forms in Rails you may wonder why you need to learn a new form building process. Let's imagine that you've been tasked with creating the world's first pet hamster social network, and one of the requirements is that the hamster profile page needs to have about 100 different form fields that can be edited. If you are using the form_tag method, your application will be technically resubmitting all 100 fields each time you edit the data. Your form view templates will also have 100 calls to the @hamster instance variable and each of the hamster attributes. Thankfully form_for is here and will help clean up the form view template and provide some additional benefits that we'll explore in this lesson.
RECAP OF FORM_TAG
To review, the form_tag helper method allows us to automatically generate HTML form code and integrate data to both auto fill the values as well as have the form submit data that the controller can use to either create or update a record in the database. It allows for you to pass in: the route for where the parameters for the form will be sent, the HTTP method that the form will utilize, and the attributes for each field.
ISSUES WITH USING FORM_TAG
Before we get into the benefits and features of the form_for method, let's first discuss some of the key drawbacks to utilizing form_tag:
Our form has to manually be passed the route for where the form parameters will be submitted
The form has no knowledge of the form's goal; it doesn't know if the form is meant to create or update a record
You're forced to have duplicate code throughout the form; it's hard to adhere to DRY principles when utilizing the form_tag
DIFFERENCE BETWEEN FORM_FOR AND FORM_TAG
The differences between form_for and form_tag are subtle, but important. Below is a basic breakdown of the differences. We'll start with talking about them at a high level perspective and then get into each one of the aspects on a practical/implementation basis:
The form_for method accepts the instance of the model as an argument. Using this argument, form_for is able to make a bunch of assumptions for you.
form_for yields an object of class FormBuilder
form_for automatically knows the standard route (it follows RESTful conventions) for the form data as opposed to having to manually declare it
form_for gives the option to dynamically change the submit button text (this comes in very handy when you're using a form partial and the new and edit pages will share the same form, but more on that in a later lesson)
A good rule of thumb for when to use one approach over the other is below:
Use form_for when your form is directly connected to a model. Extending our example from the introduction, this would be our Hamster's profile edit form that connects to the profile database table. This is the most common case when form_for is used
Use form_tag when you simply need an HTML form generated. Examples of this would be: a search form field or a contact form
IMPLEMENTATION OF FORM_FOR
Let's take the edit form that utilized the form_tag that we built before for posts and refactor it to use form_for. As a refresher, here is the form_tag version:
<% # app/views/posts/edit.html.erb %>
<h3>Post Form</h3>
 
<%= form_tag post_path(@post), method: "put" do %>
  <label>Post title:</label><br>
  <%= text_field_tag :title, @post.title %><br>
 
  <label>Post Description</label><br>
  <%= text_area_tag :description, @post.description %><br>
 
  <%= submit_tag "Submit Post" %>
<% end %>
Let's take this refactor one element at a time. Since we already have access to the @post instance variable we know that we can pass that to the form_for method. We also can remove the path argument and the method call since form_for will automatically set these for us. How does form_for know that we want to use PUT for the form method? It's smart enough to know that since it's dealing with a pre-existing record that you want to utilize PUT over POST.
<%= form_for(@post) do |f| %>
The |f| is an iterator variable that we can use on the new form object that will allow us to dynamically assign form field elements to each of the post data attributes, along with auto filling the values for each field. We get this ActionView functionality because we're using the form_for method and that gives us access to the FormBuilder module in Rails (Documentation). Inside of the form we can now refactor the fields:
<label>Post title:</label><br>
<%= f.text_field :title %><br>
 
<label>Post Description</label><br>
<%= f.text_area :description %><br>
Isn't that much cleaner? Notice how we no longer have to pass in the values manually? By passing in the attribute as a symbol (e.g. :title) that will automatically tell the form field what model attribute to be associated with. It also is what auto fills the values for us. Now let's refactor the submit button, instead of <%= submit_tag "Submit Post" %> we can change it to:
<%= f.submit %>
Lastly, form_for also automatically sets the authenticity_token value for us, so we can remove the <%= hidden_field_tag :authenticity_token, form_authenticity_token %> line completely. Our new form will look something like this:
<h3>Post Form</h3>
 
<%= form_for(@post) do |f| %>
  <label>Post title:</label><br>
  <%= f.text_field :title %><br>
 
  <label>Post Description</label><br>
  <%= f.text_area :description %><br>
 
  <%= f.submit %>
<% end %>
Our refactor work isn't quite done. If you had previously created a PUT route like we did in the form_tag lesson, we'll need to change that to a PATCH method since that is the HTTP verb that form_for utilizes. We can make that change in the config/routes.rb file:
patch 'posts/:id', to: 'posts#update'
What's the difference between PUT and PATCH? The differences are pretty subtle. On a high level PUT has the ability to update the entire object, whereas PATCH simply updates the element that was changed. Many developers choose to utilize PATCH as much as possible because it requires less overhead, however, it is pretty rare when you will need to distinguish between the two verbs and they are used interchangeably quite often.
You can also put in an additional argument if you're using the resources method for update and this will all happen automatically.
Now if you start the Rails server and go to an edit page you'll see that the data is loaded into the form and everything appears to be working properly, however if you change the value of one of the form fields and click Update post you will see that the record isn't updated. So what's happening? When I run into behavior like this I'll usually look at the console logs to see if it tells me anything. Below is the screenshot of what I see after submitting the form:
Unpermitted Parameters
Because form_for is bound directly with the Post model, we need to pass the model into update method in the controller. So let's change: @post.update(title: params[:title], description: params[:description]) to:
@post.update(params.require(:post))
So why do we need to require the post? If you look at the old form the params would look something like this:
{
  "title": "My Title",
  "description": "My description"
}
With the new structure for the form_for the params look like this:
{
  "post": {
            "title": "My Title",
            "description": "My description"
          }
}
Notice how the attributes are now encapsulated in the "post" object? That's the main reason we needed to add the require method.
Now if you go back to the edit page and submit the form, the record will be updated in the database sucessfully.
SUMMARY
Nice work, you now know how to integrate multiple form helpers into a Rails application and you should have a good idea on when to properly use form_for vs form_tag.
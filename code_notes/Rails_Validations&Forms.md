##ActiveRecord Validations
ActiveRecord can validate our models for us before they even touch the database. This means it's harder to end up with bad data, which can cause problems later even if our code is technically bug-free.

We can use `ActiveRecord::Base` class methods like `#validates` to set things up.

####OBJECTIVES
After this lesson, you should be able to:

+ Identify when validation occurs in the lifespan of an object
+ Introspect on the `ActiveRecord::Errors` collection object on an AR instance
	+ use `#valid?`
	+ use `#errors`
+ Generate `full_messages` for errors
+ Check an attribute for validation errors
+ Add a custom validation error to an AR model

####CONTEXT: DATABASES AND DATA VALIDITY
What is a "validation"?

In the context of Rails, validations are special method calls that go at the top of model class definitions and prevent them from being saved to the database if their data doesn't look right.

In general, "validations" are any code that perform the job of protecting the database from invalid code.

####AR VALIDATIONS ARE NOT DATABASE VALIDATIONS
Many relational databases such as SQLite have data validation features that check things like length and data type. In Rails, these validations **are not** used, because each database works a little differently, and handling
everything in ActiveRecord itself guarantees that we'll always get the same features no matter what database we're using under the hood.

####WHAT IS "INVALID DATA"?
Suppose you get a new phone and you ask all of your friends for their phone number again. One of them tells you, "555-868-902". If you're paying attention,
you'll probably wrinkle your nose and think, "Wait a minute. That doesn't sound like a real phone number."

"555-868-902" is an example of **invalid data**... for a phone number. It's probably a valid account number for some internet service provider in Alaska, but there's no way to figure out what your friend's phone number is from those nine numbers. It's a showstopper, and even worse, it kind of looks like valid data if you're not looking closely.

####VALIDATIONS PROTECT THE DATABASE
Invalid data is the bogeyman of web applications: it hides in your database until the worst possible moment, then jumps out and ruins everything by causing confusing errors.

Imagine the phone number above being saved to the database in an application that makes automatic calls using the Twilio API. When your system tries to call this number, there will be an error because no such phone number exists, which means you need to have an entire branch of code dedicated to handling *just* that edge case.

It would be much easier if you never have bad data in the first place, so you can focus on handling edge cases that are truly unpredictable.

That's where validations come in.

####BASIC USAGE
For more examples of basic validation usage, see the Rails Guide for [Active Record Validations](http://guides.rubyonrails.org/active_record_validations.html). Take a few minutes to browse the helpers
listed in Section 2.

```ruby
class Person < ActiveRecord::Base
  validates :name, presence: true
end
 
Person.create(name: "John Doe").valid? # => true
Person.create(name: nil).valid? # => false
```

`#validates` is our Swiss Army knife for validations. It takes two arguments: the first is the name of the attribute we want to validate, and the second is a hash of options that will include the details of how to validate it.

In this example, the options hash is `{ presence: true }`, which implements the most basic form of validation, preventing the object from being saved if its `name` attribute is empty.

####LIFECYCLE TIMING
Before proceeding, keep the answer to this question in mind:

**What is the difference between `#new` and `#create`?**

If you've forgotten, `#new` instantiates a new ActiveRecord model *without* saving it to the database, whereas `#create` immediately attempts to save it, as if you had called `#new` and then `#save`.

**Database activity triggers validation.** An ActiveRecord model instantiated with `#new` will not be validated, because no attempt to write to the database was made. Validations won't run unless you call a method that actually hits the DB, like `#save`.

The only way to trigger validation without touching the database is to call the `#valid?` method.

For a full list of methods that trigger validation, see [Section 4](http://guides.rubyonrails.org/active_record_callbacks.html#running-callbacks) of the Rails Guide for Active Record Callbacks. Don't worry
about the rest of the information in that guide just yet; we'll go into callbacks later!

####VALIDATION FAILURE
Here it is, the moment of truth. What can we do when a record fails validation?

####HOW CAN YOU TELL WHEN A RECORD FAILS VALIDATION?
**Pay attention to return values!**

By default, ActiveRecord does not raise an exception when validation fails. DB operation methods (such as `#save`) will simply return `false` and decline to update the database.

Every database method has a sister method with a `!` at the end which will raise an exception (`#create!` instead of `#create` and so on).

And of course, you can always check manually with `#valid?`.

```ruby
class Person < ActiveRecord::Base
  validates :name, presence: true
end
 
p = Person.new
p.valid? #=> false
p.save #=> false
p.save! #=> EXCEPTION
```

####FINDING OUT WHY VALIDATIONS FAILED
To find out what went wrong, you can look at the model's `#errors` object.

You can check all errors at once by examining `errors.messages`.

```ruby
p = Person.new
p.errors.messages #=> empty
p.valid? #=> false
p.errors.messages #=> name: can't be blank
```

You can check one attribute at a time by passing the name to `errors` as a key, like so:

```ruby
person.errors[:name]
```

####DISPLAYING VALIDATION ERRORS IN VIEWS
See [Section 8](http://guides.rubyonrails.org/active_record_validations.html#displaying-validation-errors-in-views) of the Rails Guide for an example of how to use the `ActiveModel::Errors#full_messages` helper, reproduced here for convenience:

```html
<% if @article.errors.any? %>
  <div id="error_explanation">
    <h2>
      <%= pluralize(@article.errors.count, "error") %>
      prohibited this article from being saved:
    </h2>
 
    <ul>
    <% @article.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
    </ul>
  </div>
<% end %>
```

This constructs more complete-looking sentences from the more terse messages available in `errors.messages`.

####OTHER BUILTIN VALIDATORS
Rails has a host of built in helpers.

#####LENGTH
`length` is one of the most versatile:

```ruby
class Person < ActiveRecord::Base
  validates :name, length: { minimum: 2 }
  validates :bio, length: { maximum: 500 }
  validates :password, length: { in: 6..20 }
  validates :registration_number, length: { is: 6 }
end
```

The `in` argument makes use of a [Range](http://ruby-doc.org/core/Range.html).

Remember that there's no syntactical magic happening with any of these method calls. If we weren't using Ruby's "poetry mode" (which is considered standard for Rails), the above code would look like this:

```ruby
class Person < ActiveRecord::Base
  validates(:name, { :length => { :minimum => 2 } })
  validates(:bio, { :length => { :maximum => 500 } })
  validates(:password, { :length => { :in => 6..20 } })
  validates(:registration_number, { :length => { :is => 6 } })
end
```

Phew!

#####UNIQUENESS
Another common builtin validator is `uniqueness`:

```ruby
class Account < ActiveRecord::Base
  validates :email, uniqueness: true
end
```

This will prevent any account from being created with the same email as another already-existing account.

#####CUSTOM MESSAGES
This isn't a validator in its own right, but a handy convenience option for specifying your own error messages:

```ruby
class Person < ActiveRecord::Base
  validates :not_a_robot, acceptance: true, message: "Humans only!"
end
```

####CUSTOM VALIDATORS
There are three ways to implement custom validators, with examples in [Section 6](http://guides.rubyonrails.org/active_record_validations.html#performing-custom-validations) of the Rails Guide.

Of the three, `#validate` is the simplest, because all you need to do is define an instance method that is invoked by `#validate`. This is probably the best way to start with most custom validations, because everything is in one place, and you can come back later to re-organize if it starts to get more complex.

+ Calling `#validate` (that's "validate" without an "s") with the name of an instance method

Use this approach when you're not sure which to use. If you end up needing to use the same validation logic on a different model, you can easily extract the instance method into one of the ActiveModel classes and use `#validates` or `#validates_with instead`.

+ Subclassing `ActiveModel::EachValidator` and invoking with an inflected key in the options hash

This is best for validating a single attribute on one model, especially one that you're already using builtin validators for.

For example, in the Rails Guide, they define `EmailValidator` and then pass the `email: true` key-value pair to `#validates` to invoke it.

+ Subclassing `ActiveModel::Validator` and invoking with `#validates_with`

This approach is best when you want to do a whole bunch of validations on several different models. You can just call `validates_with MyValidator` on each of them.

So, to recap:

+ `validate` for quick custom validations that you can extract later.

+ `EachValidator` and `validates` for validating one specific attribute.

+ `Validator` and `validates_with` for doing many validations in one pass.

##Validations in Controller Actions
Now that we know Rails automatically performs validations defined on models, let's use this information to help users fix typos or other problems in their form submissions.

At this point, we'll be covering step two of the following flow:

1. User fills out the form and hits "Submit", transmitting the form data via a POST request.

2. **The controller sees that validations have failed, and re-renders the form.**

3. The view displays the errors to the user.

####OBJECTIVES
After this lesson, you'll be able to...

+ Define validations on a model
+ Use the validation state of a model in a response conditional in an action
+ Re-render a form with validation errors
+ Validate a create action
+ Validate an update action

####MANUALLY CHECKING VALIDATION
Up until this point, our `create` action has looked something like this:

```ruby
# app/controllers/posts_controller.rb
 
  def create
    @post = Post.create(post_params)
 
    redirect_to post_path(@post)
  end
```

However, we have two problems now:

1. If the post is invalid, there will be no `show` path to redirect to. The post was never saved to the database, so that `post_path` will result in a 404!
2. If we redirect, we start a new page load, which will lose all of the feedback from the validations.

####A NOTE ABOUT PAGE LOADS
When a form is submitted, a **full page load** occurs, as if you had navigated to a completely new URL. This means that all of the variables set by the controller's `new` action (like `@post`) *disappear* and are unavailable to the `create` action.

The browser throws everything out after each request, except for cookies.

Rails throws everything out after each request, except for the `session` hash.

You're probably used to validations happening almost instantaneously on websites that you interact with on a daily basis. When you get validation feedback
*without* a full page load, that's JavaScript at work, sneakily performing requests in the background without throwing away the current page. We won't be using that advanced technique just yet!

Let's use `valid?` to see what's going on before deciding how to respond:

```ruby
# app/controllers/posts_controller.rb
 
  def create
    # Create a brand new, unsaved, not-yet-validated Post object from the form.
    @post = Post.new(post_params)
 
    # Run the validations WITHOUT attempting to save to the database, returning
    # true if the Post is valid, and false if it's not.
    if @post.valid?
      # If--and only if--the post is valid, do what we usually do.
      @post.save
      # This returns a status_code of 302, which instructs the browser to
      # perform a NEW REQUEST! (AKA: throw @post away and let the show action
      # worry about re-reading it from the database)
      redirect_to post_path(@post)
    else
      # If the post is invalid, hold on to @post, because it is now full of
      # useful error messages, and re-render the :new page, which knows how
      # to display them alongside the user's entries.
      render :new
    end
  end
```

`render` can be instructed to render the templates from other actions. In the above code, since we want the `:new` template from the same controller, we don't have to specify anything except the template name.

You can read more about this (and other) creative uses of `render` in Section 2.2.2 of the Rails Guide on [Layout and Rendering](http://guides.rubyonrails.org/layouts_and_rendering.html#using-render).

Remember: **redirects incur a new page load**. When we redirect after validation failure, we **lose** the instance of `@post` that has feedback (messages for the
user) in its `errors` attribute.

Another way to differentiate redirects is this:

+ If you hit Refresh after a redirect/page load, your browser resubmits the `GET` request without complaint.

+ If you hit Refresh after rendering on a form submit, your browser gives you a popup to confirm that you want to resubmit form data with the `POST` request.

In the next lessons, we'll learn how to use the error information in `@post.errors` to display feedback to the user through our view.

##Validations with form_tag

Now that we've learned to handle the server side of validations, we need to take care of the client side.
At this point, we'll be in step three of the following flow:

1. User fills out the form and hits "Submit", transmitting the form data via a POST request.
2. The controller sees that validations have failed, and re-renders the form.
3. **The view displays the errors to the user.**

####OBJECTIVES
After this lesson, you'll be able to...

+ Prefill in form values based on an instance
+ Print out full error messages based on an invalid instance
+ Introspect on errors for a field
+ Apply an error class to invalid fields

####PRE-FILLING FORM VALUES
No one likes re-doing work. First, let's make sure we know how to pre-fill forms with the user's input so they don't have to type everything all over again.

There are two ways to pre-fill forms in rails; `form_tag` and `form_for`. `form_for` is *very* heavy on Rails magic, and continues to baffle scientists to this day, so we'll be going over `form_tag` first.

Let's start with a vanilla form (no pre-filled values yet), using the [FormTagHelper](http://api.rubyonrails.org/classes/ActionView/Helpers/FormTagHelper.html):

```html
<!-- app/views/people/new.html.erb //-->
 
<%= form_tag("/people") do %>
  Name: <%= text_field_tag "name" %><br>
  Email: <%= text_field_tag "email" %>
  <%= submit_tag "Create Person" %>
<% end %>
```

Here's what the HTML output will look like:

```html
<form action="/people" accept-charset="UTF-8" method="post">
  <input name="utf8" type="hidden" value="&#x2713;" />
  <input type="hidden" name="authenticity_token" value="TKTzvQF+atT/XHG/7h48xKVdXvILdiPj83XQhn2mWBNNhvv0Oh5YfAl2LM3DlHsQjbMOFVsYEyOwj+rPaSk3Bw==" />
  Name: <input type="text" name="name" id="name" /><br />
  Email: <input type="text" name="email" id="email" />
  <input type="submit" name="commit" value="Create Person" />
</form>
```

We're working with this `Person` model:

```ruby
# app/models/person.rb
 
class Person < ActiveRecord::Base
  validates :name, format: { without: /[0-9]/, message: "does not allow numbers" }
  validates :email, uniqueness: true
end
```

This means validation will fail if we put numbers into the "Name" field, and the form will be re-rendered with the invalid `@person` object available.

Remember that our `create` action now looks like this:

```ruby
# app/controllers/people_controller.rb
 
  def create
    @person = Person.new(person_params)
 
    if @person.valid?
      @person.save
      redirect_to person_path(@person)
    else
      # re-render the :new template WITHOUT throwing away the invalid @person
      render :new
    end
  end
```

With this in mind, we can use the invalid `@person` object to "re-fill" the usually-empty `new` form with the user's invalid entries. This way they don't have to re-type anything.

(You wouldn't *always* want to do this, for example, with credit card numbers, because you want to minimize the amount of times sensitive information travels back and forth over the internet.)

Now, let's plug the information back into the form:

```html
<!-- app/views/people/new.html.erb //-->
 
<%= form_tag "/people" do %>
  Name: <%= text_field_tag "name", @person.name %><br>
  Email: <%= text_field_tag "email", @person.email %>
  <%= submit_tag "Create Person" %>
<% end %>
```

As you can see from the [docs](http://api.rubyonrails.org/classes/ActionView/Helpers/FormTagHelper.html#method-i-text_field_tag), the second argument to `text_field_tag`, as with most form tag helpers, is the "default" value.
The HTML for the two field inputs used to look like this:

```
Name: <input type="text" name="name" id="name" /><br>
Email: <input type="text" name="email" id="email" />
```

But now it will look like this:

```
Name: <input type="text" name="name" id="name" value="Jane Developer" /><br />
Email: <input type="text" name="email" id="email" value="jane@developers.fake" />
```

When the browser renders those inputs, they'll be pre-filled with the data in their `value` attributes.

This is the same technique used to create `edit`/`update` forms.

We can also use the **same** form code for empty and pre-filled forms, because `@person = Person.new` will create an empty model object whose attributes are all `nil`.

####DISPLAYING ALL ERRORS WITH `ERRORS.FULL_MESSAGES`
When a model fails validation, its `errors` attribute is filled with information about what went wrong. Rails creates an [ActiveModel::Errors](http://api.rubyonrails.org/classes/ActiveModel/Errors.html) object to carry this information.

The simplest way to show errors is to just spit them all out at the top of the form by iterating over `@person.errors.full_messages`. But first, we'll have to
check whether there are errors to display with `@person.errors.any?`.

```html
<% if @person.errors.any? %>
  <div id="error_explanation">
    <h2>There were some errors:</h2>
    <ul>
      <% @person.errors.full_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
    </ul>
  </div>
<% end %>
```

If the model has two errors, there will be two items in `full_messages`, which could result in the following HTML:

```html
<div id="error_explanation">
  <h2>There were some errors:</h2>
  <ul>
    <li>Name does not allow numbers</li>
    <li>Email is already taken</li>
  </ul>
</ul>
```

This is nice, but it's not very helpful from a user interface standpoint. It would be much better if the incorrect fields themselves were highlighted
somehow.

####DISPLAYING PER-FIELD ERRORS WITH `ERRORS[]`
`ActiveModel::Errors` has much more than just a list of
`full_message` error strings. It can also be used to access field-specific errors by interacting with it like a hash. If the field has errors, they will be returned in an array of strings:

```ruby
@person.errors[:name] #=> ["does not allow numbers"]
@person.errors[:email] #=> []
```

With this in mind, we can conditionally "error-ify" each field in the form, targeting the divs containing each field:

```html
<div class="field">
  <%= label_tag "name", "Name" %>
  <%= text_field_tag "name", @person.name %>
</div>
```

And conditionally adding a class if there are errors:

```html
<div class="field<%= ' field_with_errors' if @person.errors[:name].any? %>">
  <%= label_tag "name", "Name" %>
  <%= text_field_tag "name", @person.name %>
</div>
```

####THE WHOLE PICTURE
By now, our full form has grown quite a bit:

```html
<%= form_tag("/people") do %>
  <% if @person.errors.any? %>
    <div id="error_explanation">
      <h2>There were some errors:</h2>
      <ul>
        <% @person.errors.full_messages.each do |message| %>
          <li><%= message %></li>
        <% end %>
      </ul>
    </div>
  <% end %>
 
 
  <div class="field<%= ' field_with_errors' if @person.errors[:name].any? %>">
    <%= label_tag "name", "Name" %>
    <%= text_field_tag "name", @person.name %>
  </div>
 
  <div class="field<%= ' field_with_errors' if @person.errors[:email].any? %>">
    <%= label_tag "email", "Email" %>
    <%= text_field_tag "email", @person.email %>
  </div>
 
  <%= submit_tag "Create" %>
<% end %>
```

Notice that some whitespace has been added for "breathing room" and increased readability. Additionally, indentation has been very carefully maintained.

It's already starting to feel pretty unwieldy to manually manage all of this conditional display logic, but without an understanding of the dirty details, we can't even begin to use more powerful tools like `form_for` correctly.

Next, we'll dive into a lab using `form_tag` and artisinally craft our own markup.

##Validations with form_for
####THE DIFFERENCES BETWEEN `FORM_FOR` AND `FORM_TAG`
This step will make heavy usage of `form_for`, the high-powered alternative to `form_tag`. The biggest difference between these two helpers is that `form_for` creates a form specifically **for** a model object. `form_for` is full of convenient features.

In the example below, `@post` is the model object that needs a form. `form_for` automatically performs a route lookup to find the right URL for post.

`form_for` takes a block. It passes an instance of [FormHelper](http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html) as a parameter to the block, which is what `f` is below.

A basic implementation looks like this:

```html
<!-- app/views/posts/edit.html.erb //-->
 
<%= form_for @post do |f| %>
  <%= f.text_field :title %>
  <%= f.text_area :content %>
  <%= f.submit %>
<% end %>
```

This creates the HTML:

```html
<form class="edit_post" id="edit_post" action="/posts/1" accept-charset="UTF-8" method="post">
  <input name="utf8" type="hidden" value="&#x2713;" />
  <input type="hidden" name="_method" value="patch" />
  <input type="hidden" name="authenticity_token" value="nRPP2OqVKB00/Cr+8EvHfYrb5sAkZRtr8f6dzBaJAI+cMceR0fUatcLWd4zdwYCpojW2J3QLK6uyBKeFAgZvmw==" />
  <input type="text" name="post[title]" id="post_title" value="Existing Post Title"/>
  <textarea name="post[content]" id="post_content">Existing Post Content</textarea>
  <input type="submit" name="commit" value="Update Post" />
</form>
```

Here's what we would need to do with `form_tag` to generate the exact same HTML:

```html
<!-- app/views/posts/new.html.erb //-->
 
<%= form_tag post_path(@post), method: "patch", name: "edit_post", id: "edit_post" do %>
  <%= text_field_tag "post[title]", @post.title %>
  <%= text_area "post[content]", @post.content %>
  <%= submit_tag "Update Post" %>
<% end %>
```

`form_tag` doesn't know what action we're going to use it for, because it has no model object to check. `form_for` knows that an empty, unsaved model object needs a `new` form and a populated object needs an edit `form`. This means we get to skip all of these steps:

1. Setting the `name` and `id` of the `<form>` element.
2. Setting the method to `patch` on edits.
3. Setting the text of the `<submit>` element.
4. Specifying the root parameter name (`post[whatever]`) for every field.
5. Choosing the attribute (`@post.whatever`) to fill for every field.

Nifty!

####USING `FORM_FOR` TO GENERATE EMPTY FORMS
To wire up an empty form in our `new` view, we need to create a blank object:

```ruby
# app/controllers/posts_controller.rb
 
  def new
    @post = Post.new
  end
```

Here's our usual vanilla `create` action:

```ruby
# app/controllers/posts_controller.rb
 
  def create
    @post = Post.create(post_params)
 
    redirect_to post_path(@post)
  end
```

We still have to solve the dual problem of what to do when there's no valid model object to redirect to, and how to hold on to our error messages while re-rendering the same form.

####RE-RENDERING WITH ERRORS
Remember from a few lessons ago how CRUD methods return `false` when validation fails? We can use that to our advantage here and branch our actions based on the result:

```ruby
# app/controllers/posts_controller.rb
 
  def create
    @post = Post.new(post_params)
 
    if @post.save
      redirect post_path(@post)
    else
      render :new
    end
  end
```

####FULL MESSAGES WITH PREPOPULATED FIELDS
Because of `form_for`, Rails will automatically prepopulate the `new` form with the values the user entered on the previous page.

To get some extra verbosity, we can add the snippet from the previous lesson to the top of the form:

```html
<!-- app/views/posts/new.html.erb //-->
 
<% if @post.errors.any? %>
  <div id="error_explanation">
    <h2>
      <%= pluralize(@post.errors.count, "error") %>
      prohibited this post from being saved:
    </h2>
 
    <ul>
    <% @post.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
    </ul>
  </div>
<% end %>
```

####MORE FREEBIES: `FIELD_WITH_ERRORS`
Let's look at another nice feature of `FormHelper`. Here's our form_for code again:

```html
<!-- app/views/posts/edit.html.erb //-->
 
<%= form_for @post do |f| %>
  <%= f.text_field :title %>
  <%= f.text_area :content %>
  <%= f.submit %>
<% end %>
```

The `text_field` call generates this tag:

```html
<input type="text" name="post[title]" id="post_title" value="Existing Post Title"/>
```

Not only will `FormHelper` pre-fill an existing `Post` object's data, it will also wrap the tag in a div with an error class if the field has failed validation:

```html
<div class="field_with_errors">
  <input type="text" name="post[title]" id="post_title" value="Existing Post Title"/>
</div>
```

This can also result in some unexpected styling changes, because `<div>`s are block tags (which take up the entire width of their container) while `<input>`s are inline tags. If your layout suddenly gets messed up when a field has errors, this is probably why.

####RECAP
`form_for` gives us a lot of power!

Our challenge as developers is to keep track of the different layers of magic that makes this tool so convenient. The old adage is true: we're responsible for understanding not only how to use `form_for`, but also why it works. Otherwise, we'll be completely lost as soon as a sufficiently unusual edge case appears.

When in doubt, **read the HTML**. Get used to hitting the "View Source" and "Open Inspector" hotkeys in your browser (`Ctrl-u` and `Ctrl-Shift-i` on Chrome Windows), and remember that most browsers let you [examine POST data in their developer network tools](http://superuser.com/questions/395919/where-is-the-post-tab-in-chrome-developer-tools-network).

##Delete forms
####OBJECTIVES
After this lesson, you'll be able to...

+ Draw a `delete` route mapping to a `#destroy()` action
+ Explain the problem with submitting `delete` requests
+ Use `form_tag` to build a delete form for an object
+ Build a `#destroy()` action that finds the instance, destroys it, and redirects to index
+ Use `link_to` and `button_to :method => :delete` to destroy an object without a form.

####IGNORANCE IS BLISS
Before we dive into the problem with `PATCH` and `DELETE` requests, let's proceed as if we were none the wiser, and set up our route and form like usual:

```ruby
# config/routes.rb
 
delete 'people/:id', to: 'people#destroy'
```

```html
# app/views/people/show.html.erb
 
<h2><%= @person.name %></h2>
<%= @person.email %>
<%= form_tag people_path(@person.id), method: "delete" %>
  <%= submit_tag "Delete #{@person.name}" %>
<% end %>
```

But, wait a minute... there's something weird about the output we get:

```html
<h2>Caligula</h2>
caligula@rome-circa-50-AD.com
<form accept-charset="UTF-8" action="/people/1" method="post">
  <input name="_method" type="hidden" value="delete" />
  <input name="utf8" type="hidden" value="&#x2713;" />
  <input name="authenticity_token" type="hidden" value="f755bb0ed134b76c432144748a6d4b7a7ddf2b71" />
  <input name="commit" type="submit" value="Delete Caligula" />
</form>
```

Enhance!

```html
> <form accept-charset="UTF-8" action="/people/1" method="**post**">
>   <input name="_method" type="hidden" value="**delete**" />
```

What's going on? Why the extra input?

####PROGRAMMING IS HARD
Web developers love to be on the cutting edge. We hoover up new tools and techniques, and aren't afraid of breaking a few million eggs to figure out that we actually have no idea how to make an omelette. Projects like Rails themselves are a product of this incredible devotion to progress and automation.

**Browser** and **server** developers are the yin to the web developers' yang. These are the people who own and maintain the tools themselves: Internet Explorer, Firefox, Chrome, Apache, and so on.

When a web developer makes a mistake, it might affect the users of their site.

When a browser or server developer makes a mistake, it might affect [over a billion people](http://venturebeat.com/2015/05/28/google-chrome-now-has-over-1-billion-users/)!

Because of this, browser/server developers have to go slow. *Really* slow. And they have to resist the urge to release "duct tape" solutions, because duct tape doesn't scale to a billion users!

This means that, sometimes, incomplete solutions can remain in place for years, or even decades, while the maintainers go back and forth trying to find a better approach that won't open an eldritch portal to Bosch's [Garden of Earthly Delights](https://www.khanacademy.org/humanities/renaissance-reformation/northern/hieronymus-bosch/a/bosch-the-garden-of-earthly-delights).

####WHAT'S ALL THIS HAVE TO DO WITH `DELETE` REQUESTS?
As of HTML5, forms officially do not support `DELETE` and `PATCH` for their methods.

There's no short and sweet answer to explain this. If you want to dive deep and understand as much as possible about the decisions that went into it, you can read more on [this illuminating StackExchange post](http://programmers.stackexchange.com/questions/114156/why-are-there-are-no-put-and-delete-methods-on-html-forms), but for the purposes of succinct explanation, you can always stick with the tried and true "for historical reasons".

What you're seeing in the above `#form_tag()` behavior is a **workaround** implemented for us by Rails itself. With this in mind, we get the best of both worlds:

+ We get to be **good HTTP-abiding citizens** who use the correct request methods for their corresponding goals (`GET` for read, `PATCH` for update, and so on).

+ We get to **maintain our sanity** and not worry about W3C drama while writing views.

####THAT'S GREAT. CAN WE ACTUALLY DELETE SOMETHING NOW?

Thus enlightened, we can (finally) proceed with our original goal:

```ruby
# app/controllers/people_controller.rb
 
  def destroy
    Person.find(params[:id]).destroy
    redirect_to people_url
  end
```

Nothing too special happening here, except for a bit of method-chaining to immediately destroy the instance we find.

####FANCY JAVASCRIPT HELPER
As shown, you have to go to a user's `show` page to delete them. What if we want an admin control panel where users can be deleted from a list?

```html
<!-- app/views/people/index.html.erb //-->
 
<% @people.each do |person| %>
<div class="person">
  <span><%= person.name %></span>
  |
  <%= link_to "Delete", person, method: :delete, data: { confirm: "Really?" } %>
</div>
<% end %>
```

[`link_to`](http://api.rubyonrails.org/classes/ActionView/Helpers/UrlHelper.html#method-i-link_to) is a method of `UrlHelper` that has a number of convenient features.

The HTML generated by that call to `link_to` looks like this:

```html
<a data-confirm="Really?" rel="nofollow" data-method="delete" href="/people/1">Delete</a>
```

The `data-confirm` attribute and the `data-method` attribute rely on some JavaScript built into Rails.

`data-method` will "submit" a `DELETE` request, as if a form had been submitted. It will use `GET` (the default method used by all browsers for HTML links) if the user has JavaScript disabled.

`data-confirm` pops up a confirmation window before the link is followed, allowing the user to make sure they're ready to delete someone forever (what a decision!).

##Rails Testing
####THREE TEST TYPES
We'll be covering three types of tests:

+ **Models** (RSpec)
+ **Controllers** (RSpec)
+ **Features** (RSpec/Capybara)
Features are the fanciest, so we'll leave them for last. They are preferred over regular Rails "View" tests.

####RSPEC
By default, Rails uses `Test::Unit` for testing, which keeps its tests in the mysteriously-named `test/` folder.

If you're planning from the start to use RSpec instead, you can tell Rails to skip `Test::Unit` by passing the `-T` flag to `rails new`, like so:

```
rails new cool_app -T
```

Then, you will add the gem to your Gemfile:

```ruby
gem 'rspec-rails'
```

And use the builtin generator to add a `spec` folder with the right boilerplate:

```
bundle install
bundle exec rails g rspec:install
```

This is the Rails equivalent of the usual `rspec --init`.

####AN AUTOMOBILE ANALOGY
It's not enough to know *how* to test applications; we must also understand *why* it makes sense to test them in a certain way. To help with this, try thinking of Rails applications like cars:

+ **Models** are the **basic parts** that make cars useful, like the fuel tank, engine, and tires.

+ **Views** are the **interactive parts** that the driver (user) can see and touch, like the steering wheel, pedals, and gear shift.

+ **Controllers** are the rest of the **connecting parts** under the hood that connect the views to the models, like how the steering column (along with the rest of the steering assembly) connects the wheel to the tires. The users don't
really see them, think of them, or even necessarily know they exist, but they're just as necessary.

Models are not too difficult to test because they have very specific purposes that can be easily separated from the rest of the application.

This holds true for cars as well: a good tire has a tread that grips the road in adverse conditions, doesn't puncture easily, and so on. You don't really need to
think about the rest of the car when you're testing what makes a good wheel.

This gets a little trickier for views and controllers, but we'll talk more about that once we're done with models.

####MODEL TESTS
These go in `spec/models`, one file per model.

Model tests use the least amount of special features, since all you really need is the model class itself. The most common usage for model tests is to make sure you have set up your validations correctly.

Suppose we're working with this model:

```ruby
# app/models/monster.rb
 
class Monster < ActiveRecord::Base
  validates :name, presence: true
  validates :size, inclusion: { in: ["tiny", "average", "like, REALLY big"] }
  validates :taxonomy, format: { with: /\A[a-zA-Z]+ [a-zA-Z]+\z/,
    message: "must include genus and species, like 'Homo Sapien'" }
end
```

####TESTING FOR VALIDITY
First, we'll make sure that it understands a valid Monster:

```ruby
# spec/models/monster_spec.rb
 
describe Monster do
  let(:attributes) do
    {
      name: "Dustwing",
      size: "tiny",
      taxonomy: "Abradacus Nonexistus"
    }
  end
 
  it "is considered valid" do
    expect(Monster.new(attributes)).to be_valid
  end
end
```

Some questions to answer first:

#####WHAT IS `LET`?
If you haven't seen `let` before, it is a [standard helper method](http://www.relishapp.com/rspec/rspec-core/docs/helper-methods/let-and-let) that takes a symbol and a block. It runs the block once per example in which
it is called, and saves the return value in a local variable named according to the symbol. This means you get a fresh copy in every test case.

#####WHY IS `LET` BETTER THAN `BEFORE :EACH`?
It's more fine-grained, which means you have better control over your data. It can be used in combination with `before` statements to set up your test data *just right* before the examples are run.

#####WHY DID WE USE LET TO MAKE AN ATTRIBUTE HASH?
We could have put the entire `Monster.new` call inside our `let` block, but using an attribute hash instead has some advantages:

+ If we want to tweak the data first, we can just pass `attributes.merge(name: "Other")` while preserving the rest of the attributes.

+ We can also refer to `attributes` when making assertions about what the actual object should look like.

It's a good balance between saving keystrokes and maintaining flexibility over your test data.

#####WHERE DOES `BE_VALID` COME FROM?
RSpec provides plenty of builtin matchers, which you can peruse in their [API docs](http://rspec.info/documentation/3.4/rspec-expectations/frames.html#!RSpec/Matchers.html), but `be_valid` is conspicuously absent from the list.

This code uses a neat trick that RSpec refers to as ["predicate matchers"](https://www.relishapp.com/rspec/rspec-expectations/docs/built-in-matchers/predicate-matchers), and you'll see it **a lot** in Rails testing.

In Ruby, it's conventional for methods that return `true` or `false` to be named with a question mark at the end. These methods are called **predicate methods**, because "predicate" is an English grammar term for the part of a sentence that makes a statement about the subject.

As you learned earlier in this unit, Rails provides a `valid?` method that returns `true` or `false` depending on whether the model object in question
passed its validations.

In RSpec, when you call a nonexistant matcher (such as `be_valid`), it strips off the `be_` (`valid`), adds a question mark (`valid?`), and checks to see if the object responds to a method by that name (`monster.valid?`).

####TESTING FOR VALIDATION FAILURE
Now, let's add some tests to make sure our validations are working in the opposite direction:

```ruby
# spec/models/monster.rb
 
  let(:missing_name) { attributes.except(:name) }
  let(:invalid_size) { attributes.merge(size: "not that big") }
  let(:missing_species) { attributes.merge(taxonomy: "Abradacus") }
 
  it "is invalid without a name" do
    expect(Monster.new(missing_name)).not_to be_valid
  end
 
  it "is invalid with an unusual size" do
    expect(Monster.new(invalid_size)).not_to be_valid
  end
 
  it "is invalid with a missing species" do
    expect(Monster.new(missing_species)).not_to be_valid
  end
```

Note that each of these `let` blocks rely on the first one, `attributes`, which contains all of our valid attributes. `missing_name` uses the Rails Hash helper
`except` to exclude the `name`, while the other two use the Ruby standard `merge` to overwrite valid attributes with invalid ones.

#####I SAW SOME SEARCH RESULTS USING `SHOULD`. WHAT IS THAT?
`should` is an older RSpec syntax that has been deprecated in favor of `expect`.

#####ANY OTHER RSPEC FEATURES TO KNOW ABOUT?
Several RSpec features have been moved over time into the
[rspec-collection_matchers](https://github.com/rspec/rspec-collection_matchers) gem, which can make some detailed assertions more readable.

####CONTROLLER TESTS
The biggest risk in writing controller tests is redundancy: controllers exists to connect views and models, so it's difficult to test them in isolation.

First, we'll go over **how** to write controller tests, then our discussion of the **why** will bring us into the final subject of "feature tests".

```ruby
# spec/controllers/monsters_controller_spec.rb
 
describe MonstersController, type: :controller do
  let(:attributes) do
    {
      name: "Dustwing",
      size: "tiny",
      taxonomy: "Abradacus Nonexistus"
    }
  end
 
  it "renders the show template" do
    monster = Monster.create!(attributes)
    get :show, id: monster.id
    expect(response).to render_template(:show)
  end
 
  describe "creation" do
    before { post :create, monster: attributes }
    let(:monster) { Monster.find_by(name: "Dustwing") }
 
    it "creates a new monster" do
      expect(monster).to_not be_nil
    end
 
    it "redirects to the monster's show page" do
      expect(response).to redirect_to(monster_path(monster))
    end
  end
end
```

You can use the `get` and `post` methods (along with `patch` and `delete`) to initiatiate test requests on the controller. A `response` object is available to set expectations on, like with `render_template` and `redirect_to`.

The tests above are great, especially while we're still getting used to how controllers are wired. However, almost these exact tests could be copied for any controller set up according to Rails' RESTish conventions. There's nothing inherently wrong with that, but the redundance, along with the need to test views, inspired the creation of a new type of tests supported by Capybara known
as "Feature Tests".

####FEATURE TESTS
If you were going to write tests for a car's steering wheel, what would you start with?

Here's one idea:

```
When the steering wheel is rotated to the left, the tires rotate to the left.
```

This makes sense, but it's testing much more than the steering wheel. This test relies on the view (steering wheel), the model (tires), and the controller (steering column)!

This is called an **acceptance test**, because it is phrased in terms of features that provide value to the user. (It could also be called an **integration test**, because it tests more than one pieces of the system as a
group.)

Can we *isolate* the steering wheel while still testing its functionality?

Not really--the whole point of the steering wheel is to control the tires. We could talk about how it looks or what it's made of, but the functionality is inherently tied to the underlying system, just like the views in a Rails app. All of those forms and templates are meaningless without controllers and models to populate them.

In the last section, we did our best to isolate the controller, and as a result, we wrote much of our tests in terms of the controller's internal parts (such as redirects and request methods). We don't care what the HTML looks like, or what button the user pressed, or how the models are behaving.

This is called a **unit test**, because it tests a single unit of functionality.

For a car, it might look like this:

```
When the steering column's flange rotates, the steering shaft transmits the rotation to the steering box.
```

Phew. Good thing they covered steering assemblies on the last episode of Car Talk, or I wouldn't have known where to start!

By now you're probably realizing that if you say "steering" enough times, it stops sounding like a real word.

But, more importantly, it can be difficult to write isolated **unit tests**, and it's not always clear whether they're useful: now that we have this test full of
automotive jargon, compare it to this test, which covers the steering wheel (view) *and* the steering column (controller):

```
When the steering wheel is rotated to the left, the steering column transmits the rotation to the steering box.
```

This is a **feature test**.

The **acceptance test** at the top of this section covers too much ground, making it brittle and difficult to maintain.

The **unit test** in the middle of this section is so specific that it almost feels like we just rewrote the controller code, but phrased differently.

The **feature test**, on the other hand, is Just Right. It lets us think like a user (in terms of the steering wheel, or view) while still making intelligent assertions about how the underlying system responds to input (in terms of the steering column, or controller).

Now, on to the "how".

####CAPYBARA
When you see key words like `visit`, `fill_in`, and `page`, you know you're looking at a [Capybara](https://github.com/jnicklas/capybara) test.

To set up Capybara, one must first install the gem:

```ruby
gem 'capybara'
```

Then setup Capybara-Rails integration in `spec/rails_helper.rb`:

```ruby
require 'capybara/rails'
```

Then setup Capybara-RSpec integration in `spec/spec_helper.rb`:

```ruby
require 'capybara/rspec'
```

Feature tests are traditionally located in `spec/features`, but you can put them anywhere if you pass the `:type => :feature` option to your `describe` call.

To test our monster manager with Capybara, we'll start by setting up a GET request, then use Capybara's convenient helper functions to interact with the page just like a user would:

```ruby
# spec/features/monster_creation.rb
 
describe "monster creation", type: :feature do
  before do
    visit new_monster_path
    fill_in "Name", with: "Dustwing"
    select "tiny", from: "monster_size"
    fill_in "Taxonomy", with: "Abradacus Nonexistus"
    click_button "Create Monster"
  end
```

When `click_button` is called, this will trigger the POST request to the controller's `create` action, just as if a user clicked it in their browser.

Now, we can write our original controller tests like usual:

```ruby
  let(:monster) { Monster.find_by(name: "Dustwing") }
 
  it "creates a monster" do
    expect(monster).to_not be_nil
  end
 
  it "redirects to the new monster's page" do
    expect(current_path).to eq(monster_path(monster))
  end
```

And because we're in Capybara land, we also have a very convenient way of making assertions about the final GET request:

```ruby
  it "displays the monster's name" do
    within "h1" do
      expect(page).to have_content(monster.name)
    end
  end
```

`within` sets the context for our next expectation, restricting it to the first `<h1>` tag encountered on the page. This way, our `expect` call will only pass if the specified content (`"Dustwing"`) appears inside that header.

One interesting thing about this approach is that we're being much less explicit about certain expectations. For example, we're testing the redirect not by examining the initial `302` response. Instead, we're relying on Capybara's virtual "browser session" and examining the current path. This is much more powerful and intuitive, and it doesn't sacrifice much in the way of expressivity.

####SUMMARY
The hardest part about testing usually ends up being the "why" and not the "how". Why write the test this way, and not that way?

You will probably see Capybara feature tests in wider usage than direct controller and view tests, but they're not universal: if you're doing something unusual, like a series of complex redirects that change based on authorization level, it makes sense to write a more isolated controller test. But for standard CRUD functionality, Capybara is designed to save you a lot of time and mental effort.

These can serve as fairly reliable guidelines:

+ Models should always be thoroughly unit tested.

+ Controllers should be as thin as possible to keep your feature tests simple.

+ If you can't avoid making a controller complex, it deserves its own isolated test.

+ Capybara's syntax is much more powerful than Rails' builtin functionality for view tests, so stick with it whenever possible.

+ Don't get carried away with the details when testing views: you just need to make sure the information is in the right place. If your tests are too strict, it will be impossible to make even simple tweaks to your templates without breaking the build.
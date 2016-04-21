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
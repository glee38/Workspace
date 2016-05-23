#Rails Authentication Series

####HEY, IS THAT YOU?

As I continue to venture deeper into the magical land of Rails, I thought it would be a good idea to write about the different methods of authentication available for developers. The concept of authentication itself is not very difficult (figuring out if a user is really who they say they are), but I've found that proper set up and configuration is crucial to laying the foundation for great authentication flow. 

I am, of course, no expert, but here are some of the options that I have learned for implementing authentication thus far:

1. Roll your own authentication logic (not the best idea - I will explain why)
2. Use Omniauth (make it someone else's problem)
3. Devise (a Rails engine that "pimps" your authentication ride)
4. Devise & Omniauth together

In the proceeding posts, I will go over all four methods of authentication. At the end of this series, I hope that both you and I will have developed a firm grasp on Rails authentication and can build an authentication system that will have every hacker wondering why they even tried.

#AuthSeries Pt. 1: Authentication Basics

Welcome to **Part 1** of AuthSeries, a series dedicated to authentication in Rails. WOO! In today's post, I will go over a few programming concepts that are crucial in understanding authentication as a whole.

####Our goal today:
1. Learn about cookies
2. Learn about sessions

###Cookies

Imagine going into Facebook. You type `www.facebook.com` into your browser and are prompted to login. You do so happily, as you cannot wait to procrastinate for a few hours by accidentally creeping on people and reading a myriad of mundane posts.

Facebook logs you in. You are redirected to your homepage, where you see some breaking news - John's dating WHO?! You click on the post in your newsfeed only to be prompted to login again. Ugh. Again? You quickly login again because your nosiness just *needs* to know. Facebook takes you to the post. You read it, tell yourself Megan is basic anyway so who cares, and decide you want to check out some other posts. You click on the link to your newsfeed and there it is again. A prompt to login.

My goodness, you just wanted to stalk people in peace, and now this? What the http is going on?!

**WHAT IS A COOKIE?**

Cookies are used to maintain some state between the client (your browser, e.g., Google Chrome) and the origin server (where you are sending your requests to be fulfilled - in our example's case, Facebook). 

**WHY THEY ARE USEFUL**

In the scenario above, every time you click on a different link or part of the Facebook website, you are sending a new request to Facebook and therefore, being redirected to login every time. This is because HTTP requests are built to be a "stateless protocol". This means that the server processes each request separately from one another - the "state" of the first request where you logged in, is only longer maintained when you request to go to another page. 

Thus, every time you ask your browser to load a different page in Facebook, Facebook will ask you to login again because it *doesn't* remember the fact that you already logged in during a previous request.

This is where cookies come in. To solve this problem, cookies are created and stored in your browser when you visit a website that uses cookies to maintain state. The cookie is essentially a small text file that contains information about you as a user, such as your user id, what ads you click on, and more. Cookies provide a way for a website to verify who a user is once, and maintain that state for the rest of your visit on that site, or until you logout. 

Ah, finally. Now you can creep happily ever after.

**99 PROBLEMS, AND A COOKIE IS ONE**

As useful as cookies are, they also pose a huge security concern. Because they are stored as plain text files in a user's browser, it is frighteningly easy to change or manipulate the content of a cookie. 

For example, let's say you visit Facebook again, login, and are prompted to your homepage. Your browser at this point has a cookie stored containing information about your Facebook login, which was used to authenticate you as a user. Yay! You no longer have to sign in every time you request a different page. 

But what if you changed your user id in your cookie to something else? You click on the developer's console in your browser, find the Facebook cookie, change the user id, and voila! Suddenly, you find yourself logged in as the user with the newly changed user id. You sly fox, you. But wait - if you can hack into someone else's account this easily, doesn't this mean that the same could potentially happen to you? Uh oh.

###Sessions

Enter sessions, a gift to all techkind. Unlike cookies, which are client-side (your browser) files that contain user information, sessions are server-side (such as Facebook) files that contain user information. Because sessions are stored server-side, a user can no longer easily manipulate their content like they could a cookie. 

Let's break down how they work and why sessions allow maintaining state to be more secure.

**WHAT ARE SESSIONS?**

We've discussed above that HTTP is a stateless protocol, with cookies providing a way to maintain some sort of state (such as the user being logged in, the contents of a user's shopping cart, etc.) between the client and the server. A session is essentially a type of cookie in that it also behaves like a hash, storing user pertinent information in a key referred to as a "session id". The session id usually contains a 32-character string, which is used to identify the hash.

The first time you log in to Facebook, a session id is created on the server side. This session id is used to identify and authenticate you during the time you are browsing their site. Thus, your "logged in" state will be maintained with Facebook until you either logout, or until the session expires (some websites have a timeframe in which a session will expire on its own if you have not explicitly logged out), whichever comes first.

**WHY THEY ARE USEFUL**

![facebook-login](http://i64.tinypic.com/2432a92.jpg)

Now when you send a request to Facebook to go to another page or look at a different post, every cookie sent to Facebook's server will include your unique session id with which to authenticate you. Facebook will then send back the contents of your request for your browser to render, and will send back your session id with it. This exchange allows a state to be maintained between the client and server without having any revealing information passed through your cookies. Your session id variables can only be seen on the server side.

Let's use a real life example to illustrate what is going on.

**GIMME THAT SHACKSTACK**

After hours of successfully procrastinating on Facebook, you realize that all this hard work has made you pretty darn hungry. There's no food in the fridge, so you decide to go out to the nearest Shake Shack. 

![login_sessions](http://i67.tinypic.com/2vuwqq9.jpg)

This Shake Shack is so poppin' that they provide every customer a buzzer and receipt number that is used to identify you when your food is ready. You stand on line, order, pay with your credit card, sign the receipt, and the employee hands you a buzzer and an order number. Your number is 33.

After 180 seconds of hunger-induced torture, your buzzer vibrates. Your heart skips a beat and you bring your buzzer and receipt up to the counter. An employee takes a look at the number on your receipt and matches it up to the receipt attached the glorious brown bag containing your order. You don't need to show your credit card, or sign the receipt with your signature again to verify that you are indeed the proud owner of the food baby to be. That was already validated when you first paid for your order, and the ticket number is all you need to prove who you are. You take your food and blast Hall & Oates "You make my dreams come true" on your way home. Well done.

![login_sessions_2](http://i63.tinypic.com/108h7rp.jpg)

In the example illustrated above, paying and signing your receipt is like logging in with your username and password. The receipt is like the session id that is created by Facebook when you first log in, and that receipt is what is used to identity you for the remainder of your visit. You never have to show your credit card or sign the receipt again - the number on the receipt (like a session id) is all you need for a back and forth exchange between yourself (the client) and the employee (the server). This way, you don't run the risk of identity theft by exposing your credit card numbers multiple times (like a cookie being passed back and forth between requests), and you can access your food with the number issued by Shake Shack.

**CONCLUSION**

As you can see, cookies and sessions are a critical part in maintaining state and allowing for great user experience during your interaction with an application. In the next post, we will discuss how we can use these newly learned concepts to roll our own authentication logic using Rails.

# AuthSeries Pt. 2: Creating Your Own Authentication

Welcome to **Part 2** of AuthSeries, a series dedicated to authentication in Rails. In today's post, I will go over how to go about creating your own authentication logic from scratch. Rails has a lot of useful gems that get this job done for you, but I think it's always useful to understand what is going on under the hood before using gems that abstract away these basic, but very important principles.

#### OUR GOAL TODAY:

1. Learn how to build our own authentication logic inside of the:
	+ User model and controller
	+ Sessions controller
	+ Application controller
2. Allow users to sign up and login through forms
3. Use the `has_secure_password` method
4. Write validations
5. Write helper methods:
	+ current_user
	+ authorize!
	+ logged_in?

#### STEP 1: GENERATE USER RESOURCE

Let's say we are building a "Recipes" website where users can sign up, login, create, edit and view their own recipes. 

[image of recipes index page]

In order to keep track of our users and store their recipes in the database, we will need to create sign up and login forms on your website. But before we do that, let's begin by generating our User model and migrating it to the database.

**NOTE**: This is assuming that you have already generated your rails application and are cd'd into your application's folder. 

In terminal, begin by typing in the following:

```
rails g resource User email:string password_digest:string
```

It is important to take note of the `password_digest` column, as it is the column that will utilize the `has_secure_password` feature in Rails that will be an essential part of our authentication logic.

Running the above command  will generate the `User` model and `UsersController`.

#### STEP 2: `has_secure_password`

Now, go inside of your User model and add `has_secure_password` like so:

```ruby
class User < ActiveRecord::Base
has_many :recipes
has_secure_password

# more code to come 

end
```

The `has_secure_password` will do the following:

1. Require a `password_digest` attribute (which we added previously in our Users table)

2. Add methods to set and authenticate against a BCrypt password (a gem we will install later to help encrypt passwords)

3. Automatically add  validations:
	+ Password must be present on creation
	+ Password length should be less than or equal to 72 characters
	+ Password confirmation (using the password_confirmation attribute)

Just by typing in one little line, Rails automagically generated validations and methods to help us in our authentication journey. Rails, you da best.

#### STEP 3: `BCrypt`

To utilize `has_secure_password`, uncomment this line in your `Gemfile`:

```ruby
# Use ActiveModel has_secure_password
gem 'bcrypt', '~> 3.1.7'
```

Then, run `bundle install` in your terminal. The `bcrypt` gem will hash a user's password and stores it securely in your database.

#### STEP 4: ADD VALIDATIONS

Now that we have successfully set up our params, let's go back inside the User model. Here, you can add validations for the attributes of your choice. This is utilized mostly in the 'Sign Up' portion of our application.

For example:

```ruby
class User < ActiveRecord::Base
has_many :recipes
has_secure_password

validates_uniqueness_of :email
validates_presence_of :email
# ... etc.

end
```

You do not need to add validations for the `password` attribute since they are automatically added by `has_secure_password`. Woo!

#### STEP 5: STRONG PARAMS

If Rails terms were people, this one would be wearing sunglasses. Besides sounding undeniably cool, strong parameters are crucial in that they give you the ability to sanitize params passed in through user forms.

What does this mean exactly?

In your `UsersController`, type in the following:

```ruby
class UsersController < ApplicationController

# we will add actions here later

private # create private method user_params

def user_params
	params.require(:user).permit(:email, :password, 
	:password_confirmation)
end
```

Let's break down the above code:

1. In the `params` hash, require the `user` key

2. Allow only `email`, `password` and `password_confirmation` attributes to be passed in by the user. (The permitted attributes are not necessarily required - just allowed.)

What exactly will this look like when a user submits a sign up form? The value of `params[:user]` upon form submission should look similar to the following:

```
{ "email" => "grace@test.com", "password" => "test",
 "password_confirmation" => "test }
```

This way, users cannot assign attributes to fields that you do not want them to have access to (such as the option to select whether or not they are an admin). Strong parameters are so important in fact, that Rails will raise an exception if a form is submitted and the model params are not found.

The strong params plugin not only sanitizes your params, but it allows for flexibility in mass assignment. 

For example:

```ruby
def user_params
  if current_user.admin?
    params.require(:user).permit(:email, :password, 
    :password_confirmation, :is_admin)
  else
    params.require(:user).permit(:email, :password, 
    :password_confirmation)
  end
end
```

**NOTE**: If you have googled around about how to set up authentication in Rails, you may have read about `attr_accessible`. This method has been deprecated in Rails 4 in favor of strong params as the latter is more flexible in mass assignment. 

#### STEP 6: `UsersController`

Next, we will write some actions in our `UsersController` to handle signing up a user.

In `UsersController`, type in the following:

```ruby
class UsersController < ApplicationController
  def new
    @user = User.new
  end
  
private
  def user_params
	params.require(:user).permit(:email, :password, 
	:password_confirmation)
  end

end
```

Here, we are setting up an instance variable of `@user` that our view will be able to access later on in order to build our user sign up form. 

Next, let's build our `create` action:

```ruby
class UsersController < ApplicationController
  def new
    @user = User.new
  end

  def create
    @user = User.new(user_params)
    if @user.save
      session[:user_id] = @user.id
      flash[:notice] = "Thank you for signing up!"
      redirect_to root_path
    else
      render :new
    end
  end
  
  private
	def user_params
		params.require(:user).permit(:email, :password, 
		:password_confirmation)
	end
end
```

Let's break down what is happening on within our `create` method:

1. Set the `@user` variable to a new `User` instance with our strong parameters, `user_params`, to mass assign attributes

2. If `@user` saves successfully (meaning all of our validations have returned true), create a `session[:user_id]` equal to the current user's id, `@user.id`. This automatically logs the user in upon signing up so they do not have to login again.
	+ **NOTE**: Every Rails app comes equipped with a session for each user. Read more about how Rails sessions work under the hood [here](http://guides.rubyonrails.org/action_controller_overview.html#session).

3. Redirect to `root_path` and display a flash notice thanking the user for signing up. 

4. If `@user` does not save successfully due to validation errors, re-render the sign up page with error messages.

####STEP 7: `ROUTES`

Now that we have successfully set up our `User` actions, let's write out some routes to make sure our actions actually have paths to which they can route to. 

In `config/routes.rb`, type in the following:

```ruby
Rails.application.routes.draw do
  resources :users
  resources :recipes
end
```

The `resources` keyword will create restful routes for the specified controllers. To view these routes, type in `rake routes` or `rake route | grep users` in terminal. 

####STEP 8: `VIEWS`

It is finally time to move on to our views! 

In `views/users/new`, type in the following:

```ruby
<h1>Sign Up</h1>

<%= form_for @user do |f| %>
  <% if @user.errors.any? %>
    <div class = "error_messages">
		<h2>Form is invalid</h2>
		<ul>
		  <% @user.errors.full_messages.each do |message| %>
		    <li><%= message %></li>
		  <% end %>
		</ul>
    </div>
  <% end %>
  
  <div class = "field" >
    <%= f.label :email %><br>
    <%= f.text_field :email %><br>
  </div>
  
  <div class = "field" >
    <%= f.label :password %><br>
    <%= f.text_field : password %><br>
  </div>

  <div class = "field" >
    <%= f.label : password_confirmation %><br>
    <%= f.text_field :password_confirmation %><br>
  </div>
  
  <%= f.submit%>
<% end %>
```

You can, of course, customize your form in whatever way you'd like. Another option is to write your form in a partial, such as `_form.html.erb` and simply render the partial within your `new.html.erb` view.

We're *almost* there! Next, let's create a link to our sing up page within our application layout.

In `app/views/layouts/application.html.erb`, type in the following line wherever you'd like (I personally like to put this in the navigation bar):

```ruby
<%= link_to "Sign Up", new_users_path %>
```

Run `rails s` in your terminal to start up the rails server on `http://localhost:3000/` and you should see something similar to:

[drawing of recipes index page with sign up link]

[drawing of login form]

[example of login form with error]

[example of login form filled in]

[recipes index page with success message]

At this point, we have successfully managed to create our user and log them in! We. Are. Awesome. 

But wait - we still have no way to log ourselves out, or log back in! Let's build that next.

####STEP : `SessionsController`

In `SessionsController`, type in the following:

```ruby
class SessionsController < ApplicationController
  
  def new
    @user = User.new
  end

  def create
    @user = User.find(params[:user][:email])
    if @user
      session[:user_id] = @user.id
      flash[:notice] = "Login successful!"
      redirect_to user_path(@user)
    else
      flash[:notice] = "User does not exist."
      redirect_to new_users_path
    end
  end

  def destroy
    if session[:user_id]
      reset_session
    end
    redirect_to '/'
  end
  
end
```

As usual, let's break down what is going on:

1. When someone logs in, use their email to search for an existing user with that email address in the database.

2. If a user is found, create a `session[:user_id]` equal to `@user.id` and redirect the user to their show page.

3. If a user with that email address is not found, redirect the user to the sign up page and show an error message.



```ruby
Rails.application.routes.draw do
  resources :users
  resources :recipes

  root 'recipes#index'
  get '/' => 'recipes#index'

  get '/signin' => 'sessions#new'
  post '/signin' => 'sessions#create'

  delete '/signout' => 'sessions#destroy'

end
```

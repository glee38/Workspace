#Authentication
##Cookies And Sessions
####OBJECTIVES
1. Describe a cookie.
2. Explain the data flow problem cookies solve.
3. Find all the cookies on a page.
4. Explain what a session is, as far as Rails is concerned.
5. Describe where session data is stored.
6. Write data to the Rails session and access it later in the app.

####OVERVIEW
Cookies are a way for an HTTP server to ask the user's browser to store a little bit of data for it, and then get that data back from the browser later.

They are fundamental to the operation of nearly every contemporary website.

Primarily, cookies are used for log in. They provide a way for us to verify who a user is once, and then remember it for their entire session. Without cookies, you would have to provide your username and password on every single request to the server.

Cookies may also be used to store other information about a user, such as what's in their shopping cart, or what ads you've shown them during their visit.

In this document, we'll cover what cookies are, how they fit into the HTTP response flow, and how you can access them within your Rails application.

####SHOPPING
Let's say we want to build an e-commerce site. Users can come to the site, add some items to their shopping cart, and then pay for all of them at the end. Simple.

Where does the user's shopping cart live?

We could create a `Cart` model, which `has_and_belongs_to_many` `Item`s. But that leaves a fundamental problem: how do we know what cart to load when we get a request? When one user requests to see their cart, how can I tell them apart from another user, and make sure they're seeing the right cart?

The flow looks like this:

+ When a user adds something to their cart, their browser will make a `POST` request to `/cart`.

+ Later, to see the content of their cart, they'll send a `GET` request to `/cart`.

Remember what's included in an HTTP request:

+ an [HTTP verb](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html), like `GET`, `PUT`, or `POST`
+ a path
+ various headers

HTTP servers are typically stateless. They receive requests, process them, return data, then forget about them.

For example, `GET` requests usually encode this information in the path. When you write a route matching `/items/:item_id`, you are telling Rails to pull the value `id` from the request path and save it in `params[:id]`. In your `items_controller`, you'll probably have a method that looks something like:

```ruby
def show
  @item = Item.find(params[:item_id])
end
```

Which loads the row for that item from the database and returns it as an ActiveRecord model object, which your `show.html.erb` then renders.

If we want to be able to retrieve the current cart, we need to have its id somewhere in the HTTP request. Specifically, it must be in the path or the headers.

It would be possible, though quite convoluted, to store this information in the path. This would have strange effects: since the path is shown in the browser's URL bar, a user who copies a URL and sends it to a friend ("check out this neat skirt!") would also be copying their shopping cart ID. Upon loading the page, the friend would see what's in the user's cart. Since a cart is owned by a particular user, and may contain private information, this is probably not what we want.

Cookies allow us to store this information in the only other place available to us: HTTP headers.

####WHAT'S A COOKIE, ANYWAY?
Let's see what the [spec](http://tools.ietf.org/html/rfc6265) has to say:

```
This section outlines a way for an origin server to send state information to a user agent and for the user agent to return the state information to the origin server.
 
To store state, the origin server includes a Set-Cookie header in an HTTP response. In subsequent requests, the user agent returns a Cookie request header to the origin server. The Cookie header contains cookies the user agent received in previous Set-Cookie headers. The origin server is free to ignore the Cookie header or use its contents for an application-defined purpose.
```

The description is quite technical, so let's look at their example:

```
  == Server -> User Agent ==
  Set-Cookie: SID=31d4d96e407aad42
 
  == User Agent -> Server ==
  Cookie: SID=31d4d96e407aad42
```

In this example, the server is an HTTP server, and the User Agent is a browser. The server responds to a request with the `Set-Cookie` header. This header sets the value of the `SID` cookie to `31d4d96e407aad42`.

Next, when the user visits another page on the same server, the browser sends the cookie back to the server, including the `Cookie: SID=31d4d96e407aad42` header in its request.

Cookies are stored in the browser. The browser doesn't care about what's in the cookies you set. It just stores the data and sends it along on future requests to your server. You can think of them as a hash—and indeed, as we'll see later, Rails exposes cookies with a method that behaves much like a hash.

####USING COOKIES
So how would we use a cookie to store a reference to the user's shopping cart? Let's say that we create a cart the first time a user adds something to their cart. Then, in the response, we might include the header,

```
  == Server -> User Agent ==
  Set-Cookie: cart_id=273
```

Only with the `cart.id` of the cart we just saved.

When the user comes back to our site, their browser will include the cookie in their reply:

```
  == User Agent -> Server ==
  Cookie: cart_id=273
```

We can look at this HTTP header, get the `cart_id` from it, and look it up using the `ActiveRecord` find method we know and love.

####SECURITY CONCERNS
Cookies are stored as plain text in a user's browser. Therefore, the user can see what's in them, and they can set them to anything they want.

If you open the developer console in your browser, you can see the cookies set by the current site. In Chrome's console, you can find this under `Resources > Cookies`. You can delete any cookie you like. For example, if you delete your `user_session` cookie on `github.com`, you will find that you've been logged out.

You can also edit cookies, for example with [this extension](https://chrome.google.com/webstore/detail/editthiscookie/fngmhnnpilhplaeedifhccceomclgfbg?hl=en).

This presents a problem for us. If users can edit their `cart_id` cookie, then they can see other users' shopping carts.

####RAILS TO THE RESCUE
Fortunately, Rails has a solution to this. When you set cookies in Rails, you usually don't manipulate the HTTP headers directly. Instead, you use the `session` method. The `session` method is available anywhere in the Rails response cycle, and it behaves like a hash:

```ruby
  # set cart_id
  session[:cart_id] = @cart.id
 
  # load the cart referenced in the session
  @cart = session[:cart_id]
```

You can store any simple Ruby object in the session. In fact, we don't need a `Cart` model at all—we can just store a list of items in the session!

Rails manages all session data in a single cookie, named `_YOUR_RAILS_APP_NAME_session`. It *serializes* all the key/value pairs you set with `session`, converting them from a Ruby object into a big string. Whenever you set a `key` with the `session` method, Rails updates the value of its session cookie to this big string.

When you set cookies this way, Rails signs them to prevent users from tampering with them. Your Rails server has a key, configured in `config/secrets.yml`.

```
development:
  secret_key_base: kaleisgreat  # probably not the most secure key ever
```

Somewhere else, Rails has a method, let's call it `sign`, which takes a `message` and a `key` and returns a `signature`, which is just a string:

```ruby
# sign(message: string, key: string) -> signature: string
def sign(message, key):
  # cryptographic magic here
  return signature
end
```

It's guaranteed that given the same message and key, sign will produce output. Also, without the key, it is practically impossible to know what sign would return for a given message. That is, signatures can't be forged.

Rails creates a signature for every cookie it sets, and appends the signature to the cookie.

When it receives a cookie, Rails verifies that the signature matches the content (that is, that `sign(cookie_body, secret_key_base) == cookie_signature`).

This prevents cookie tampering. If a user tries to edit their cookie and change the `cart_id`, the signature won't match, and Rails will silently ignore the cookie and set a new one.

Cryptography is a deep rabbit hole. At this point, you don't need to worry about the specifics of how cryptography works, just that Rails and other frameworks use it to ensure that session data which is set on the server can't be edited by users.

####TYING IT TOGETHER
In our `items_controller.rb`, we might have an `add_to_cart` method, which is called when the user adds something to their cart. It might work something like this:

```ruby
# Routed from POST /items/:id/add_to_cart
def add_to_cart
  # Get the item from the path
  @item = Item.find(params[:id])
 
  # Load the cart from the session, or create a new empty cart.
  cart = session[:cart] || []
  cart << @item.id
 
  # Save the cart in the session.
  session[:cart] = cart
end
That's it! It's common to wrap this up in a helper method:
class ApplicationController < ActionController::Base
  def current_cart
    session[:cart] ||= []
  end
end
```

So now our controller looks like this:

```ruby
# Routed from POST /items/:id/add_to_cart
def add_to_cart
  # Get the item from the path
  @item = Item.find(params[:id])
 
  # Load the cart from the session, or create a new empty cart.
  current_cart << @item.id
end
```

This way, we can use `current_cart` in our views and layouts too. For example, we may want to show the user how many items are in their cart as part of the layout.

####CONCLUSION
Cookies are foundational for the modern web.

Most sites use cookies, either to let their users log in, to keep track of their shopping carts, or record other ephemeral session data. Almost nobody thinks these are bad uses of cookies: nobody really believes that you should have to type in your username and password on every page, or that your shopping cart should clear if you reload the page.

But cookies just let you store data in a user's browser, so by nature, they can be used for more controversial endeavors.

For example, Google AdWords sets a cookie and uses that cookie to track what ads you've seen and which ones you've clicked on. The tracking information helps AdWords decide what ads to show you.

This is why, if you click on an ad, you may find that the ad follows you around the Internet. It turns out that this behavior is as effective as it is annoying: people are far more likely to buy things from ads that they've clicked on once.

This use of cookies worries people and the EU [has created legislation around the use of cookies](https://en.wikipedia.org/wiki/HTTP_cookie#EU_cookie_directive).

Cookies, like any technology, are a tool. In the rest of this unit, we're going to be using them to let users log in. Whether you later want to use them in such a way that the EU passes another law is up to you.

##Login Required
####OVERVIEW
Sometimes you want to require that a user is logged in to access a route. Here's how.

####OBJECTIVES
1. Restrict a route to logged in users.
2. Skip a filter for particular controller actions.

####FIRST PASS: MANUAL CHECKS
Let's say we have a `DocumentsController`. Its `show` method looks like this:

```ruby
  def show
    @document = Document.find(params[:id])
  end
```

Now let's add a new requirement: documents should only be shown to users when they're logged in. From a technical perspetive, what does it actually mean for a user to log in? As we saw in the last section, when a user logs in all we are doing is using cookies to add their `:user_id` to their session.

The first thing you might do is to just add some code into `DocumentsController#show`:

```ruby
  def show
    return head(:forbidden) unless session.include? :user_id
    @document = Document.find(params[:id])
  end
```

The first line is a return guard. Unless the session includes `:user_id`, we return an error. `head(:forbidden)` is a controller method that returns the specified HTTP status code—in this case, if a user isn't logged in, we return `403 Forbidden`.

We're using the phrase `unless session.include?` rather than `unless session[:user_id]`, because a user_id of 0 is still a valid user id, but would evaluate falsey, and reject the request.

####REFACTOR
This code works fine, so you use it in a few places. Now your DocumentsController looks like this:

```ruby
  class DocumentsController < ApplicationController
    def show
      return head(:forbidden) unless session.include? :user_id
      @document = Document.find(params[:id])
    end
 
    def index
      return head(:forbidden) unless session.include? :user_id
    end
 
    def create
      return head(:forbidden) unless session.include? :user_id
      @document = Document.create(author_id: user_id)
    end
 
    def update
      return head(:forbidden) unless session.include? :user_id
      @document = Document.find(params[:id])
      # code to update a document
    end    
  end
```

That doesn't look so DRY. I really wish there were a way to ask Rails to run a check before any controller action.

Fortunately, Rails gives us a solution: [`before_action`](http://guides.rubyonrails.org/action_controller_overview.html#filters). We can refactor our code like so:

```ruby
  class DocumentsController < ApplicationController
    before_action :require_login
 
    def show
      @document = Document.find(params[:id])
    end
 
    def index
    end
 
    def create
      @document = Document.create(author_id: user_id)
    end
 
    private
 
    def require_login
      return head(:forbidden) unless session.include? :user_id    
    end
  end
```

Let's look at the code we've added:

```ruby
  before_action :require_login
```

This is a call to the ActionController class method `before_action`. `before_action` registers a filter. A filter is a method which runs before, after, or around, a controller's action. In this case, the filter runs before all DocumentsController's actions, and kicks requests out with `403 Forbidden` unless they're logged in.

####SKIPPING FILTERS FOR CERTAIN ACTIONS
What if we wanted to let anyone see a list of documents, but keep the `before_action` filter for other `DocumentsController` methods? We could do this:

```ruby
  class DocumentsController < ApplicationController
    before_action :require_login
    skip_before_action :require_login, only: [:index]
 
    # ...
  end
```

This class method,

```ruby
  skip_before_action :require_login, only: [:index]
```

Tells Rails to skip the `require_login` filter only on the `index` action.

##Sessions Controller
####OVERVIEW
We've covered how cookies can be used to store data in a user's browser.

One of the most common uses of cookies is for login. In this lesson, we'll cover how to use the Rails session to log users in.

####OBJECTIVES
1. Describe where session data is stored.
2. Create a `Sessions` controller in Rails.
3. Log a user in with it.

####HOW LOGIN WORKS ON THE WEB
Nearly every website in the world uses what I am going to call the "wristband" pattern. A lot of nightclubs use this pattern as well.

You arrive at the club. The bouncer checks your ID. They put a wristband on your wrist (or stamp your hand). They let you into the club.

If you leave and come back, the bouncer doesn't look at your ID, they just look for your wristband. If you buy a drink, the bartender doesn't need to see your ID, since your wristband proves you're old enough to buy alcohol.

You arrive at gmail.com. You submit your username and password. Google's servers check to see if your credentials are correct. If they are, Google's servers issue a cookie to your browser. If you visit another page on gmail, or anywhere on google.com for that matter, your browser will show the cookie to the server. The server verifies this cookie, and lets you load your inbox.

####HOW THIS LOOKS IN RAILS
Let's look at what the simplest possible login system might look like in Rails.

The flow will look like this:

+ The user GETs `/login`
+ The user enters their username. There is no password.
+ The user submits the form, POSTing to `/login`.
+ In the create action of the `SessionsController` we set a cookie on the users browser by writing their username into the session hash.
+ Thereafter, the user is logged in. `session[:username]` will hold their username.

Let's write a `SessionsController` to handle these routes. This controller has two actions, `new` and `create`, which we'll map in `routes.rb` to `get` and `post` on `/login`.

Typically, your `new` method would look up a user in the database, verify their login credentials, and then store the authenticated user's id in the session.

We're not going to do any of that right now. Our sessions controller is just going to trust that you are who you say you are.

```ruby
    class SessionsController < ApplicationController
      def new
        # nothing to do here!
      end
 
      def create
        session[:username] = params[:username]
    redirect '/'
      end
    end
```

There's no way for the server to log you out right now. To log yourself out, you'll have to delete the cookie from your browser.

We'll make a very small login form for `new.html.erb`,

```html
    <form method='post'>
      <input name='username'>
      <input type='submit' value='login'>
    </form>
```

Ordinarily, we would use `form_for @user`, but in this example, we don't have a user model at all!
When the user submits the form, they'll be logged in!

####LOGGING OUT
The log out flow is even simpler. We add a `SessionsController#destroy` method, which will clear the username out of the session.

```ruby
    def destroy
      session.delete :username
    end
```

The most common way to route this action is to `post '/logout'`. This means that our logout link will actually be a submit button that we style to look like a link.

It's tempting, but don't attach this to a `get` route. HTTP specifies that `get` routes don't change anything—logging out is definitely changing something. You don't actually want someone to be able to put a link to `http://www.yoursite.com/logout` in an email or message board post. It's not a security flaw, but it's pretty annoying to be logged out because of mailing list hijinks.

####CONCLUSION
At its base, login is very simple: the user provides you with credentials in a POST, you verify those credentials and set a token in the `session`. In this example, our token was literally the username the user typed. In a more complex app, it would most likely be their user id.

####RESOURCES
+ [Rails Tutorial Chapter 8 — Log in, log out](https://www.railstutorial.org/book/log_in_log_out)

##Using has_secure Password
####OVERVIEW
It's quite difficult to manage passwords securely. About once a month, there is another big hack in the news, and all the passwords and credit cards from some poor site shows up on bittorrent.

Rails provides us with tools to store passwords relatively securely, so that when hackers break into your servers, they don't gain access to users' actual passwords.

####OBJECTIVES
1. Explain why, it's a bad idea to store passwords in plaintext.
2. Write code to store and verify hashed, salted passwords.
3. Use Rails' `has_secure_password` to store and authenticate users login credentials securely.

####THE PROBLEM WITH PASSWORDS
Let's imagine a `SessionsController#create` method that does very simple authentication. It goes like this:

```ruby
   def create
     @user = User.find(username: params[:username])
     return head(:forbidden) unless params[:password] == @user.password
     session[:user_id] = @user.id
   end
```

We load the user row, check to see if the provided password is equal to the password stored in the database, and if it is, we set `user_id` in the `session`.

This is tremendously insecure.

The reason this is tremendously insecure is that you have to store all your users' passwords in the database, unencrypted.

Never do this.

Even if you don't care about the security of your site, people have a strong tendency to reuse passwords. That means that the inevitable security breach of your site will leak passwords which some users also use for Gmail. Your `users` table probably has an `email` column. This means that if I'm a hacker, getting access to your database has given me the Internet equivalent of the house keys and home address for some (probably surprisingly large) percentage of your users.

####HASHING PASSWORDS
So how do we store passwords if we can't store passwords?

We store their hashes. A *hash* is a number computed by feeding a string to a *hash function*. Hash functions have the property that they will always produce the same number given the same input. You could write one yourself. Here's one that I just made:

```ruby
   # dumb_hash(input: string) -> number
   def dumb_hash(input)
     input.bytes.reduce(:+)
   end
```

My `dumb_hash` function just finds the sum of the bytes that comprise the string. It is a hash function, since it satisfies the criteria that the same string always produces the same result.

We could imagine using this function to avoid storing passwords in the database. Our `User` model and `SessionsController` might look like this:

```ruby
   # app/models/user.rb
   class User < ActiveRecord::Base
     def password=(new_password)
       self.password_digest = dumb_hash(new_password)
     end
 
     def authenticate(password)
       return nil unless dumb_hash(password) == password_digest
       self
     end
 
     private
 
     def dumb_hash(input)
       input.bytes.reduce(:+)
     end
   end
 
   # app/controllers/sessions_controller.rb
   class SessionsController < ApplicationController
     def create
       user = User.find_by(username: params[:username])
       authenticated = user.try(:authenticate, params[:password])
       return head(:forbidden) unless authenticated
       @user = user
       session[:user_id] = @user.id
     end
   end
```

Note [try](http://api.rubyonrails.org/classes/Object.html#method-i-try) is an ActiveSupport method. `object.try(:some_method)` means
`if object != nil then object.some_method else nil end`.

In this world, we have saved the passwords' hashes in the database, in the `password_digest` column. We are not storing the passwords themselves.

You can set a user's password by saying `user.password = *new_password*`. Presumably, our `UsersController` would do this, but we're not worrying about it for the moment.

`dumb_hash` is, as its name suggests, a pretty dumb hash function to use for this purpose. It's a poor choice because similar strings hash to similar values. If my password was 'Joshua', you could log in as me by entering the password 'Jnshub'. Since 'n' is one less than 'o' and 'b' is one more than 'a', the output of `dumb_hash` would be the same.

This is known as a *collision*. Collisions are inevitable when you're writing a hash function, since hash functions usually produce either a 32-bit or 64-bit number, and the space of all possible strings is much larger than either `2**32` or `2**64`.

Fortunately, smart people who have thought about this a lot have written a lot of different hash functions which are well-suited to different purposes. And nearly all hash functions are designed with the quality that strings which are similar but not the same hash to significantly different values.

Ruby internally uses [MurmurHash](https://en.wikipedia.org/wiki/MurmurHash), which produces better results for this:

```
> 'Joshua'.hash
 => -3766180385262328513
 
> 'Jnshub'.hash
 => 827642026211689321
```

But Murmur still isn't ideal, because while it does not produce collisions so readily, it is still not difficult to produce them if that's what you're trying to do.

Instead, Rails uses BCrypt. BCrypt is designed with these properties in mind:

1. BCrypt hashes similar strings to very different values.

2. It is a *cryptographic hash*. That means that if you have an output in mind, finding a string which produces that output is designed to be "very difficult". "Very difficult" means "even if Google put all their computers on it, they couldn't do it".

3. BCrypt is designed to be slow—it is intentionally computationally expensive.

The last two features make BCrypt a particularly good choice for passwords. (2) means that even if an attacker gets your database of hashed passwords, it is not easy for them to turn a hash back into its original string. (3) means that even if an attacker has a dictionary of common passwords to check against, it will still take them a considerable amount of time to check your password dictionary against that list.

####SALT
But what if our attackers have done their homework?

Say I'm a hacker. I know I'm going to break into a bunch of sites and get their password databases. I want to make that worth my while.

Before I do all this breaking and entering, I'm going to find the ten million most common passwords and hash them with BCrypt. If I can do around 1,000 hashes per second, so that's about three hours. Maybe I'll do the top five hundred million just to be sure.

It doesn't really matter that this is going to take long time to run—I'm just doing this once. Let's call this mapping of strings to hash outputs a "rainbow table".

Now, when I get your database, I just look and see if any of the passwords there are in my [rainbow table](https://en.wikipedia.org/wiki/Rainbow_table). If they are, then I know the password.

The solution to this problem is *salting* our passwords. A salt is a random string appended to the password before hashing it. It's stored in plain text next to the password, so it's not a secret. But the fact that it's there makes an attacker's life much more difficult: it's very unlikely that I constructed my rainbow table with your particular salt in mind, so I'm back to running the hash algorithm over and over as I guess passwords. And remember, Bcrypt is designed to be expensive to run.

So let's update our `User` model to use bcrypt:

```ruby
    # Gemfile:
    gem 'bcrypt'
 
    # app/models/user.rb
    class User < ActiveRecord::Base
      def password=(new_password)
        salt = BCrypt::Engine::generate_salt
        hashed = BCrypt::Engine::hash_secret(new_password, salt)
        self.password_digest = salt + hashed
      end
 
      # authenticate(password: string) -> User?
      def authenticate(password)
        # Salts generated by generate_salt are always 29 chars long.
        salt = password_digest[0..28]
        hashed = BCrypt::Engine::hash_secret(password, salt)
        return nil unless (salt + hashed) == self.password_digest
      end
    end
```

Our `users.password_digest` column actually stores two values: the salt, and the actual return value of BCrypt. We just concat them together in the column, and use our knowledge of the length of salts—generate_salt always produces 29 character strings—to separate them.

After we've loaded the User, we find the salt which we previously stored in their `password_digest` column. We run the password we were given in `params` through BCrypt, along with the salt we read from the database. If the results match, you're in. If they don't, no dice.

####RAILS MAKES IT EASIER
You don't have to deal with all this yourself. Rails provides a method called `has_secure_password` which you can use on your ActiveRecord models to handle all this. It looks like this:

```ruby
    class User < ActiveRecord::Base
      has_secure_password
    end
```

You'll need to add 'bcrypt' to your Gemfile if it isn't already.

[`has_secure_password`](http://api.rubyonrails.org/classes/ActiveModel/SecurePassword/ClassMethods.html) adds two fields to your model: `password` and `password_confirmation`. These fields don't correspond to database columns! Instead, the method expects there to be a `password_digest` column defined in your migrations.

`has_secure_password` also adds some `before_save` hooks to your model. These compare `password` and `password_confirmation`. If they match (or if `password_confirmation` is nil), then it updates the `password_digest` column pretty much exactly like our example code before did.

These fields are designed to make it easy to include a password confirmation box when creating or updating a user. All together, our very app might look like this:

```html
    # app/views/user/new.html.erb
    <%= form_for :user, url: '/users' do |f| %>
      Username: <%= f.text_field :username %>
      Password: <%= f.password_field :password %>
      Password Confirmation: <%= f.password_field :password_confirmation %>
      <%= f.submit "Submit" %>
    <% end %>
```

```ruby 
    # app/controllers/users_controller.rb
    class UsersController < ApplicationController
      def create
        user = User.new(user_params).save
      end
 
      private
 
      def user_params
        params.require(:user).permit(:name, :email, :password, :password_confirmation)
      end
    end
```

```ruby 
    # app/controllers/sessions_controller.rb
    class SessionsController < ApplicationController
      def create
        @user = User.find(username: params[:username])
        return head(:forbidden) unless @user.authenticate(params[:password])
        session[:user_id] = @user.id
      end
    end
```

```ruby 
    # app/models/user.rb
    class User < ActiveRecord::Base
      has_secure_password
    end
```

##Omniauth
####OBJECTIVES
1. Describe the problem of authentication and how Omniauth solves it.
2. Explain an Omniauth strategy.
3. Describe the problem OAuth solves, and how it solves it.
4. Use Omniauth to provide OAuth authentication in a Rails server.

####OVERVIEW
Passwords are terrible.

For one thing, you have to remember them. Or you have to use a password manager, which comes with its own problems. Unsurprisingly, some percentage of users will just leave and never come back the moment you ask them to create an account.

And then on the server, you have to manage all these passwords. You have to store them securely. Rails secures your passwords when they are stored in your database, but it does not secure your servers, which see the password in plain text. If I can get into your servers, I can edit your Rails code and have it send all your users' passwords to me as they submit them. You'll also have to handle password changes, email verification, and password recovery. Inevitably, your users accounts will get broken into. This may or may not be your fault, but when they write to you, it will be your problem.

What if it could be someone else's problem?

Like Google, for example. They are dealing with all these problems somehow (having a huge amount of money helps). For example, when you log into Google, they are looking at vastly more than your username and password. Google considers where you are in the world (they can [guess based on your IP address](https://en.wikipedia.org/wiki/Geolocation), the operating system you're running (their servers can tell because they [listen very carefully to your computer's accent when it talks to them][IP_stack_fingerprinting]), and numerous other factors. If the login looks suspicious—like you usually log in on a Mac in New York, but today you're logging in on a Windows XP machine in Thailand—they may reject it, or ask you to solve a [captcha](https://en.wikipedia.org/wiki/CAPTCHA).

Wouldn't it be nice if your users could use their Google—or Twitter, or Facebook—login for your site?

Of course, you know this is possible. I'm sure you've seen sites that let you log in with Facebook. Today, we're going to talk about how you can enable such a feature for your site.

####OMNIAUTH
[Omniauth](https://github.com/intridea/omniauth) is a gem for Rails that lets you use multiple authentication providers on your site. You can let people log in with Twitter, Facebook, Google, or with a username and password.

Here's how it works from the user's standpoint:

1. I try to access a page which requires me to be logged in. I am redirected to the login screen.

2. It offers me the options of creating an account, or logging in with Google or Twitter.

3. I click "login with Google". This momentarily sends me to `$your_site/auth/google`, which quickly redirects to the Google signin page.

4. If I'm not signed in to Google, I sign in. More likely, I am already signed in to Google (because Gmail), so Google asks me if they should let `$your_site` know who I am. I say yes.

5. I am (hopefully briefly) redirected to `$your_site/auth/google/callback`, and from there, to the page I wanted.

Let's see how this works in practice:

####OMNIAUTH WITH FACEBOOK
The Omniauth gem allows us to use the oauth protocol with a number of different providers. All we need to do is add the gem specific to the provider we want to use in addition to the omniauth gem, in this case add `omniauth` and `omniauth-facebook` to your Gemfile and `bundle`. We can add as many additional omniauth gems if you want multiple provider login in our app.

First we'll need to tell omniauth about our app's oauth credentials.

Create `config/initializers/omniauth.rb`. It will contain this:

```ruby
    Rails.application.config.middleware.use OmniAuth::Builder do
      provider :facebook, ENV['FACEBOOK_KEY'], ENV['FACEBOOK_SECRET']
    end
```

The ENV constant refers to a global hash for your entire computer environment. You can store any key value pairs in this environment and so it's a very useful place to store credentials that we don't want to be managed by git and later stored on github (if your repo is public). The most common error we see from students here is that when ENV["PROVIDER_KEY"] is evaluated in the initializer it returns nil! Then later when you try and authenticate with the provider you'll get some kind of 4xx error because the provider doesn't recognize your app.

To recieve these credentials, each provider's process is different, but you'll essentially need to register your app with the provider and they'll give you a set of keys specific to your app.

For Facebook:
Log in to the [Facebook developer's panel](https://developers.facebook.com/). Create an app, copy the key (it's called "App ID" on Facebook's page) and the secret and set them as environment variables in the terminal:

```
export FACEBOOK_KEY=<your_key>
export FACEBOOK_SECRET=<your_key>
```

We've included a [quick video](https://youtu.be/1yryyKB7Edk) as this often trips people up (including experienced folks!). Make sure you do the last two steps of setting your URL and valid domains. If you don't Facebook will think you're making a request from an invalid site and will never let the user login.

Running these commands will make these key value pairs appear in the ENV hash in ruby in that terminal. A more lasting way to do this is using the Figaro or Dotenv gems.

Jump into the console to check that you have set the keys properly. If `ENV["FACEBOOK_KEY"]` and `ENV["FACEBOOK_SECRET"]` return your keys you're all set!

We now need to create a link that will take the user to Facebook to login. Create a link anywhere you'd like that sends the user to "/auth/facebook". We'll need a route, a controller and a view, I'll only show the view.

```html
  #\views\static\home.html.erb
  <%= link_to("login with facebook!", "/auth/facebook") %>
```

**Hot-Tip**
Log out of Facebook before you do this portion so you can see the full flow.

Let's visit this page in the browser and click on the link.

Clicking on the link clearly sends a GET request to your server to "/auth/facebook", but in the browser we end up at "https://www.facebook.com/login.php?skip_api_login=1&api_key=1688265381390456&signed_next=1&next=https%3A%2F%2Fwww.facebook.com%2Fv2.5%2Fdialog%2Foauth%3Fredirect_uri%3Dhttp%253A%252F%252Flocalhost%253A3000%252Fauth%252Ffacebook%252Fcallback%26state%3Dc7e7feeea98f875e7a77d76f7385ea2960db3dc23a397c4b%26scope%3Demail%26response_type%3Dcode%26client_id%3D1688265381390456%26ret%3Dlogin&cancel_url=http%3A%2F%2Flocalhost%3A3000%2Fauth%2Ffacebook%2Fcallback%3Ferror%3Daccess_denied%26error_code%3D200%26error_description%3DPermissions%2Berror%26error_reason%3Duser_denied%26state%3Dc7e7feeea98f875e7a77d76f7385ea2960db3dc23a397c4b%23_%3D_&display=page"

This URL has a whole bunch of parameters all URL [encoded](http://ascii.cl/url-encoding.htm) (which is why they look so strange). At this point we are at Facebook's site because somewhere in our app omniauth sent the browser a redirect to that url (which it intelligently autogenerated for us!).

Once we're at Facebook and the user logs in, Facebook will send the browser ANOTHER redirect with the URL omniauth told it about in the previous URL. Omniauth always wants Facebook to redirect us back to our server to the route "/auth/whatever_provider/callback". Along with that request they'll send a whole bunch of information for us!

The URL in your browser should looks something like this mess!
"
http://localhost:3000/auth/facebook/callback?code=AQA_CrhVYnuufhQid-3vS1NvI5rZfk4uPJwFZIymA90JeUR7NDFFy0bHQjbtneLkymqqZlmFbjcg2A0y5zRmaCy0D7k9H46F3j9pm9slzBIN9fM4Q54zAdiVZo2k6XtiMPZ_AG2xEZ8MyiTtbbQOBdaK57PY7lr7iLuFeaVUCUnZC69ddzcq_tLILEkjagSyWXi8WGGshbnIwy9C6d98hnoxl6AJjIi4TC3FScEAxKQ9vH1tXntQ9YvTLNWlWsWUcbefEq1RlywNi3IqGsLnDgyyRcHph0u4-TpnaqZPxHSNdcWCgnYfHK_bSO-R_a3H4Oo&state=60fb843af784e411ea7b5f809e34dd29d5e4eda891d0c4c1#=
"

You should now see a routing error
`No route matches [GET] "/auth/facebook/callback"`

Let's add something to handle that redirect

```ruby
#routes.rb
get '/auth/facebook/callback' => 'sessions#create'
#note the controller and action you use don't matter, but to be semantic we #should use the sessions controller because we're going to log the user in by creating a session.
```

Now we create a `SessionsController`. Our goal here is to either create a new user or find the user in our database and log them in. Facebook sends us a bunch of information back and omniauth parses it for us and puts it in the request environment `request.env['omniauth.auth']`. We can use the information in here to login the user or create them.

Here's a sample of the auth hash Facebook sends us

```ruby
{
  :provider => 'facebook',
  :uid => '1234567',
  :info => {
    :email => 'joe@bloggs.com',
    :name => 'Joe Bloggs',
    :first_name => 'Joe',
    :last_name => 'Bloggs',
    :image => 'http://graph.facebook.com/1234567/picture?type=square',
    :urls => { :Facebook => 'http://www.facebook.com/jbloggs' },
    :location => 'Palo Alto, California',
    :verified => true
  },
  :credentials => {
    :token => 'ABCDEF...', # OAuth 2.0 access_token, which you may wish to store
    :expires_at => 1321747205, # when the access token expires (it always will)
    :expires => true # this will always be true
  },
  :extra => {
    :raw_info => {
      :id => '1234567',
      :name => 'Joe Bloggs',
      :first_name => 'Joe',
      :last_name => 'Bloggs',
      :link => 'http://www.facebook.com/jbloggs',
      :username => 'jbloggs',
      :location => { :id => '123456789', :name => 'Palo Alto, California' },
      :gender => 'male',
      :email => 'joe@bloggs.com',
      :timezone => -8,
      :locale => 'en_US',
      :verified => true,
      :updated_time => '2011-11-11T06:21:03+0000'
    }
  }
}
```

Let's log the user in! (We've omitted the model related code)

```ruby
#app/controllers/sessions_controller
class SessionsController < ApplicationController
 
  def create
    user = User.find_or_create_by(:uid => auth['uid']) do |u|
      u.name = auth['info']['name']
      u.email = auth['info']['email']
    end
    session[:user_id] = user.id
  end
 
  def auth
    request.env['omniauth.auth']
  end
 
end
```

That completes the whole oauth login flow!

####CONCLUSION
Implementing the oauth protocol yourself is extremely complicated. Using the omniauth gem along with the omniauth-provider gem for the provider you'd like to allow users to log in to your site with makes the process a lot easier, but it still trips a lot of people up! Make sure you understand each piece of the flow, what you expect to happen, and any deviance from the expected result. The end result should be getting access to the users data from the provider in your sessions controller where you can decide what to do with it, which is usually either creating a user in your database using their provider data, and/or logging them in.

##Authentication And Authorization With Gems
####AUTHENTICATION VS AUTHORIZATION
Two concepts that are related, often confused, but important to distinguish are authentication and authorization.

*Authentication* is determining who someone is. When I take money out of an ATM, the bank authenticates me with using two authentication factors: my PIN, and the secret key stored on the chip in my ATM card. Before I get on a plane, the TSA (the airline security organization in the U.S.) authenticates me by asking for my ID and using the extremely advanced facial recognition hardware embedded in human brains to verify that the ID matches my face. Usernames and passwords and credentials are all authentication things.

*Authorization* is determining if someone can do a particular thing. The bouncer at the club authenticates me by looking at my ID, and then authorizes me to enter by looking at the list (though I am more typically asked to leave the premises).

####AUTHENTICATION
In the first half of this unit we learned how to roll our own authentication scheme.

Most security professionals will tell you NEVER to roll your own authentication logic as we did in the first half of the unit. This is because as a young (or even experienced programmer) it's unlikely you are a match for the myriad of tricks hackers are going to use to try and hack into your site. Forget to salt your passwords? You're open to rainbow table attacks. Use the wrong hashing algorithm? You're open to brute force attacks. Leave security to those who are experts at it!

There are two solutions to this problem. One, which we've already learned is letting someone else take care of logging users in! Rather than devoting your scarce resources to solving a solved problem, use the omniauth gem and let one of the internet giants like Facebook deal with authentication by leveraging the oauth protocol. If Facebook gets hacked, you probably wouldn't have been safer implementing your own authentication scheme...

Along the same lines as this solution, is leveraging the Rails communities open source nature and using a battle tested gem to implement authentication. In the same way using the omniauth gem can help you avoid implementing the oauth specification correctly yourself, there are a host of gems the Rails community has built over the years to help you avoid implementing authentication and authorization yourself.

One of the gems everybody loves almost as much as they hate it is Devise. It's without a doubt the leader in the authentication space, and has over 3,000 commits in it's 7 year history. It's heavily metaprogrammed, modularized and has a solution to just about any need your authentication scheme might require. Our biggest complaint with devise is that it often obscures what's going on due to its metaprogramming, which makes it hard to change if it's configuration options don't account for EXACTLY your use case. The great thing about Devise is it also offers some authorization options as well.

Other gems worth looking at in this space are [Warden](https://github.com/hassox/warden) (which devise is based around) and [Authlogic](https://github.com/binarylogic/authlogic)

####AUTHORIZATION
Hey, can I delete this file?

It's a surprisingly loaded question. Did I create the file? Do I own it? What does it mean to own a file? Can I write to the file? If I can write to a thing, can I delete it?

These are questions of authorization.

Authorization deals with WHAT a user is allowed to do, once we know WHO that user is. Can this particular user delete a post? Can they view posts written by other users? Authorization helps you answer these questions.

Although Devise doesn't have out of the box support for authorization, it's common to implement "roles" using Devise. Roles allow us to segment our users into types or kinds of users. For example, admins, teachers, and students. By leveraging ActiveRecord's enum feature, you can define a user as having a specific role. If you have simple enough authorization requirements this might be enough. However, if your roles get more complicated you might want to bring in another gem to do the heavy lifting [Rolify](https://github.com/RolifyCommunity/rolify).

You can pair the concept of roles with other gems that allow you to specify what "abilities" or "policies" users or types of users. For example we could say that admins have the "ability" to read, write and delete any post.
[CanCanCan](https://github.com/CanCanCommunity/cancancan) and [Pundit](https://github.com/elabs/pundit) are the leaders in this space. The original gem CanCan (what can? a user do) was written by the creator of RailsCasts if you are a fan!

##Devise
####LEARNING OBJECTIVES
1. Describe the major architecture and modules of Devise.
2. Build a working login system using Devise.

####OVERVIEW
[Devise](https://github.com/plataformatec/devise) is a gem for when you have a lot of authentication needs.

Want to send confirmation emails, lock user accounts after a certain number of failed login attempts, and send password resets? Devise can do that.

Want to allow multiple models to be signed in, each with different roles, granting different permissions? Devise can handle that too.

Devise has an extensive collection of generators. It includes standard views and controllers. It will generate templates that include Bootstrap for you.

It will also be, at times, a giant pain, because no magic is without a price. Devise abstracts away a lot of implementation details. That can be very nice, but it can also make it challenging to setup and debug when things don't go exactly to plan.

####ARCHITECTURE
Devise is a Rails [engine](http://guides.rubyonrails.org/engines.html). That means it's basically a Rails app that sits inside your Rails app. It has its own views and controllers, and defines its own routes.

It does not define models for you, but it does have generators that make the process of creating a Devise-compliant User model very easy.

Devise is made up of modules. Modules are applied to your `User` model, so you should read these as abilities that User accounts can have.

```
   * Database Authenticatable: encrypts and stores a password in the database to validate the authenticity of a user while signing in. The authentication can be done both through POST requests or HTTP Basic Authentication.
   * Omniauthable: adds OmniAuth (https://github.com/intridea/omniauth) support.
   * Confirmable: sends emails with confirmation instructions and verifies whether an account is already confirmed during sign in.
   * Recoverable: resets the user password and sends reset instructions.
   * Registerable: handles signing up users through a registration process, also allowing them to edit and destroy their account.
   * Rememberable: manages generating and clearing a token for remembering the user from a saved cookie.
   * Trackable: tracks sign in count, timestamps and IP address.
   * Timeoutable: expires sessions that have not been active in a specified period of time.
   * Validatable: provides validations of email and password. It's optional and can be customized, so you're able to define your own validations.
   * Lockable: locks an account after a specified number of failed sign-in attempts. Can unlock via email or after a specified time period.
```

That's from the [Devise docs](https://github.com/plataformatec/devise).

####CONFIGURATION
Several of these modules come added for you by default if you run

```
rails generate devise User
```

This generates a `User` model which includes this helpful bit at the top:

```
# Include default devise modules. Others available are:
# :confirmable, :lockable, :timeoutable and :omniauthable
devise :database_authenticatable, :registerable,
       :recoverable, :rememberable, :trackable, :validatable,
```

That's Devise wiring itself into the model. It also creates a migration for all the fields it needs.

Let's look at the highlights.

####DATABASE_AUTHENTICABLE
This adds a `valid_password?(password)` method to the model. The password is stored securely in the database.

####REGISTERABLE
Registerable gives you `User.new_with_session(params, session)`, which lets you initialize a `User` from session data (like a token from Facebook) in addition to params.

####RECOVERABLE
Recoverable gives you password resets, like so:

```
# resets the user password and save the record, true if valid passwords are given, otherwise false
User.find(1).reset_password('password123', 'password123')
 
# only resets the user password, without saving the record
user = User.find(1)
user.reset_password('password123', 'password123')
 
# creates a new token and sends it with instructions about how to reset the password
# (this one requires a mailer.)
User.find(1).send_reset_password_instructions
```

####REMEMBERABLE
This lets you remember a user and associate them with a `User` object in the database without them having to log in. It works by storing a token in cookies.

```ruby
User.find(1).remember_me!  # regenerating the token
User.find(1).forget_me!    # clearing the token
```

####TRACKABLE
Track information about your user sign in. It tracks the following columns:

+ `sign_in_count` - Increased every time a sign in is made (by form, openid, oauth)

+ `current_sign_in_at` - A timestamp updated when the user signs in

+ `last_sign_in_at` - Holds the timestamp of the previous sign in

+ `current_sign_in_ip` - The remote ip updated when the user sign in

+ `last_sign_in_ip` - Holds the remote ip of the previous sign in

####VALIDATABLE
The documentation on this is quite clear:

````
Validatable creates all needed validations for a user email and password. It's optional, given you may want to create the validations by yourself. Automatically validate if the email is present, unique and its format is valid. Also tests presence of password, confirmation and length.
````

####LOCKABLE
Handles blocking a user's access after a certain number of attempts. Lockable accepts two different strategies to unlock a user after it's blocked: email and time. The former will send an email to the user when the lock happens, containing a link to unlock its account. The second will unlock the user automatically after some configured time (ie 2.hours). It's also possible to setup lockable to use both email and time strategies.

####OMNIAUTHABLE
Honestly, this one doesn't give you a whole lot more than omniauth already does. It does set some (but not all!) of the routes for you. That's a nice touch.

####TYPICAL SETUP
Add Devise to your Gemfile:

```ruby
    gem 'devise'
```

Now run the installer:

```
    rails generate devise:install
```

This creates a massive initializer in `config/initializers/devise.rb`. This is probably the single best source of documentation for Devise. You may wish to look through it.

You'll notice that the installer prints a big notice of several things you should do. In particular, we should have a root route.

Create a `WelcomeController` with a `home` action view and route.

Now generate your `User` model with:

```ruby
    rails g devise User
```

Run `rake routes` and `rake db:migrate`. You should see that Devise has added a bunch of routes. Run `rails s` and take a look at one, maybe `/users/sign_in`.

You should now have a working app with sign in.

If you look at the routes you can see that Devise gives us a sign out route as well. Let's implement that.

We probably want the user to be able to click sign out on any page when they're logged in so let's add it to our layout.

```html
#views/layouts/application.html.erb
  ...
  link_to("Sign out", destroy_user_session_path)
  ...
```

Devise will also add messages to the flash when a user signs in or when there's an error. We can add that to the layout as well so that those flash notices appear.

```html
#views/layouts/application.html.erb
  ...
  <%= content_tag(:div, flash[:error], :id => "flash_error") if flash[:error] %>
  <%= content_tag(:div, flash[:notice], :id => "flash_notice") if flash[:notice] %>
  <%= content_tag(:div, flash[:alert], :id => "flash_alert") if flash[:alert] %>
  ...
```

There's no navigation link for a user to get to the sign up page so let's add one of those as well.

```html
#views/layouts/application.html.erb
  ...
  <%= link_to("Sign Up", new_user_registration_path) %>
  ...
```

####CONCLUSION
In a real app you'd probably want to add some css, maybe put these pieces into partials like a header or a nav partial but you can see how quickly and with how little code we were able to get a fully functioning login system. Feel free to play around with the other Devise modules!

##Cancan
####OBJECTIVES
1. Understand how to create an Ability class.
2. Learn how to model permissions in the database.
3. Prevent users from accessing certain actions from the controller.
4. Prevent users from seeing certain pieces of the view.

####OVERVIEW
We have been looking at different modes of authentication. Now, we'll shift our focus, and start dealing with authorization: how do you describe a permissions model, and how do you implement it in Rails?

(Also, in case you were wondering, standard UNIX permissions let you delete a file you don't own and can't write to. You must, however, be able to write to the directory that contains the file.)

####CANCANCAN
[CanCanCan](https://github.com/CanCanCommunity/cancancan) is a gem for Rails that provides a simple but quite flexible way to authorize users to perform actions. It's the continuation of a no longer maintained gem [CanCan](https://github.com/ryanb/cancan).

In your controllers, it looks like this:

```ruby
    def show
      @article = Article.find(params[:id])
      authorize! :read, @article
    end
```

Here we're calling CanCanCan's `authorize!` method to determine if the user can `:read` this `@article`. If they can't, an exception is thrown.

Setting this for every action can be tedious, therefore the `load_and_authorize_resource` method is provided to automatically authorize all actions in a controller. It will use a before filter to load the resource into an instance variable and authorize it for every action.

```ruby
class ArticlesController < ApplicationController
  load_and_authorize_resource
 
  def show
    # @article is already loaded and authorized
  end
end
```

####HANDLING UNAUTHORIZED ACCESS
If the user authorization fails, a CanCan::AccessDenied exception will be raised. You can catch this and modify its behavior in the ApplicationController.

```ruby
class ApplicationController < ActionController::Base
	rescue_from CanCan::AccessDenied do |exception|
	redirect_to root_url, :alert => exception.message
	end
end
```

In your views, it might look like this:

```html
    <% if can? :update, @article %>
      <%= link_to "Edit", edit_article_path(@article) %>
    <% end %>
```
 
Or maybe this:

```html 
   <% if cannot? :update, @article %>
     Editing disabled.
   <% end %>
```

####ABILITIES
To generate the ability file you can run `rails g cancan:ability`

To define the permissions, you fill out the `Ability` class, which goes a bit like this:

```ruby
    class Ability
      include CanCan::Ability
 
      def initialize(user)
        if user.admin?
          # only admins can change things
          can :update, Article
        end
        # but anyone can read them
        can :read, Article
      end
    end
```

Your `Ability` class is passed an instance of your `User` model by calling the `current_user` method. In the initializer, you call `can` or `cannot` to define what this particular user can do.

`can` and `cannot` both take these arguments:

+ the action, written as a symbol 
+ an ActiveRecord class, an instance of which is the target of the action.

You can be more specific if you want to allow or disallow actions based on information in the models. For example,

```ruby
    can :write, Article, owner_id: user.id
```

This will let the user write any article whose `owner_id` is her user id. (That is, any Article she owns).

```ruby
    can :read, Article, :category => { :visible => true }
```

This will let the user `:read` any `Article` whose `category`'s `visible` column is `true`.

CanCanCan doesn't make any assumptions about how you've stored permission information in your user model. It's up to you to add the appropriate fields to support your authorization scheme.

####A SIMPLE SCHEME
Here is a basic CanCanCan Ability class for a message board.

The rules are: anyone can post. Registered users can edit their post after posting (but not delete it). Moderators can do anything to any post.

```ruby
    class Ability
      include CanCan::Ability
 
      def initialize(user)
        can :read, Post
        can :create, Post
        unless user.nil? # guest
          # CanCan accepts a hash of conditions;
          # here, we're saying that the Post's user_id
          # needs to match the requesting User's id
          can :update, Post, { user_id: user.id }
        end
        if user.admin?
          can :manage, Post
        end
      end
    end
```

`:manage` is a special CanCanCan action which means "any action". So users with an admin column set to `true` can do anything to any Post.

####RESOURCES
+ [CanCanCan](https://github.com/CanCanCommunity/cancancan)

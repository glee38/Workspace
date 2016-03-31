#Forms
##Intro to Capybara Tests
####THE MVC FRAMEWORK
There are three basic levels of testing that correspond to the different levels of our application stack in a Model-View-Controller architecture.

![Web Application Stack and Tests](https://dl.dropboxusercontent.com/s/k2ypcn86btb6ajo/2015-09-29%20at%204.14%20PM.png)

**Database**: Databases persist or save data from our application.

**Models**: Models provide an object-oriented abstraction or metaphor for the data in our application. Our models do the job of interacting with the database for us. Our model can talk to the database by asking for certain data and using that data to create a new instance of our class. We can then interact with that data by using the methods and properties of the instance of that class.

**Controllers**: Controllers provide the main interface and application logic. They deal with things like: "What data should I show a user in response to certain input from that user" or "What HTML should be sent to the user when they visit the /about page?". In large scale MVC applications, controllers are represented by classes, but really, lots of `bin` files could be considered controllers.

**Views**: Views present information to the user. Any code that is responsible for presenting data or output to the user, from methods that use a bunch of `puts`, to HTML, to ERB templates, could be considered a View. In web applications, Views are generally represented by ERB templates that generate HTML.

**User/Browser**: The top of our application pyramid is finally the user. Whether describing the people using our application, the interface they use such as a Command Line, Voice, or HTML, or the program they use to even access our application, such as a Browser like Chrome or Safari, or a Native app, our application is responsible for delivering the user an experience on some sort of pre-existing platform.

####BASIC MVC FLOW
Let's take a look at how it all fits together. We'll use an example of a social networking application.

1. The user interacts with our application's view(s), via the command line or a web browser, for example. The user enters some input that asks our application to show him or her a list of all of their friends who are using the application.

2. The controller receives this request and looks at it. The controller says "okay, this user wants to see a list of their friends."

3. The controller asks the `Friends` model––"can you go into the database and bring back all of this user's friends?".

4. The model goes to the database, retrieves all of the requested data, and gives it to the controller.

5. The controller passes that data to a view, and the view displays it to the user.

####THE THREE LEVELS OF TESTING
#####UNIT TESTS
Unit tests test the models in our application and how they interact with our database.

#####CONTROLLER TESTS
Controller tests test that the code responsible for delivering the appropriate data to a user is working properly. In a web app, a controller test is responsible for making sure that an HTTP request returns the expected HTTP response. Controller tests should not test HTML or forms, but rather, that the controller behaved as expected.

#####INTEGRATION TESTS
Integration tests are the highest-level of test, and they are closest to describing how a user will actually interact with our application. Commonly referred to as End-To-End Tests, Integration tests should flex your entire application stack (i.e. all of the MVC components) and rarely isolate components or behaviors. They are perfect for spec'ing high level user interactions with HTML and forms. We're going to be learning how to write and read an integration test using a library called Capybara within an RSpec test suite.

*Note: While you will generally not be required to write tests in Learn, you will be required to read a test and understand what functionality the test suite is anticipating and testing. If you can write a test, we believe you can read a test.*

####INTEGRATION TESTING WITH CAPYBARA
#####WHAT IS CAPYBARA?
Capybara is a library of code that we can include in our application via the capybara gem. The Capybara library allows us to write code that simulates how a user interacts with our app. We can write such code in our integration tests and thus test the functionality of our application.

#####CAPYBARA SETUP
To use Capybara for integration tests in Rails or Sinatra, we need to include it in our testing environment. In our spec/spec_helper.rb file, we add the following code:

```ruby
# Load RSpec and Capybara
require 'rspec'
require 'capybara/rspec'
require 'capybara/dsl'
 
# Configure RSpec
RSpec.configure do |config|
  # Mixin the Capybara functionality into Rspec
  config.include Capybara::DSL
  config.order = 'default'
end
 
# Define the application we're testing
def app
  # Load the application defined in config.ru
  Rack::Builder.parse_file('config.ru').first
end
 
# Configure Capybara to test against the application above.
Capybara.app = app
```

The above code might look long and confusing. All you need to know is that it is configuring `RSpec` and our tests to be able to use all the wonderful methods and interactions Capybara provides.

The most important part of the configuration is the last line where we explicitly tell `Capybara` that the `app` we're testing against is defined in `config.ru`.

####OUR APPLICATION
Consider a simple Web Application that shows the user a form asking for their name and then when they submit the form, the application will greet the user based on the name they inputted.
Our homepage, at "/", will display the form:

![Form](https://dl.dropboxusercontent.com/s/1zocl86jv9qguth/2015-09-29%20at%206.00%20PM%20%281%29.png)

Upon submitting the form, the application sends a `POST` request to the `POST '/greet'` route. Then, the user should see the following page:

![Response](https://dl.dropboxusercontent.com/s/83o4onopkwquuve/2015-09-29%20at%206.01%20PM.png)

Colloquially we could express the tests for this application as follows:

```
When a user visits '/'
  they should see a greeting
  they should see a form with a name field
 
When a user submits the greeting form
  they should fill in the name with "Avi"
  they should click submit
  they should see "Hi Avi, it's nice to meet you!"
```

It is exactly these sort of behaviors, conditions, and expectations that Capybara make very easy to read and test.

####TESTING OUR APPLICATION
Now that our test suite is setup to use Capybara, let's start writing some tests for our application. **All of the code we will review below is already present in this repo. You can fork and clone this repo to take a closer look, or you can rebuild this simple app on your own as we review it together, below.**

**`GET '/'`**

We want our root path to show our greeting form:

![Form](https://dl.dropboxusercontent.com/s/1zocl86jv9qguth/2015-09-29%20at%206.00%20PM%20%281%29.png)

So, we want to write a test that checks that the `Get '/'` route brings the user to that page.

Let's write this test in `spec/application_integration_spec.rb`.

File: `spec/application_integration_spec.rb`

```ruby
require 'spec_helper'
 
describe "GET '/' - Greeting Form" do
  it 'welcomes the user' do
    visit '/'
    expect(page.body).to include("Welcome!")
  end
end
```

Most of that code is actually vanilla RSpec. Capybara provides two new methods, `visit` and `page`.

####CAPYBARA `VISIT`
The `visit` method navigates the browser used during the test to a specific URL. It is equivalent to a user typing a URL into their browser's location bar. The argument it accepts is a string for the URL you want to test. Since we want to test our `'/'` root URL, we say `visit '/'` and Capybara will load that page within our test.

####CAPYBARA `PAGE`
The `page` method in capybara exposes the "session" or "browser" that is conceptually (and literally) being used during the test. The `page` method gives you a `Capybara::Session` object that represents the browser page the user would actually be looking at if they typed in `'/'` (or whatever argument you last passed to `visit`).

As such, `page` responds to a lot of methods that represent actions a user would do on a page, such as `click_link`, `fill_in`, and `body`.

The `page.body` will basically dump out all the HTML in the current page as a string.

Our test is telling Capybara to visit the `/` URL. It then sets the expectation that the body of the page returned should include at least the text `Welcome!` somewhere.

####PASSING THE FIRST TEST
File: `./app.rb`

```ruby
class Application < Sinatra::Base
  get '/' do
    erb :index
  end
end
```

`./app.rb` is our main application file, defining the controller that will power this web application. We create a class `Application` and inherit from `Sinatra::Base` to give this class all the web super-powers needed to transform the ruby class into a Sinatra controller.

Within our `Application` controller, we use the Sinatra DSL to create a `GET` route at the `/` URL. We tell our application that in response to HTTP GET requests to `/`, render the ERB template or HTML as the response.

The line `erb :index` tells the application to render, or deliver to the user's browser, the file in `views/index.erb`. This is a Sinatra provided functionality to render ERB templates located in the `views` directory. Let's look at that view file.

File: `views/index.erb`

```
<h1>Welcome!</h1>
```

Our ERB view `views/index.erb` (Sinatra will automatically look for the `.erb` extension when you call `erb` in the controller), contains the necessary HTML to make our test pass.

The final piece of the puzzle is a `config.ru` file to put everything together and start our Sinatra application. This is the file `shotgun` or `rackup` will read to start your local application server and it's also the file our test suite is using to define our application, remember `Rack::Builder.parse_file('config.ru').first`?

File: `./config.ru`

```ruby
require 'sinatra'
 
require_relative './app'
 
run Application
```

In a basic application like this example, our `config.ru` `require`s the `sinatra` gem. It then `require_relative`s the required file `app.rb` that defines our main `Application` controller. Finally, we `run` our `Application` controller to start our web application.

If we now start our application with `shotgun` and open `http://localhost:9393` in our browser we'll see our welcome message.

![Welcome](https://dl.dropboxusercontent.com/s/bue5icj3yuz6iol/2015-09-29%20at%208.56%20PM.png)

And when we run our test suite, we'll see:

```
$ rspec
 
GET '/' - Greeting Form
  welcomes the user
 
Finished in 0.03438 seconds (files took 0.43261 seconds to load)
1 example, 0 failures
```

####PASSING THE SECOND TEST: ADDING THE GREETING FORM REQUIREMENT
Great, step 1, get a basic test and application working, is done! Let's now add tests for the HTML form that will allow users to provide their name for the greeting. When our tests pass, our form at `/` will look like:

![Form](https://dl.dropboxusercontent.com/s/1zocl86jv9qguth/2015-09-29%20at%206.00%20PM%20%281%29.png)

So let's describe an expectation for that HTML in our test.

Edit: `spec/application_integration_spec.rb`

```ruby
require 'spec_helper'
 
describe "GET '/' - Greeting Form" do
  # Code from previous example
  it 'welcomes the user' do
    visit '/'
    expect(page.body).to include("Welcome!")
  end
 
  # New test
  it 'has a greeting form with a user_name field' do
    visit '/'
 
    expect(page).to have_selector("form")
    expect(page).to have_field(:user_name)
  end
end
```

Our new test has a similar setup to the first test, we need to tell Capybara to visit the page at `'/'`. Once that is done, we can set some expectations against the `page` object that represents the user looking at the homepage in their browser. We can simply assert that the `page` has an HTML selector for `form`, meaning that the page contains an HTML element that matches the `form` tag.

Where does this magic `have_selector` matcher come from? That's right, Capybara has added that to RSpec. Capybara `page` objects respond to methods that relate intimately to HTML and the DOM (Document Object Model) that defines web applications. You can literally ask the `page` object: "hey, do you have HTML that matches the following selector?" Pretty amazing, right?

The second expectation is similar, `expect(page).to have_field(:user_name)`. We're saying that we expect the `page` to have a form field called `user_name`. We get to be even more semantic with the `have_field` matcher that will make sure the HTML in `page` contains a form input with either an `ID` or `name` attribute that matches the argument, in this case `:user_name`.

After editing the integration test and saving it, if we run our tests according to the current code, we'll see:

```
rspec
 
GET '/' - Greeting Form
  welcomes the user
  has a greeting form with a user_name field (FAILED - 1)
 
Failures:
 
  1) GET '/' - Greeting Form has a greeting form with a name field
     Failure/Error: expect(page).to have_selector("form")
       expected #has_selector?("form") to return true, got false
     # ./spec/application_integration_spec.rb:12:in `block (2 levels) in <top (required)>'
 
Finished in 0.03963 seconds (files took 0.42466 seconds to load)
2 examples, 1 failure
 
Failed examples:
 
rspec ./spec/application_integration_spec.rb:9 # GET '/' - Greeting Form has a greeting form with a name field
```

Our first test for welcoming the user still passes, but our new test fails. Let's zoom in on the meaningful part of the failure message.

```
Failure/Error: expect(page).to have_selector("form")
  expected #has_selector?("form") to return true, got false
```

The failure message is telling us that we expected the page to have an HTML form and it doesn't, so it's failing. Let's add our HTML form to our view and make this test pass.

Edit: `views/index.erb`

```html
<h1>Welcome!</h1>
 
<form action="/greet" method="POST">
  <label for="user_name">Name:</label>
 
  <p><input type="text" name="user_name" id="user_name" /></p>
 
  <input type="submit" value="Submit"/>
</form>
```

Don't worry about the specifics of the HTML form, but know that it is building an HTML form that when submitted by the user clicking on the Submit button, will create an HTTP POST request to `/greet`, submitting whatever the user typed into the form text `<input>` field nested in the form that happens to be `name` and `user_name` (I know confusing, but it's `name` attribute is equal to `user_name`).

Run your tests now or reload your browser and you should see the form and your tests passing.

####`POST '/GREET'`

####PASSING OUR THIRD TEST: PROCESSING THE FORM AND GREETING THE USER
The final step of our greeting application is to teach our application how to accept the form that a user submits. We know that we want the outcome to be a custom greeting message based on what they type into the `user_name` field in the form. If you typed in `Avi` and clicked submit, we'd expect the page in our browser to look like:

![Response](https://dl.dropboxusercontent.com/s/83o4onopkwquuve/2015-09-29%20at%206.01%20PM.png)

Let's write those tests.

Add the following to the end of: `spec/application_integration_spec.rb` (under the closing `end` of the first `describe` block).

```ruby
describe "POST '/greet' - User Greeting" do
  it 'greets the user personally based on their user_name in the form' do
    visit '/'
 
    fill_in(:user_name, :with => "Avi")
    click_button "Submit"
 
    expect(page).to have_text("Hi Avi, nice to meet you!")
  end
end
```

This new test is trying to mimic a user visiting our greeting form, filling in their name, clicking the submit button, and what they should see in response. Because of the amazing `RSpec` DSL mixed in with `Capybara`, our test quite literally describe that behavior.

We `visit '/'` to load the form into the `page` object.

Then we use the Capybara method `fill_in` to fill in the input field `user_name` with `Avi`.

Finally, we `click_button "Submit"` to submit the form. That HTML interaction, submitting a form, will trigger a new HTTP request in the Capybara session and `page` object.

Just like when you submit a form, the browser loads a new page and you see new content. When Capybara submits a form, just like when we call `visit`, the `page` object is appropriately updated. `page` no longer contains the original greeting form, but rather, after `click_button`, `page` now has the response to the greeting form.

For the response to submitting the greeting form, we can `expect` the `page` to `have_text` `"Hi Avi, nice to meet you!"`. The user filled in their user_name as "Avi", the resulting greeting should mention that. `have_text` is another really friendly and semantic Capybara matcher for testing HTML text value explicitly.

After adding this test, if we run our test suite, we'll see:

```
GET '/' - Greeting Form
  welcomes the user
  has a greeting form with a user_name field
 
POST '/greet' - User Greeting
  greets the user personally based on their user_name in the form (FAILED - 1)
 
Failures:
 
  1) POST '/greet' - User Greeting greets the user personally based on their user_name in the form
     Failure//Error: expect(page).to have_text("Hi Avi, nice to meet you!")
       expected #has_text?("Hi Avi, nice to meet you!") to return true, got false
     # ./spec//application_integration_spec.rb:24:in `block (2 levels) in <top (required)>'
Finished in 0.05962 seconds (files took 0.42668 seconds to load)
3 examples, 1 failure
Failed examples:
rspec ./spec//application_integration_spec.rb:18 # POST '/greet' - User Greeting greets the user personally based on their user_name in the form
```

Our first two tests relating to `GET '/'` pass, but our new test is failing. Zooming in on the failure:

```
1) POST '/greet' - User Greeting greets the user personally based on their user_name in the form
   Failure/Error: expect(page).to have_text("Hi Avi, nice to meet you!")
     expected #has_text?("Hi Avi, nice to meet you!") to return true, got false
```

When submitting a POST request to `/greet`, if the `user_name` was filled in with `Avi`, we expected the resulting page to contain the text `"Hi Avi, nice to meet you!"`. Unfortunately, at this point, it does not.

To make this pass we need to add two things. Currently, our Sinatra application only responds to a single HTTP request, `GET` requests to `/`. We need to teach it to respond to `POST` requests to `/greet`. Let's do that.

Edit: `./app.rb`

```ruby
class Application < Sinatra::Base
  # Old route from previous tests
  get '/' do
    erb :index
  end
 
  # New route to respond to the form submission
  post '/greet' do
    erb :greet
  end
end
```

Using the Sinatra `post` method, we create a response for requests to `POST '/greet'`. That response should be the HTML contained in the `views/greet.erb` template, just like the HTML response of our first route was contained in `views/index.erb`.

The next step is to build our view in `views/greet.erb`. The point of this view is to use the data the user submitted in the previous form within our HTML to produce a personalized greeting for the user.

File: `views/greet.erb`

```html
<h1>Hi <%= params[:user_name] %>, nice to meet you!</h1>
```

That HTML and ERB will satisfy our test.

If you are unfamiliar with the `params` object and how it relates to form and inputs, that's totally fine. The tl;dr is that all the information the user submitted in the form is available to your code within a hash `params`. `params[:user_name]` returns the text the user put into the form input field `user_name`. `<%= params[:user_name] %>` uses ERB's embedded ruby to dynamically insert the value of `params[:user_name]` into the HTML of the response.

With this code in place, our tests should pass and if we refresh our browser and submit the form it should behave as expected.

That's it, you totally crushed Capybara and Integration testing.

####SUMMARY
We learned about:

1. Integration Tests - Tests that exercise the outermost part of our application code from the perspective of our user, actually interacting with the application via it's interface like a web browser and HTML.
2. Capybara - A Ruby library that works with RSpec to allow you to write extremely powerful, simple, and semantic Integration Tests for Web Applications.
3. `visit`, `page` - Capybara methods for controlling the test user's browser and introspecting on the current state of the page they are looking at.
4. `have_selector`, `have_field`, `have_text` - Capybara matchers for ensuring that the page contains certain matching HTML or text.
5. `fill_in`, `click_button` - Capybara methods for mimicking user activity like filling in form fields or clicking buttons.
6. A simple Sinatra Form Application - We also built a simple Sinatra application that has two routes, a GET and a POST, displaying a form, accepting the form input, and sending a dynamic response.

##Dynamic Routes

####WHY DYNAMIC ROUTES MATTER
How does AirBnB create a separate url for every property it hosts on its site? Would it make sense to hard-code hundreds of thousands of routes (`get '/property1'`, `get '/property2'`, `get '/property2356'`) in the controller to display each rental property? The controller would get messy and long very quickly. Instead, AirBnB (and Twitter, and Facebook, etc) use *dynamic routes* - routes that are created based on attributes within the url of the request. In this code-along we'll learn why dynamic routes are powerful and how to integrate them in to a Sinatra project.

To code along, fork and clone this lab. Run `bundle install` to make sure all of your dependencies are installed. Run `shotgun` to make sure that your application can run. There are tests, so make sure you're running `learn` periodically to make sure you code is behaving as expected.

####STARTER CODE
Open up `app.rb` in your text editor. You'll notice two routes, `get '/hello'` and `get '/hello/:name'`.
The first route is familiar looking to us. It returns the string "hello world" in the browser when we go to the url. This is an example of static routing, which we've seen.

But `get '/hello/:name'` is very different. What's with that `:` in front of `name`? This is an example of a `dynamic route`.

Eventually we are going to need to capture data from the user. We need to know who they want to say hello to. There are a few ways to get this information, and the easiest is built right into the URL. They are already typing that URL into the box at the top of their browser, so let's use it to get a bit more information.

####DYNAMIC ROUTES
In your browser, head to `http://localhost:9393/hello/danny`. Now go to `http://localhost:9393/hello/victoria` and `http://localhost:9393/hello/lyel`. Notice how the content on the page changes depending on what we type as the URL in the browser. This is the beauty of dynamic routing - it allows us to take input straight from the url, instead of through a form. In doing so, we can modify the content of a view at the moment the `get` request is received by the controller.

####HOW DYNAMIC ROUTES WORK:
It's important to note that in Sinatra a route is simply an HTTP method/verb that is paired with a URL-matching pattern. When your Sinatra application receives a request, it will match that route to a specific controller action that matches that URL pattern.

The best way to explain routes is by going through an example. Our application is a medicine application that has an array containing three instances of a Medicine class.

Here's our array:

```ruby
all_the_medicines = [ 
  #<Medicine:0x007fb739b1af88 @id=1, @name="penicillin" @group="antibiotic">, 
  #<Medicine:0x007fb739b1af88 @id=2, @name="advil" @group="anti-inflammatory">, 
  #<Medicine:0x007fb739b1af88 @id=3, @name="benadryl" @group="anti-histamine">
]
```

Our application gets a request `:GET /medicines/1`. What happens here?

The first thing Sinatra does is try to match the request to a specific controller action. The controller action it would match is as follows: `get '/medicines/:id'`. Once the request has been matched to the controller action, it then executes the code inside of the controller action block, as shown below:

```ruby
# medicines_controller.rb
get '/medicines/:id' do
  @medicine = all_the_medicines.select do |medicine|
    medicine.id == params[:id]
  end.first
  erb :'/medicines/show.html'
end
```

Let's run through this specific scenario. The HTTP request verb, `GET` matches the `get` method in our controller. The `/medicines` path in the HTTP request matches the `/medicines` path in our controller method. Finally, the `1`, which is an `id` parameter that's being passed into the path, matches the controller's expectation for an `id` parameter to be passed in place of `:id`.

####URL PARAMS
A URl parameter is a variable whose values are set dynamically in a page's URL, and can be accessed by its corresponding controller action. It's a very similar concept to a dynamic url.

The next thing that happens is that the `all_the_medicines` array is queried for a medicine object that has the `id` of `1`. It seems to match this entry: `#<Medicine:0x007fb739b1af88 @id=1, @name="penicillin" @group="antibiotic">`,. The attributes from this object are assigned to the variable `@medicine`.

Finally, the `@medicine` object is rendered via the `show.html.erb` template inside of the `views/medicines` directory.

Going back to our initial example, if you played around enough with the examples above, you'll notice that whatever name you typed in the url also appeared in the browser, saying hello to that person. How were we able to get the text from the URL to the views?

URL params help us get the text from the URL into the views. That `:name` in the route name is just a symbol that will be filled in with text later. The data is passed from the URL to the controller action through an automatically generated hash called `params`. Don't worry too much how the hash is created. Just know that inside your controller action, you automatically have access to this hash through the variable `params`.

To continue the medicine example, the hash looks something like this:

```ruby
params = {
  :id => 1
}
```

The key of the hash is determined by the symbol in the url (`:id`), and the associated value will be the content in the url provided by the user (`1`). Once inside the controller action, we can access the value from the params hash, just like we would any other hash

```ruby
params[:id]
```

You can receive multiple pieces of data through a dynamic route by separating the content with a forward slash. For example, `get` `'/addnumbers/:number1/:number2'` would give you a params hash with two key-value pairs (`number1` and `number2`).

##HTML Forms and Params
####FORMS, FORMS, FORMS!
Think about how many forms you fill out online every day. Credit card payments, logins, registration forms, and even Google searches are all examples of forms. That's because forms are the most common way for users to pass data to a web application.

Specifically in this code-along, we'll connect HTML forms to a Sinatra application by building a form that takes a user's name and favorite food and returns an interpolated string. For example, if the name were "Sam" whose favorite food is "Green Eggs and Ham," we will get an interpolated string of "My name is Sam, and I love Green Eggs and Ham."

####FORM REVIEW
We're not going to dive too deep into form-making here, but the basics are:

+ HTML forms need a `<form>` opening tag and a `</form>` closing tag.
+ Each form field (text, date, password, etc) has a tag (usually `<input>`) with an attribute `type` denoting the type of form field. For example, a text box field looks like this:

```html
<input type="text">
```

+ A form needs a submit button:

```html
<input type="submit">
```

That's all a basic form needs!

####OUR FIRST FORM

Let's build a form with two input fields: one for your name and one for your favorite food. We'll enclose our `<input>` tags in `<p>` tags so we can give them some textual context.

Put it all together and your HTML form will look like this:

**Copy the code below to `views/food_form.erb`**:

```html
<form>
  <p>Your Name: <input type="text"></p>
  <p>Your Favorite Food: <input type="text"></p>
  <input type="submit">
</form>
```

Now if you run `shotgun` and go to the corresponding view (`localhost:9393/food_form`), you'll see your very basic form.

####CONNECTING THE FORM TO YOUR SINATRA APP

If you try submitting the form, nothing will happen. That's because the form is not yet connected to our Application Controller in `app.rb`. There is nothing telling the form to send the user's data to our application.

In order to connect the form to our application, we need to give it explicit directions on **where** and **how** to send the data from the user. Both of these pieces of data are attributes that we give our `<form>` tag.

```html
<form method="POST" action="/food">
```

+ The `method` attribute tells the form what kind of request should be fired to the server when the submit button is clicked. In general, forms use POST request, because it is 'posting' data to the server.

+ The `action` attribute tells the form what specific route the post request should be sent to. In this case, we're posting to a route called `/food`

Each form field `<input>` also must define a `name` attribute. The `name` attribute of an `<input>` defines how our application will identify each `<input>` data.

**Update the form in `views/food_form.erb` to**:

```html
<form method="POST" action="/food">
  <p>Your Name:<input type="text" name="name"></p>
  <p>Your Favorite Food:<input type="text" name="favorite_food"></p>
  <input type="submit">
</form>
```

Let's see what happens when we submit this form...

![Sinatra Error](http://s3.amazonaws.com/readme-pics/localhost_9393_food.png)

We get a Sinatra error! This is great news. Sinatra errors tell us exactly what we need to do next to make the form work.

####POST ROUTES AND PARAMS
The error message Sinatra gives us is telling us that we don't have a route to receive the data from the HTML form that we created in `food_form.erb`.

If you recall, we gave our form a method attribute of `POST` and an `action` attribute of `"/food"`. Again, this is the **how** and **where** the data goes from this form.

Every form also needs a corresponding route in the controller file (`app.rb`). Instead of a `get` route (which we used to route users to view an html page), we'll set up a post route:

```ruby
post '/food' do
 
end
```

Notice that both of the attributes from the form are covered in this route: The `method` `post` and the `action` `/food`. It's almost like a game of catch - the form is throwing the data to the server, which catches it by having the same receiving address (`/food`) and way of receiving the data (`post`).

All user submitted data will appear in a `params` hash accessible throughout our Sinatra controllers. The `name` attribute of an `<input>` corresponds to a key in the `params` hash for that data.

If you create a text field input with `<input type="text" name="favorite_food">`, whenever the user submits that form, you will be able to access the data entered into the Favorite Foods text box via `params[:favorite_food]`.

This is because we will be passing our data in the form of a hash, where the key will be the name of the data, and the value will be the data itself. In this case, we want our hash (which we call `params`) to look something like this:

```ruby
params = { 
  :name => "Sam",
  :favorite_food => "Green Eggs and Ham"
}
```

So our input names will need to be `name` and `favorite food`

####PARAM I AM
The data from the form comes nicely packaged up in the form of a hash called `params`. Let's set the return value of the post route to be `params.to_s`, and see what our form does now...

```ruby
post '/food' do
    params.to_s
  end
```

When you submit your form, you should now see the contents of `params` displayed as a hash in your browser like this:

```
{"name"=>"Sam", "favorite_food"=>"Green Eggs and Ham"}
```

Great! This means you've been able to successfully get the data from the form in to the controller, and can now manipulate it any way you want.

Let's use the key-value pairs in `params` to return the following phrase, using good-old string interpolation:

```ruby
"My name is #{params[:name]}, and I love #{params[:favorite_food]}"
```

Here's the full post route and action in `app.rb`:

**Add this code to `app.rb`**:

```ruby
post '/food' do
  "My name is #{params[:name]}, and I love #{params[:favorite_food]}"
end
```

Submit the form and see what happens! If you've gotten this far you can successfully connect an HTML form to your Sinatra app, and can use the params hash to use and manipulate data from the user.

##Passing Data Between Views and Contr
####SETUP
Why is passing data back to views from your controller so important? It allows us to make your pages *dynamic* rather than *static* - that is, the data can change depending on the inputs provided by the user. As an example, we'll be creating a "String Reverser" - the user inputs a string ("Hello World") into an HTML form, and is shown the reverse of the string ("dlroW olleH") on the following page.

####STARTER CODE
Let's take a closer look at the starter code.

####VIEWS
+ `reverse.erb` has a form that has one text input tag: `<input type="text" name="string">`. Remember that the name attribute becomes the key for the form data in the `params` hash. The form has a method attribute of `POST`, and an action attribute of `/reverse`.

+ `reversed.erb` which will eventually show the reversed version of the string submitted by the user.

+ `friends.erb`, which we'll be using to talk about iteration in erb.

+ **Note**: The views all have basic CSS styling (linked using the `<link>` tag) that can be found in `style.css` in the `public/stylesheets` folder.

####ROUTES
The controller has three routes:

+ `get '/reverse' do`, which renders the `reverse.erb` page.
+ `post '/reverse' do`, which receives that params hash from the form (but does nothing with it) and renders the `reversed.erb` page.
+ `get '/friends' do`, which renders the `friends.erb` page.

####MANIPULATING PARAMS IN THE CONTROLLER
Let's start by taking a look at our params when we submit the form on the /reverse page. The easiest way to do this is to find the post route to which the form is sending data - in this case `post /reverse do` - and add a `puts params` inside the method:

```ruby
  post '/reverse' do
    puts params
    erb :reversed
  end
```

When we submit the form, the contents of params will output **in the console**. Let's submit "hello friend" to the form and look at `params` in our console:

![Puts Params](https://s3.amazonaws.com/learn-verified/puts-params.png)

To manipulate the string, let's take it out of the params hash, and then call the `.reverse` method on it:

```ruby
  post '/reverse' do
    original_string = params["string"]
    reversed_string = original_string.reverse
    erb :reversed
  end
```

We now have a reversed version of the original string submitted by the user. Great! But it's in our controller, which our user will never see. How do we pass this data back in to a view?

####USING INSTANCE VARIABLES
Instance variables allow us to bypass scope between the various methods in a class. Creating an instance variable in a controller method (route) lets the contents become 'visible' to the erb file to which it renders. Instead of creating a local variable `reversed_string`, change it to an instance variable `@reversed_string`.

```ruby
  post '/reverse' do
    original_string = params["string"]
    @reversed_string = original_string.reverse
    erb :reversed
  end
```

We can now access the contents of `@reversed_string` inside of our view, `reversed.erb`.

**Note**: Instance variables are ONLY passed from the controller method where they are created to the view that is rendered, not between controller methods. For example:

```ruby
  get "/" do
    @user = "Ian"
    erb :index #@user will be defined as "Ian" in the view
  end
 
  get "/profile" do
    erb :profile # @user will be nil here
  end
```

####RENDERING USING ERB TAGS
Right now the content of `reversed.erb` is just plain old vanilla HTML in a `.erb` file. In order to show the content of a Ruby string, we need to use erb tags. As a recap:

+ `<%= contents %>` will display the evaluated expression within the opening and closing.
+ `<% contents %>` will evaluate the contents of the expression, but will not display them.

We know that the contents of `@reversed_string` are available to the erb file, so let's put them together:

```html
<!DOCTYPE html>
<html>
 <head>
  <meta charset="UTF-8">
  <title>title</title>
  <link rel="stylesheet" href="stylesheets/style.css" type="text/css">
 </head>
 <body>
  <h1> Your Reversed String!</h1>
    <h2><%= @reversed_string %></h2>
 </body>
</html>
```

Notice that we've put the erb tag with `@reversed_string` within `<h2>` tags. Just some additional styling!

####ITERATING IN ERB
We have one additional `get` request that we're going to use to practice sending data from the controller to a view. In this case, we want to assign an array (rather than a string) to an instance variable. Let's create an array called `@friends` inside of the `get /friends do` route, and render the `friends.erb` page:

```ruby
  get '/friends' do
    @friends = ["Charlie Chaplin", "Richard Pryor", "Eddie Murphy", "Louis CK", "Jerry Seinfeld"]
    erb :friends
  end
```

In `friends.erb`, we want to show each item in the array inside of its own `<h2>` tag. Unfortunately this won't work:

```html
#BAD EXAMPLE
<h2><%= @friends %></h2>
```

Instead, we'll have to use iteration with the `.each` method to loop through each item in the array and put it in its separate `<h2>` tag. Let's start with the iterator - notice that we're using erb tags that don't display the evaluated expression:

```html
<% @friends.each do |friend| %>
 
<% end %>
```

Next, let's set up what we want to do for each item:

```html
<% @friends.each do |friend| %>
    <h2><%= friend %></h2>
<% end %>
```

This will set up a loop through all items in `@friends` and then place each item in the array in its own `<h2>` tag. The rendered HTML will look like this:

```html
<h2>Charlie Chaplin</h2>
<h2>Richard Pryor</h2>
<h2>Eddie Murphy</h2>
<h2>Louis CK</h2>
<h2>Jerry Seinfeld</h2>
```

You can imagine how powerful iteration in erb is when you have an array of thousands of items that you have to display in your view!

##Layouts and Yield
####LAYOUT
If you look at pretty much every website, you'll notice that there are things that exist across all the site's pages. Typically the navigation bar and the footer content stay the same. There may also be menu options that stay consistent across all pages.

You could copy and paste the HTML and ERB for nav bar and make sure that code is in every single erb file, but that isn't at all DRY.

In order to not repeat ourselves, we can create a single file `layout.erb` that contains all the code we want to exist on every single web page.

Below is the HTML for a website that has a header and links to JavaScript files.

```html
<!doctype html>
<html>
  <head>
    <title>Cats</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css">
    <link rel="stylesheet" href="/css/style.css">
  </head>
  <body>
 
    <div class="container">
      <h1>I love cats</h1>
      <img src="https://s3.amazonaws.com/after-school-assets/cat-typing.gif">
 
 
 
    </div>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/js/bootstrap.min.js"></script>
  </body>
</html>
```

We want every code to have a head tag with bootstrap's CSS and a link to our own CSS file. The body of our site contains the header `I love cats` and a cat gif. At the bottom, we have our jquery links.

Now, let's say we have an `index.erb` with the following code:

```html
<h2>This cat...<h2>
<img src="https://s3.amazonaws.com/after-school-assets/cat.gif">
```

####YIELD
Now that we have our layout written, how can we get the `layout.erb` loaded around the `index.erb`?

This is where the `yield` comes in.

In `layout.erb`, we need to add a `yield` wherever we want the other page content to be loaded:

```html
<!doctype html>
<html>
  <head>
    <title>Cats</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css">
    <link rel="stylesheet" href="/css/style.css">
  </head>
  <body>
 
    <div class="container">
      <h1>I love cats</h1>
      <img src="https://s3.amazonaws.com/after-school-assets/cat-typing.gif">
 
      <%= yield %>
 
 
 
    </div>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/js/bootstrap.min.js"></script>
  </body>
</html>
```

Let's say we have a controller action:

```ruby
get '/' do
  erb: index
end
```

When the above controller action is triggered and the `erb` method is called, it looks to see if there is a view titled `layout.erb`. If that file exists, it loads that content around the desired erb file, in this case `index.erb`.

The resulting HTML will look like this:

```html
<!doctype html>
<html>
  <head>
    <title>Cats</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css">
    <link rel="stylesheet" href="/css/style.css">
  </head>
  <body>
 
    <div class="container">
 
      <h1>I love cats</h1>
      <img src="https://s3.amazonaws.com/after-school-assets/cat-typing.gif">
 
      <h2>This cat...<h2>
      <img src="https://s3.amazonaws.com/after-school-assets/cat.gif">
 
 
    </div>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/js/bootstrap.min.js"></script>
  </body>
</html>
```

##Integrating Models for a Full MVC
####SETUP
We'll use input from a form to create an instance of a model, and then send that instance back to a view to be displayed to the user. As an example, we're going to create a web application that analyzes a block of text from the user - showing the number of words, most common letters, and least common letters to us.

####CREATING A MODEL
We could analyze all of the data from `params[:user_text]` in our application controller, but our route would get messy very quickly. Instead, let's create a new class inside of our `models` directory that will take care of the analysis of the text. In your `models` directory, create a new file called `textanalyzer.rb`.

We're not going to go deeply into creating models in this lesson, as you've covered it in depth in our unit on object oriented programming. Instead, paste the following code in to your `textanalyzer.rb` file:

```ruby
class TextAnalyzer
attr_accessor :text

  def initialize(text)
    @text = text
  end
 
  def count_of_words
    words = @text.split(" ")
    words.count
  end
 
  def count_of_vowels
    @text.downcase.scan(/[aeoui]/).count
  end
 
  def count_of_consonants
    @text.downcase.scan(/[bcdfghjklmnpqrstvwxyz]/).count
  end
 
def most_used_letter
    s1 = @text.downcase.gsub(/[^a-z]/, '') #gets rid of spaces
    arr = s1.split('')
    arr1 = arr.uniq
    arr2 = {}
    arr1.map do |c| 
     arr2[c] =  arr.count(c)
    end
    biggest = { 
      :most_used_letter  => arr2.keys.first,
      :letter_count => arr2.values.first 
    }
    arr2.each do |key, value|
      if value > biggest.values[1]
        biggest = {
          :most_used_letter => "",
          :letter_count => ""
        }
        biggest[:most_used_letter] = key
        biggest[:letter_count] = value
      end
    end
    biggest
  end
 
end
``` 
 
The model above has an initializer which takes in a string `text` and saves it to an instance variable `@text`. This instance variable is then used in the four instance methods, which provide information on the block of text in question. If we wanted to use this class on its own, we could do the following:

```ruby
my_text = TextAnalyzer.new("The rain in Spain stays mainly on the plain.")
my_text.count_of_words #=> 9
my_text.count_of_vowels #=> 13
my_text.count_of_consonants #=> 20
my_text.most_used_letter #=> ["n", 6]
```

In general our models are agnostic about the rest of our application - we could drop this class into a Command Line or Ruby on Rails app and it would function in the exact same way.

####USING A MODEL IN THE CONTROLLER
In order to use the model we've created in our controller we need to connect the two. To do this, we'll use the `require_relative` keyword to bring in the code from the model we've created. At the top of `app.rb`, add `require_relative "models/textanalyzer.rb"`. This now gives us the ability to create new instances of the TextAnalyzer class from within our controller.
Now, let's take the data from `params[user_text]` (in the `post '/' do` route) and feed it into a new instance of the `TextAnalyzer` class:

```ruby
post '/' do
  text_from_user = params[:user_text]
  @analyzed_text = TextAnalyzer.new(text_from_user)
  erb :results
end
```

We can shorten this to:

```ruby
post '/' do
  @analyzed_text = TextAnalyzer.new(params[:user_text])
  erb :results
end
```

We now have the instance of `TextAnalyzer` saved to an instance variable called `@analyzed_text`. This means that we can call it and its methods from the `results.erb` view that is being rendered, using erb tags!

####USING INSTANCE VARIABLES IN ERB
In our `results.erb` file, use erb tags to display the data stored in the `@analyzed_text` variable. Your end result should look something like this:

![text_analysis](https://s3.amazonaws.com/learn-verified/text-analyzer.png)

##Sinatra Nested Forms
####FORMS THAT CREATE MULTIPLE OBJECTS
In web apps, we use forms to create objects. When you fill out a form for a dinner reservation on Open Table, you're creating a reservation object. When you upload a photo to Instagram, you're creating an image object.

Those are examples of using forms to create a single object, but what if you wanted to use a form to create more than one object? This is where nested forms comes in.

Let's say we're the registrar's office at a school and it's the start of the school year. We need to create each student and their class schedule. It would be tedious to go through a process that goes through the steps to first create the student, and then goes through the same step again and again to create each of their courses. Wouldn't it be nice to create the student **and** their courses in one go?

####THE MODELS
To create these two different classes of objects, we need to create two models, `Student` and `Course`.

#####STUDENT CLASS
Our student class, with name and grade attributes will look something like this:

```ruby 
class Student
  attr_reader :name, :grade
 
 STUDENTS = []
 
  def initialize(params)
    @name = params[:name]
    @grade = params[:grade]
    STUDENTS << self
  end
 
  def self.all
    STUDENTS
  end
 
end
```

In this model, we have an attr_reader for `name` and `grade`, and we set the value of those attributes on initialization. We also set up a class method `self.all` which returns an array of all the students

#####COURSE CLASS
Now let's set up the model for the courses each student is taking.

```ruby
class Course
  attr_reader :name, :topic
 
  COURSES = []
 
  def initialize(args)
    @name = args[:name]
    @topic = args[:topic]
    COURSES << self
  end
 
  def self.all
    COURSES
  end
end
```

Here, we have a reader for `name` and `topic` and we set the value of those attributes on initialization. We also have the class method `self.all` to return all the courses.

####CREATING THE FORM
The first thing we need is to create the form. For later use in the controller, we'll call this file `new.erb`.

Before we dive into the HTML, let's think about how we want to structure the data our controller action will receive. Typically, if we were just doing student information, we would expect the `params` hash to look something like this:

```ruby
params = {
  "name" => "Joe",
  "grade" => 9
}
```

But how do we handle a student **and** a course? Both course and student have a `name` attribute. If keys in hashes have to be unique, we can't have `name` twice. We could call our keys `student_name` and `course_name`, but that really isn't best practice. And how would it look with two courses? `course_one_name` and `course_two_name`? Suddenly our keys are getting messy.

Instead, we need to think about restructuring our `params` hash to have nested hashes. We can have one hash for all the student information:

```ruby
params = {
  "student" => {
    "name" => "Joe",
    "grade" => 9,
  }
}
```

Now we have a key `student`, which stores a hash of all the student's information, `name` and `grade`.

How would we create a hash such as this in Ruby? We'd do:

```ruby
my_hash["student"] = {}
my_hash["student"]["name"] = "Joe"
```

Thankfully, ERB provides similar syntax. It handles that first level of nesting, so instead of having to do `my_hash["student"]={}` we can just go straight into student. It assumes the name of your hash is the first key so the resulting erb would be `student["name"]`.

This makes it easy for us to insert a second nested hash for the student's course. Let's go ahead and build out the HTML for this form:

```html
<form action="/student" method="post">
  Student Name: <input type="text" name="student[name]">
  Student Grade: <input type="text" name="student[grade]">
  <input type="submit">
</form>
```

We know this form is going to get submitted via a POST request and processed by a controller action. In this case, we've named the action `/student`. You'll notice the `name` attribute of the `input` tag is set up like `student[name]`. This way, when the form gets submitted, the params sent to the `/students` controller action will be exactly like we designed.

Now, let's think about how we want a student's course to fit in the params hash:

```ruby
params = {
  "student" => {
    "name" => "Joe",
    "grade" => 9,
    "course" => {
      "name" => "US History",
      "topic" => "History"
    }
  }
}
```

In this hash, both the student and the course can have the key `name` because they're in different namespaces.
Let's think about how we'd build this hash using Ruby:

```ruby
my_hash["student"] = {}
my_hash["student]["name"] = "Joe"
myhash["student"]["course"] = {}
myhash["student"]["course"]["name"] => "US History"
myhash["student"]["course"]["course"] => "History"
```

Again, we can use the ERB syntax to set up our form. We can ignore the first level of nesting, the `my_hash` portion, and just dive straight into student and course, turning `my_hash["student"]["course"]["name"] => "US History"` into `["student"]["course"]["name"]`.
Let's go ahead and build out the corresponding HTML for the form:

```html
<form action="/student" method="post">
  Student Name: <input type="text" name="student[name]">
  Student Grade: <input type="text" name="student[grade]">
  Course Name: <input type="text" name="student[course][name]">
  Course Topic: <input type="text" name="student[course][topic]">
  <input type="submit">
</form>
``` 
In this form, the information for the course name is set up as `student[course][name]`, giving us the nested hashes we designed in the `params` hash. We're first accessing the `student` key.

But this leaves us with a much bigger problem. How do we now handle **two** (or more!) courses?

Again, we need to restructure how we want the data coming in the `params` hash. The `courses` key should store an array of nested hashes:

```ruby
params = { 
  "student"=> {
    "name"=>"vic",
    "grade"=>"12",
    "courses"=> [
      {
        "name"=> "ap us history", 
        "topic"=>"history"
      }, 
      {
        "name"=>"ap human geography", 
        "topic"=>"history"
      }
    ]
  }
}
```

In this case, each course is an index of the array. This simple pattern is easy to mimic no matter what objects you're creating. It's much simpler than using keys `first_course`, `second_course`, `third_course`, etc.
The HTML for the form looks like this:

```html
<form action="/student" method="post">
  Student Name: <input type="text" name="student[name]">
  Student Grade: <input type="text" name="student[grade]">
  Course Name: <input type="text" name="student[courses][][name]" />
  Course Topic: <input type="text" name="student[courses][][topic]" />
  Course Name: <input type="text" name="student[courses][][name]" />
  Course Topic: <input type="text" name="student[courses][][topic]" />
  <input type="submit">
</form>
```

Now, we've added four more input fields, which will create TWO courses. Again, because the student has many courses in their schedule, we're nested the courses under the student `student[courses][][name]`. This will create a key called `course` inside the `student` hash in the params. The `courses` key will store an array of hashes, each with the details about the course details.

This is where ERB syntax differs from Ruby. In Ruby, if you wanted a hash to store an array, you would do something like this:

```ruby
my_hash["student"] = {}
my_hash["student["name"] = "Joe"
myhash["student"]["course"] = []
myhash["student"]["course"][0] = { "name" => "US History", "topic" => "History"}
myhash["student"]["course"][1] = { "name" => "AP Human Geography", "topic" => "History"}
```

To access the first course's name, you would do something like:

```ruby
my_hash["student"]["course"][0]["name"]
```

Unfortunately, this is where the ERB syntax starts to differ from Ruby. We use the `[]` in our form view and ERB can automagically index the array for us, turning `my_hash["student"]["course"][0]["name"]` into `student[courses][][name]`. The `[]` is some ERB magic, that we just need to learn to accept and use. It actually helps us out, and let's us simplify our code!

####THE DISPLAY VIEW
We need a way to display the objects back to the user (in this case the registar) once the student and their courses have been created. For later use in the controller, we'll call this file `student.erb`.

```html
<h1>Student</h1>
 
<div class="student">
  <h3>Name: <%= @student.name %></h3><br>
  <h4>Height: <%= @student.grade %></h4>
</div><br>
 
<h1>Classes</h1>
<% @courses.each do |course| %>
  <div class="class">
    <p>Name: <%= course.name %></p><br>
    <p>Type: <%= course.topic %></p><br>
  </div><br>
<% end %>
```

In this view, we're using the instance variable `@student` and the reader methods `name` and `grade` to display the student's information.

We're then iterating over `@courses` to display the name and topic of each class.

####THE CONTROLLER
Now, we need two controller actions - one to serve up the form and one to process the data from the form.

In order to serve the form in the browser, we need a GET request:

```ruby
get '/' do
  erb :new
end
```

And now we need a way to process the input from the user, and to display the student and their classes. We process a form with a POST request:

```ruby
post '/student' do
  @student = Student.new(params[:student])
 
  params[:student][:course].each do |course, details|
    Course.new(details)
  end
 
  @classes = Course.all
 
  erb :student
end
```

In this controller action, we're creating a new student using the `params[:student]`, which just pulls the information about `name` and `grade`.

`params[:student][:course]` gives us a series of hashes that store each individual course information:

```ruby
{ 
  "0"=>{
    "name"=>"AP US HIStory", 
    "topic"=>"history"
  }, 
  "1"=>{
    "name"=>"ap human geography", 
    "topic"=>"history"
  }
}
```

We can iterate over those nested hashes using `.each` and then use the values to create two instances of our `Course` class.

Lastly, this controller action loads the erb file `student.erb`
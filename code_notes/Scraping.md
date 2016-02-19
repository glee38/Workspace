##Gems and Bundler

**WHAT ARE RUBYGEMS?**

Nothing you ever write will be 100% your code. While you probably haven't noticed it every day you use somebody else's code. You didn't write your text editor, you didn't write Ruby, you didn't write your operating system. Those are the types of things that regular users interact with. As a developer though there is a new set of outside code you will work with: Libraries. Libraries (or gems in Ruby parlance) are just bundles of code that someone else wrote for you to integrate into your code base. For example, remember rspec? That's a gem. Instead of everyone having to re-invent a way to do testing for ruby, initially one person and now hundreds of people have worked together to make a single amazing library that everyone can use. It's open source, and you integrate it using the RubyGems tool. Head over to rubygems.org, there are thousands of gems you can pull from that will make your life easier. That is the power of open source. Together we can create something no single person could make.

###How to find a gem?

One option is always to google. Lets assume we are looking for a gem to send emails. We could google `ruby gem to send emails`. One of the first result is a link to `https://www.ruby-toolbox.com/categories/e_mail`.

Another option is to go to `https://rubygems.org/` and enter `mail` in the search bar.

**How to choose the right gem?**

Lets assume we googled and now we are on the `ruby-toolbox.com` website, which shows a list of all the gems that help with sending mails. Which one is the best? How to choose from those many options?

At the top of the page you will see the most popular gems listed - `ActionMailer`, `Mail` and `Ponyare` the top 3.

![ruby-gem](http://readme-pics.s3.amazonaws.com/the-ruby-toolbox-most-popular.png)

**Which one should I use for my application?**

First of all, if you have a rails application you should use gems built for rails, like the `actionmailer` gem. How do we know it is a rails application? If you look at the Github link, you'll notice this gems lives under `rails/rails repository.

![action-mailer](http://readme-pics.s3.amazonaws.com/actionmailer.png)
![mail](http://readme-pics.s3.amazonaws.com/mail.png)

We don't have a rails application, so we need to decide between the `mail` and `pony` gem. The easiest way to do this is by inspecting the total fork count, or by going to the Github repository. Some questions that will help with making your decision:

+ How many people forked the repo?
+ How many are contributed to the repo?
+ How many open issues does the repo have?
+ Does the repo have a test suite?

**How to install a gem?**

We are going to use the `mail` gem.

**How to 'require' a gem**

One way of installing it would be to run `gem install mail` in your terminal and then `require` the gem in your file that uses that gem.

For example:

```
require 'mail'
```

**How to add the gem to your Gemfile**

The other way, assuming you have a application, would be to add `gem 'mail', '~> 2.6', '>= 2.6.3'` (which is the current version of the gem) to your `GEMFILE`. This makes sure who ever uses your application would also use the same version of the gem.

**GEMFILE**

The Gemfile is a list of gems your app uses. The Gemfile lets you setup groups, so gems are only loaded in specific instances. For example, you might have a gem like Pry in your `development` group because you only need to use Pry to debug when you are in the development phase. Your code in production, i.e. when your app is being used by a user, doesn't need to use the Pry gem.

Here's an example Gemfile.

```
source "https://rubygems.org"
 
gem "sinatra"
 
group :development do
  gem "pry"
end
```

**WHAT IS BUNDLER?**

Bundler is a way to handle code dependencies. To see why that's a big deal, let's try to understand the problem a bit.

**THE PROBLEM**

Imagine you're writing an amazing app. This app, being built on the shoulders of giants(gems), requires OTHER code to work. If it's a web app, maybe you'll be using the incredible Sinatra gem. Need a database? Try the Sequel gem.

One way to handle this, is to have a note in your README, with something like, "Hey, install Sinatra and Sequel".

Software is complex. One change to a dependency can completely break your app. We can remedy this by enforcing that we use a specific version of the gem. But this, too, has its problems. How do you enforce this? Are you going to trust that everyone has the right version?

No! We're programmers damn it!

**ENTER BUNDLER**

Bundler handles all of this for you. It provides you with a `Gemfile` where you can keep your requirements in one place. The `Gemfile` creates a single place for gems to be required and versions to be specified.

+ Need the Sinatra gem for your project? Add `gem 'sinatra'` to your Gemfile.
+ Need the Sinatra gem, but at version 1.4.5? Add `gem 'sinatra', '1.4.5'` to your Gemfile.
+ Need the Sinatra gem at a version higher than 1.4, but less than 1.5? Add gem `'sinatra', '~> 1.4.0'`

[Read more here](http://bundler.io/gemfile.html)

With this, you can make sure everyone working on your app is using the right version. Now to get the code working :-)

###Code along

**USING BUNDLER**

Getting started with Bundler is super easy. To create a Gemfile, type `bundle init` in your terminal. You'll notice we created one for you in the repo so running `bundle init` will give you an error.

**ANATOMY OF BUNDLER FILES**

There's only one file Bundler requires you have (Gemfile), the other files are conventional for a typical Ruby application, but not required by the use of Bundler for gem management.

+ **Gemfile** - This file is required by Bundler and contains a source, and a list of file requirements. That's all.
+ **config/environment.rb** - The environment file is where we'll be loading all of our app's dependencies, from gems to database connections.
+ **bin/run.rb** - This file will start our application. This file will require the environment file we created earlier to provide our app with access to our gems.

**GEMFILE**

Add this code to your `Gemfile`

```
source "https://rubygems.org"
 
gem "sinatra"
 
group :development do
  gem "pry"
end
```

After getting all of your gems in your Gemfile, run `bundle install` from your terminal. This will install the listed gems for you. They won't show up in the directory, but they are in your system, and available.

Running `bundle install` also creates a new file: `Gemfile.lock`. This file notes which specific version of the gem was installed.

**CONFIG/ENVIRONMENT.RB**

This file is used to set up our environment. Here we specify which Bundler groups we want to load. The following code is used to load the `default` group (Anything not explicitly in a group, like `development` in your Gemfile), and the `development` group.

**Place the following code in `config/environment.rb`:**

```
require 'bundler/setup'
Bundler.require(:default, :development)
```

**Important:** The two arguments that you are passing into the `.require` method *must be passed in in the correct order*, shown above. The test you are trying to pass is testing for order.

**BIN/RUN.RB**

This is where the action is. This is where our app logic goes, and where we make our millions.

To take advantage of all of the work we did in the environment file, let's require it here.

**Place the following code in `bin/run.rb`**:

```
require_relative '../config/environment'
```

That's it! Now we can access all of our gems from our `run.rb` file.

**FINISHED GEMFILE CODE**

```
source "https://rubygems.org"
 
gem "sinatra", "1.4.4"

#using a hash format
gem "pry", :group => :development

#using a block format 
group :test do
  gem "rspec"
end

gem "hashie"

#funny sperm-looking guy ~> is called a twiddle-wakka. means the following: 
#'~> 0.3.1' is satisfied by 0.3.1, 0.3.2, 0.3.3, etc.
#'~> 0.3.1' is not satisfied by 0.3.0
#'~> 0.3' is satisfied by 0.3.1, 0.4.0, 0.5.1, etc.
#'~> 0.3' is not satisfied by 0.2.0, 0.2.1, etc.

gem "octokit", "~> 2.0"

gem "awesome_print", :git => "https://github.com/michaeldv/awesome_print.git"
```
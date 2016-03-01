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

##Scraping

###Introduction

In previous lessons we've become familiar with working with APIs in order to retrieve data from external resources. You may have seen, for example, the process of sending an HTTP request to an API and receiving data back from that API in JSON format. You may also have seen the Twitter gem used to request data from Twitter.

However, there is yet another way for our Ruby programs to retrieve data from external sources: web scraping. Web scraping is the act of parsing a web page's HTML and pulling, or "scraping" pertinent data from that HTML. In this reading, we'll take a brief look at what scraping is and how to accomplish it. Then, we'll move on to a scraping code along exercise.

###What is Scraping and Why Use it?

As we established above, scraping is a technique used to grab data out of the HTML that makes up a web page. Scraping can be difficult to accomplish––in order to get the data we want, we need to closely examine the HTML and identify exactly which elements contain the information we're interested in. It requires a high degree of precision.

So, if scraping is so tricky, why do we use it? Well, not all of the data we might be interested in using to program is available to use through APIs. For example, let's say we're creating an app that catalogues popular musicians and searches the web for their upcoming concerts. A quick Google search will reveal that, unfortunately for us, there isn't a "Popular Musician" API out there just waiting to be used. There is however, a very comprehensive list of musicians on the Billboard website. In such a scenario, you may want to programmatically grab every musician's name from the Billboard list and store those artists in your own database.

Here's another example: let's say you're creating an app that allows a user to subscribe to a news feed. You anticipate that your users are super-tech savvy and might be interested in subscribing to some lesser-known tech news sites. Such sites may not have an API that makes their articles available to you. Instead, you would have to scrape those sites for their latest news articles and send those newest articles to your users.

These are just a few examples of situations in which scraping might come in handy. Now that we have a few use-cases that illustrate the utility of scraping, let's talk about *how* to scrape.

###Scraping HTML Using Nokogiri and Open-URI

**REFRESHER: WHAT IS OPEN-URI?**

Open-URI is a module in Ruby that allows us to programmatically make HTTP requests. It gives us a bunch of useful methods to make different types of requests, but for this guide, we're interested in only one: `open`. This method takes one argument, a URL, and will return to us the HTML content of that URL.

In other words, running:

```
html = open('http://www.google.com')
```

stores the HTML of Google into a variable called html. (More specifically, it actually stores the HTML in a temporary file that we can then call read on to get the raw HTML. We won't worry about that here though.)

**WHAT IS NOKOGIRI?**

Nokogiri is a Ruby gem that helps us to parse HTML and collect data from it. Essentially, Nokogiri allows us to treat a huge string of HTML as if it were a bunch of nested nodes. In doing so, Nokogiri offers you, the programmer, a series of methods that you can use to extract the desired information from these nested nodes. Nokogiri makes the level of precision required to extract the necessary data much easier to attain. It works like a fine-toothed saw to scrape only the necessary data. In fact, that's what "nokogiri" means: a fine-toothed saw.

Let's get Nokogiri up and running and look at a very basic example of its usage. Then, we'll move on to the next lesson, where you'll try it out for yourself.

**INSTALLING NOKOGIRI**

Installing Nokogiri is as easy as `gem install nokogiri`. If you run into any issues with this, check out the following documentation: Nokogiri Installation Guide.

**OPENING A WEB PAGE AS HTML WITH NOKOGIRI AND OPEN-URI**

Let's say we have a file, `scraper.rb` which is responsible for (you guessed it) scraping. We need to require Nokogiri and open-uri:

```
require 'nokogiri'
require 'open-uri'
 
#more code coming soon!
```

We can use the following line to grab the HTML that makes up the Flatiron School's landing page at flatironschool.com:

```
html = open("http://flatironschool.com/")
```

Next, we'll use the `Nokogiri::HTML` method to take the string of HTML returned by open-uri's `open` method and and convert it into a NodeSet (aka, a bunch of nested "nodes") that we can easily play around with.

```
Nokogiri::HTML(html)
```

Let's save the HTML document in a variable, doc that we can then operate on:

```
doc = Nokogiri::HTML(html)
```

If we were to `puts` out doc right now, we'd see something like this in our terminal:

```
<!DOCTYPE html>
<html> <head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8"> <meta charset="utf-8"> <meta name="viewport" content="width=device-width, initial-scale=1.0"> <meta content="IE=edge,chrome=1" html-equiv="X-UA-Compatible"> <meta name="description" content="Learn to love code. The Flatiron School trains passionate, creative people in web and mobile development. Our Web and iOS Immersive courses are 12 weeks, full-time, and prepare students for careers as software developers."> <title>Flatiron School</title> <link href="stylesheets/all.css" rel="stylesheet"> <script src="javascripts/all.js"></script> <link rel="canonical" href="http://flatironschool.com/"> <link rel="shortcut icon" type="image/x-icon" href="/images/favicon.ico"> <meta name="google-site-verification" content="IyKREnTZw16bmZ3mSIACjTF7n1smFdMOkjJS2dtwLl8"> </head> <body> <div class="wrapper"> <header id="header"> <div class="nav-holder holder"> <strong class="logo"><a href="/"><img src="images/logo.png" alt="The Flatiron School"></a></strong> <nav id="nav"> <a href="#" class="opener"><span></span></a> <div class="drop"> <div class="drop-holder"> <ul class="top-nav"> <li class="active hide_mobile"><a href="http://go.flatironschool.com/apply" target="_blank">Apply</a></li> <li class="hide_mobile"><a href="/hire">Partner</a></li> <li class="hide_mobile"><a href="http://precollege.flatironschool.com" target="_blank">Precollege</a></li> <li class="hide_mobile"><a href="http://blog.flatironschool.com/" target="_blank">Blog</a></li> <li class="hide_mobile"><a href="/contact">Contact</a></li> <li class="hide_mobile"><a href="/careers">We're hiring!</a></li> </ul> <ul class="main-nav mega-menu-nav"> <li> <a href="/school">The School</a> <div class="megamenu"> <div class="column quote-container"> <div class="tab-content"> <div id="tab7"> <blockquote> <q>“Best of luck to the new @FlatironSchool group. Looking back, you'll categorize your life as before today and after today.”</q> <cite> <a href="https://twitter.com/mcnameekm/status/298577378274336772" class="twitter"> <span class="icon-twitter"></span> </a> <span class="photo"><span data-picture data-alt="image description"> <span data-src="images/kevin_mcnamee.png"></span> <span data-src="images/kevin_mcnamee.png" data-media="(-webkit-min-device-pixel-ratio:1.5), (min-resolution:1.5dppx)"></span> <!--[if (lt IE 9) & (!IEMobile)]><span data-src="images/kevin_mcnamee.png"></span><![endif]--> <noscript><img src="images/kevin_mcnamee.png" width="41" height="40" alt="image description"></noscript> </span> </span> <strong class="name">@mcnameekm</strong> </cite> </blockquote> </div> </div> </div> <div class="column col-4 menu-holder"> <ul class="submenu"> <li><a href="/school#about">About</a></li> <li><a href="/team">The Team</a></li> <li><a href="/employers">Employers</a></li> <li><a href="/faq">FAQ</a></li> <li><a href="/careers">Careers at Flatiron</a></li> </ul> </div> </div> </li> <li class="mobile_subnav"><a href="/school#about">About</a></li> <li class="mobile_subnav"><a href="/team">The Team</a></li> <li class="mobile_subnav"><a href="/employers">Employers</a></li> <li class="mobile_subnav"><a href="/faq">FAQ</a></li> <li class="mobile_subnav"><a href="/careers">Careers at Flatiron</a></li> <li> <a href="/courses">Courses</a> <div class="megamenu"> <div class="column quote-container"> <div class="tab-content"> <div id="tab7"> <blockquote> <q>“What I love most about @FlatironSchool grads: not only are they great programmers, but they are great bloggers and presenters.”</q> <cite> <a href="https://twitter.com/debbiemadden200/status/345546073751822340" class="twitter"> <span class="icon-twitter"></span> </a> <span class="photo"><span data-picture data-alt="image description"> <span data-src="images/debbie_madden.png"></span> <span data-src="images/debbie_madden.png" data-media="(-webkit-min-device-pixel-ratio:1.5), (min-resolution:1.5dppx)"></span> <!--[if (lt IE 9) & (!IEMobile)]><span data-src="images/debbie_madden.png"></span><![endif]--> <noscript><img src="images/debbie_madden.png" width="41" height="40" alt="image description"></noscript> </span> </span> <strong class="name">@debbiemadden200</strong> </cite> </blockquote> </div> </div> </div> <div class="column col-4 menu-holder"> <ul class="submenu"> <li><a href="/courses">Course Listing</a></li> <li><a href="/web">Web Development</a></li> <li><a href="/ios">iOS Development</a></li> <li><a href="/frontend">Intro to Front End</a></li> <li><a href="/data-science">Intro to Data Science</a></li> <li><a href="/diy-electronics">DIY Electronics</a></li> <li><a href="/nycfellowship" target="_blank">Fellowship</a></li> </ul> </div> </div> </li> <li> <a href="/events">Events</a> <div class="megamenu"> <div class="column col-3"> <a class="event" href="http://www.meetup.com/Ruby-Project-Night-NYC/events/222354716/" target="_blank"> <em class="date">08.17.15</em> <span class="ico icon-svg_03"></span> <h2>Ruby Project Night</h2> </a> </div> <div class="column col-3"> <a class="event" href="http://www.meetup.com/Flatiron-School-Presents/" target="_blank"> <em class="date">08.18.15</em> <span class="ico icon-svg_05"></span> <h2>Flatiron School Students Present</h2> </a> </div> <div class="column col-3"> <a class="event" href="http://www.meetup.com/Flatiron-School-Presents/" target="_blank"> <em class="date">08.25.15</em> <span class="ico icon-svg_05"></span> <h2>Flatiron School Students Present</h2> </a> </div> <div class="column col-4 menu-holder"> <ul class="submenu"> <li><a href="/events">Upcoming Events</a></li> <li><a href="/guestspeakers">Guest Speakers</a></li> </ul> </div> </div> </li> <li class="mobile_subnav"> <a href="/guestspeakers">Guest Speakers</a>
</li> </ul> </div> </div> </nav> </div> </header> <main id="main"> <div class="slideshow"> <div id="hero-video-container" class="hero-video-container"> <h1>
<span>Learn</span> to Love <span>Code</span>.</h1> <a href="/courses" class="find-course">Find a Course</a> <a href="http://precollege.flatironschool.com" target="_blank" class="hero">To learn about our Precollege program for high school students, click here.</a> <video id="hero-video" width="100%" loop="loop" preload="auto" autoplay="true"> <source src="http://flatiron-web-assets.s3.amazonaws.com/videos/web-broll-3.mp4" type="video/mp4"> <source src="http://flatiron-web-assets.s3.amazonaws.com/videos/web-broll-3.webm" type="video/webm"> </source></source></video> </div> </div> <div class="slideshow hero-slideshow"> <div class="slideset"> <div class="slide"> <span data-picture data-alt="Flatiron School"> <span data-src="images/new-hero.jpg"></span> <span data-src="images/new-hero@2x.jpg" data-media="(-webkit-min-device-pixel-ratio:1.5), (min-resolution:1.5dppx)"></span> <span data-src="images/new-hero-small.jpg" data-media="(max-width:767px)"></span> <span data-src="images/new-hero@2x-small.jpg" data-media="(max-width:767px) and (-webkit-min-device-pixel-ratio:1.5), (max-width:767px) and (min-resolution:144dpi)"></span> <!--[if (lt IE 9) & (!IEMobile)]><span data-src="images/new-hero.jpg"></span><![endif]--> <noscript><img src="images/new-hero.jpg" width="1400" height="520" alt="Flatiron School"></noscript> </span> <h1>
<span>Learn</span> to Love <span>Code</span>.</h1> <a href="/courses" class="find-course">Find a Course</a> <a href="http://precollege.flatironschool.com" target="_blank" class="hero">To learn about our Precollege program for high school students, click here.</a> </div> </div> </div> <section class="section"> <header class="page-heading main-headline"> <h1><span class="grey-text">350+ lives changed,<br>and counting.</span></h1> <div class="link-row no-border"> <a href="http://flatironschool.com/far" target="_blank" class="btn-inverted">READ OUR 2014 ANNUAL REPORT</a> </div> </header> <div class="fourcolumns"> <a href="/courses" class="box"> <span data-picture data-alt="image description"> <span data-src="images/img02.jpg"></span> <span data-src="images/img02@2x.jpg" data-media="(-webkit-min-device-pixel-ratio:1.5), (min-resolution:1.5dppx)"></span> <span data-src="images/img02-small.jpg" data-media="(max-width:767px)"></span> <span data-src="images/img02@2x-small.jpg" data-media="(max-width:767px) and (-webkit-min-device-pixel-ratio:1.5), (max-width:767px) and (min-resolution:144dpi)"></span> <!--[if (lt IE 9) & (!IEMobile)]><span data-src="images/img02.jpg"></span><![endif]--> <noscript><img src="images/img02.jpg" width="209" height="209" alt="image description"></noscript> </span> <strong class="title">Learn</strong> <h2>Find a course. Build a better future.</h2> </a> <a href="/employers" class="box"> <span data-picture data-alt="image description"> <span data-src="images/img03.jpg"></span> <span data-src="images/img03@2x.jpg" data-media="(-webkit-min-device-pixel-ratio:1.5), (min-resolution:1.5dppx)"></span> <span data-src="images/img03-small.jpg" data-media="(max-width:767px)"></span> <span data-src="images/img03@2x-small.jpg" data-media="(max-width:767px) and (-webkit-min-device-pixel-ratio:1.5), (max-width:767px) and (min-resolution:144dpi)"></span> <!--[if (lt IE 9) & (!IEMobile)]><span data-src="images/img03.jpg"></span><![endif]--> <noscript><img src="images/img03.jpg" width="209" height="209" alt="image description"></noscript> </span> <strong class="title">Partner</strong> <h2>Hire or mentor a Flatiron School student.</h2> </a> <a href="/events" class="box"> <span data-picture data-alt="image description"> <span data-src="images/img05.jpg"></span> <span data-src="images/img05@2x.jpg" data-media="(-webkit-min-device-pixel-ratio:1.5), (min-resolution:1.5dppx)"></span> <span data-src="images/img05-small.jpg" data-media="(max-width:767px)"></span> <span data-src="images/img05@2x-small.jpg" data-media="(max-width:767px) and (-webkit-min-device-pixel-ratio:1.5), (max-width:767px) and (min-resolution:144dpi)"></span> <!--[if (lt IE 9) & (!IEMobile)]><span data-src="images/img05.jpg"></span><![endif]--> <noscript><img src="images/img05.jpg" width="209" height="209" alt="image description"></noscript> </span> <strong class="title">Visit</strong> <h2>Stop by an event. We'd love to meet you.</h2> </a> <a href="/school" class="box"> <span data-picture data-alt="image description"> <span data-src="images/img04.jpg"></span> <span data-src="images/img04@2x.jpg" data-media="(-webkit-min-device-pixel-ratio:1.5), (min-resolution:1.5dppx)"></span> <span data-src="images/img04-small.jpg" data-media="(max-width:767px)"></span> <span data-src="images/img04@2x-small.jpg" data-media="(max-width:767px) and (-webkit-min-device-pixel-ratio:1.5), (max-width:767px) and (min-resolution:144dpi)"></span> <!--[if (lt IE 9) & (!IEMobile)]><span data-src="images/img04.jpg"></span><![endif]--> <noscript><img src="images/img04.jpg" width="209" height="209" alt="image description"></noscript> </span> <strong class="title">Connect</strong> <h2>Learn more about Flatiron School or get in touch.</h2> </a> </div> <div class="twocolumns" id="big-squares"> <div class="column"> <strong class="title">Flatiron Data</strong> <a href="/jobs-report-2014"> <h1 class="heading">2014 Jobs Report</h1> </a> <div class="subtext small-mobile-subtext long-subtext"> <p>Our full-time courses prepare students for careers in software development. To provide transparency around student outcomes, we released a verified report on our job placement results.</p> </div> <a href="/jobs-report-2014" class="more">Download the Report</a> </div> <div class="column img"> <div class="bg-stretch" id="immersive-square"> <img src="images/img06.jpg" alt="Upcoming Classes"> </div> <strong class="title">Upcoming Classes</strong> <a href="/web"> <h1 class="heading">NYC Web Development Fellowship</h1> </a> <div class="subtext small-mobile-subtext"> <p class="light">An intensive training program designed to equip New Yorkers with the skills necessary to launch careers in web development. Applications are now open.</p> </div> <a href="/nycfellowship" class="more">Learn More</a> </div> </div> <div class="row"> <div class="column"> <strong class="title">Alumni Stories</strong> <div class="carousel"> <div class="mask"> <div class="slideset"> <div class="slide"> <h2>
<span>Sara Gorecki</span><br> <span class="alumni-title">Node.js Engineer</span>
</h2> <a class="photo-link" href="http://blog.flatironschool.com/post/102455310528/from-project-manager-to-node-js-developer-a" target="_blank"> <span class="alumni-photo" data-picture data-alt="Sara Gorecki"> <span data-src="http://flatiron-web-assets.s3.amazonaws.com/images/alumni/sara-gorecki.png"></span> <span data-src="http://flatiron-web-assets.s3.amazonaws.com/images/alumni/sara-gorecki.png" data-media="(-webkit-min-device-pixel-ratio:1.5), (min-resolution:1.5dppx)"></span> <span data-src="http://flatiron-web-assets.s3.amazonaws.com/images/alumni/sara-gorecki.png" data-media="(max-width:767px)"></span> <span data-src="http://flatiron-web-assets.s3.amazonaws.com/images/alumni/sara-gorecki.png" data-media="(max-width:767px) and (-webkit-min-device-pixel-ratio:1.5), (max-width:767px) and (min-resolution:144dpi)"></span> <!--[if (lt IE 9) & (!IEMobile)]><span data-src="http://flatiron-web-assets.s3.amazonaws.com/images/alumni/sara-gorecki.png"></span><![endif]--> <noscript><img src="http://flatiron-web-assets.s3.amazonaws.com/images/alumni/sara-gorecki.png" width="251" height="251" alt="image description"></noscript> </span> </a> <a class="slide-link" href="http://blog.flatironschool.com/post/102455310528/from-project-manager-to-node-js-developer-a" target="_blank"> <p>"It completely changed my life. I feel like it's a clichéd thing to say...but it is absolutely true."</p> </a> </div> </div> </div> </div> </div> <div class="column"> <div class="box top-row little-square"> <strong class="title">QUORA</strong> <h2><a href="http://www.quora.com/What-kinds-of-projects-are-Flatiron-School-alumni-working-on-in-their-new-jobs" target="_blank">What kinds of jobs do grads get?</a></h2> </div> <div class="box top-row little-square"> <strong class="title">QUORA</strong> <h2><a href="http://www.quora.com/How-can-I-best-prepare-for-Flatiron-Schools-admissions-process" target="_blank">How do I get in?</a></h2> </div> <div class="box"> <strong class="title">QUORA</strong> <h2><a href="http://www.quora.com/Whats-a-typical-day-like-for-a-student-at-Flatiron-School" target="_blank">What's a typical day like for a student?</a></h2> </div> <div class="box"> <strong class="title">QUORA</strong> <h2><a href="http://www.quora.com/How-successful-are-code-bootcamps-like-Dev-Bootcamp-and-Flatiron-School-at-job-placement/answer/Rebekah-Rombom-1" target="_blank">Does Flatiron School help students find jobs?</a></h2> </div> </div> </div> </section> <section class="section no-bottom-border"> <header class="page-heading page-heading2"> <h1 class="no-description">Upcoming Events</h1> </header> <ul class="accordion"> <li class="active lblue"> <div class="slide"> <div class="bg-stretch"> <img src="images/img09.jpg" alt="Ruby Project Night"> </div> <em class="date">Monday 17 August, 6pm-9pm</em> <span class="ico icon-svg_03 event-icon"></span> <h1 class="event-title">Ruby Project Night</h1> <p>Come work on personal projects, release some code, and meet some other folks in the community! If you need help on anything related to Ruby, you should come to this event. If you think you've mastered Ruby, you should also come and help out other people too!</p> <a href="http://www.meetup.com/Ruby-Project-Night-NYC/events/222354716/" target="_blank" class="more">Register</a> </div> <div class="opener"> <span class="ico icon-svg_03"></span> <strong class="title">Ruby Project Night</strong> <em class="date">08.17.15</em> </div> </li> <li class=" green"> <div class="slide"> <div class="bg-stretch"> <img src="images/img06.jpg" alt="Flatiron School Students Present"> </div> <em class="date">Tuesday 18 August, 7pm-9:30pm</em> <span class="ico icon-svg_05 event-icon"></span> <h1 class="event-title">Flatiron School Students Present</h1> <p>Students from Flatiron School's Web and iOS Development Immersives present applications they've built or a technical topic of their choice—from how to implement Core data to how to build a recipe app.</p> <a href="http://www.meetup.com/Flatiron-School-Presents/" target="_blank" class="more">Register</a> </div> <div class="opener"> <span class="ico icon-svg_05"></span> <strong class="title">Flatiron School Students Present</strong> <em class="date">08.18.15</em> </div> </li> <li class=" lblue"> <div class="slide"> <div class="bg-stretch"> <img src="images/img08.jpg" alt="Flatiron School Students Present"> </div> <em class="date">Tuesday 25 August, 6pm-8:30pm</em> <span class="ico icon-svg_05 event-icon"></span> <h1 class="event-title">Flatiron School Students Present</h1> <p>Students from Flatiron School's Web and iOS Development Immersives present applications they've built or a technical topic of their choice—from how to implement Core data to how to build a recipe app.</p> <a href="http://www.meetup.com/Flatiron-School-Presents/" target="_blank" class="more">Register</a> </div> <div class="opener"> <span class="ico icon-svg_05"></span> <strong class="title">Flatiron School Students Present</strong> <em class="date">08.25.15</em> </div> </li> <li class=" green"> <div class="slide"> <div class="bg-stretch"> <img src="images/img06.jpg" alt="ManhattanJS"> </div> <em class="date">Wednesday 9 September, 7pm-9:30pm</em> <span class="ico icon-svg_01 event-icon"></span> <h1 class="event-title">ManhattanJS</h1> <p>ManhattanJS is a combination of Ruby, Javascript and CSS developers who aim to bring the greatest programming minds to Manhattan once a month to chat, teach, and share their work.</p> <a href="http://manhattanjs.com/" target="_blank" class="more">Register</a> </div> <div class="opener"> <span class="ico icon-svg_01"></span> <strong class="title">ManhattanJS</strong> <em class="date">09.09.15</em> </div> </li> <li class=" lblue"> <div class="slide"> <div class="bg-stretch"> <img src="images/img08.jpg" alt="Out in Tech"> </div> <em class="date">Tuesday 10 September, 6pm-9pm</em> <span class="ico icon-svg_04 event-icon"></span> <h1 class="event-title">Out in Tech</h1> <p>As New York’s digital scene blossoms with LGBT tech enthusiasts, so too does the need for a community that can connect all areas of the sector – from big companies to small start-ups, from AdTech to FinTech – in a casual and meaningful way. Out in Tech aims to unite the tech industry one connection at a time.</p> <a href="http://www.outintech.com/events/" target="_blank" class="more">Register</a> </div> <div class="opener"> <span class="ico icon-svg_04"></span> <strong class="title">Out in Tech</strong> <em class="date">09.10.15</em> </div> </li> <li class=" green"> <div class="slide"> <div class="bg-stretch"> <img src="images/img06.jpg" alt="NYC on Rails"> </div> <em class="date">Wednesday 16 September, 6:30pm-8:30pm</em> <span class="ico icon-svg_03 event-icon"></span> <h1 class="event-title">NYC on Rails</h1> <p>Discuss all Ruby and Ruby on Rails related topics, meet other developers, find out about interesting projects and opportunities for work and potential employment.</p> <a href="http://www.meetup.com/nyc-on-rails/" target="_blank" class="more">Register</a> </div> <div class="opener"> <span class="ico icon-svg_03"></span> <strong class="title">NYC on Rails</strong> <em class="date">09.16.15</em> </div> </li> <li class=" lblue"> <div class="slide"> <div class="bg-stretch"> <img src="images/img09.jpg" alt="Ruby Project Night"> </div> <em class="date">Monday 21 September, 6pm-9pm</em> <span class="ico icon-svg_03 event-icon"></span> <h1 class="event-title">Ruby Project Night</h1> <p>Come work on personal projects, release some code, and meet some other folks in the community! If you need help on anything related to Ruby, you should come to this event. If you think you've mastered Ruby, you should also come and help out other people too!</p> <a href="http://www.meetup.com/Ruby-Project-Night-NYC/" target="_blank" class="more">Register</a> </div> <div class="opener"> <span class="ico icon-svg_03"></span> <strong class="title">Ruby Project Night</strong> <em class="date">09.21.15</em> </div> </li> </ul> </section> <section class="section no-bottom-border"> <div class="row"> <header class="page-heading page-heading2 page-heading-no-top-border"> <h1 class="no-description">Press</h1> </header> <div class="social-grid"> <div class="box white-box"> <a class="right-white white-box-link" href="http://www.usatoday.com/story/tech/columnist/2014/11/26/van-jones-yeswecode-silicon-valley-diversity-nyc-web-development-project/70103542/" target="_blank"> <div class="text"> <img class="press-left-image" src="http://flatiron-web-assets.s3.amazonaws.com/press/usa_today.png" alt="USA Today"> </div> <em class="date"></em> </a> </div> <div class="box white-box extra-wide"> <span class="ico"></span> <div class="text press-text"> <h2 class="long-quote short-line-height">"Flatiron School is quickly and quietly becoming one of the leading schools for would-be computer programmers."</h2> <a class="white-box-link citation-link" href="http://www.usatoday.com/story/tech/columnist/2014/11/26/van-jones-yeswecode-silicon-valley-diversity-nyc-web-development-project/70103542/" target="_blank"><p class="citation">VAN JONES, USA TODAY</p></a> </div> <em class="date"></em> </div> <div class="box"> <a class="white-box" href="http://betabeat.com/2014/03/civilians-learn-to-code-for-free-at-flatiron-school/" target="_blank"> <div class="text"> <img class="dimmed" src="images/beta_beat.png" alt="BetaBeat"> </div> <em class="date"></em> </a> </div> <div class="box"> <a class="white-box" href="http://www.nytimes.com/2014/06/23/nyregion/flatiron-school-program-expands-new-yorks-web-developer-ranks.html" target="_blank"> <div class="text"> <img class="dimmed" src="http://flatiron-web-assets.s3.amazonaws.com/press/ny_times.png" alt="The New York Times"> </div> <em class="date"></em> </a> </div> <div class="box"> <a class="white-box" href="http://blogs.wsj.com/venturecapital/2014/04/09/flatiron-school-raises-5-5m-to-teach-tech-skills-place-graduates-in-jobs/" target="_blank"> <div class="text"> <img class="dimmed" src="images/wsj.png" alt="The Wall Street Journal"> </div> <em class="date"></em> </a> </div> <div class="box"> <a class="white-box" href="http://www.fastcompany.com/3008349/how-flatiron-school-makes-" target="_blank"> <div class="text"> <img class="dimmed" src="http://flatiron-web-assets.s3.amazonaws.com/press/fast_company.png" alt="Fast Company"> </div> <em class="date"></em> </a> </div> </div> </div> </section> <section id="marketing-video-section"> <div id="marketing-video-container" class="marketing-video-container"> <div id="letterbox-left"> </div> <p class="video-text-overlay">"...we just found really great people, and taught them how to code."</p> <a class="play-video">Hear Our Story<span class="play-arrow">▶</span></a> <a id="android-play-video-container"><img id="android-play-video" src="images/android-play-button.png"></a> <video id="marketing-video" width="80%" preload="auto" controls poster="http://flatiron-web-assets.s3.amazonaws.com/images/marketing-video-poster.jpg"> <source src="http://flatiron-web-assets.s3.amazonaws.com/videos/flatiron-school-main.webm" type="video/webm"> <source src="http://flatiron-web-assets.s3.amazonaws.com/videos/flatiron-school-main.mp4" type="video/mp4"> </source></source></video> <div id="letterbox-right"> </div> </div> </section> <section class="section no-bottom-border"> <header class="page-heading page-heading2 bg-white"> <h1>Student Projects</h1> </header> <div class="carousel-holder"> <div class="carousel"> <div class="mask"> <div class="slideset"> <div data-href="http://heatseeknyc.com/" class="slide student-slide"> <h1>Heat Seek NYC</h1> <div class="student-work-description-container"> <p class="student-work-description"><a href="http://heatseeknyc.com/" target="_blank">Heat Seek NYC</a> helps New Yorkers validate legal claims against landlords who won't keep the heat on. These temperature sensors are now in real homes, thanks to a fully-backed Kickstarter, an NYC Big Apps win, and a shoutout out from Mayor de Blasio.</p> </div> <strong class="author">by William Jeffries, Tristan Siegel, and Daniel Kronovet </strong> <div class="screen-holder"> <span data-picture data-alt=""> <span data-src="http://flatiron-web-assets.s3.amazonaws.com/images/student-work/screenshots/heat-seek-nyc.png"></span> <span data-src="http://flatiron-web-assets.s3.amazonaws.com/images/student-work/screenshots/heat-seek-nyc.png" data-media="(-webkit-min-device-pixel-ratio:1.5), (min-resolution:1.5dppx)"></span> <span data-src="http://flatiron-web-assets.s3.amazonaws.com/images/student-work/screenshots/heat-seek-nyc.png" data-media="(max-width:767px)"></span> <span data-src="http://flatiron-web-assets.s3.amazonaws.com/images/student-work/screenshots/heat-seek-nyc.png" data-media="(max-width:767px) and (-webkit-min-device-pixel-ratio:1.5), (max-width:767px) and (min-resolution:144dpi)"></span> <!--[if (lt IE 9) & (!IEMobile)]><span data-src="http://flatiron-web-assets.s3.amazonaws.com/images/student-work/screenshots/heat-seek-nyc.png"></span><![endif]--> <noscript><img src="http://flatiron-web-assets.s3.amazonaws.com/images/student-work/screenshots/heat-seek-nyc.png" width="251" height="251" alt=""></noscript> </span> </div> </div> <div data-href="http://kickammender.com/" class="slide student-slide"> <h1>Kickammender</h1> <div class="student-work-description-container"> <p class="student-work-description"><a href="http://kickammender.com/" target="_blank">Kickammender</a> is a recommendation engine for Kickstarter. It gets to know its users with an algorithm. Then it recommends projects for them to back based on their interests.</p> </div> <strong class="author">by Michael Polycarpou and Joe O'Conor </strong> <div class="screen-holder"> <span data-picture data-alt=""> <span data-src="http://flatiron-web-assets.s3.amazonaws.com/images/student-work/screenshots/kickammender.jpg"></span> <span data-src="http://flatiron-web-assets.s3.amazonaws.com/images/student-work/screenshots/kickammender.jpg" data-media="(-webkit-min-device-pixel-ratio:1.5), (min-resolution:1.5dppx)"></span> <span data-src="http://flatiron-web-assets.s3.amazonaws.com/images/student-work/screenshots/kickammender.jpg" data-media="(max-width:767px)"></span> <span data-src="http://flatiron-web-assets.s3.amazonaws.com/images/student-work/screenshots/kickammender.jpg" data-media="(max-width:767px) and (-webkit-min-device-pixel-ratio:1.5), (max-width:767px) and (min-resolution:144dpi)"></span> <!--[if (lt IE 9) & (!IEMobile)]><span data-src="http://flatiron-web-assets.s3.amazonaws.com/images/student-work/screenshots/kickammender.jpg"></span><![endif]--> <noscript><img src="http://flatiron-web-assets.s3.amazonaws.com/images/student-work/screenshots/kickammender.jpg" width="251" height="251" alt=""></noscript> </span> </div> </div> <div data-href="https://itunes.apple.com/us/app/canyon-of-heroes/id922279505?mt=8" class="slide student-slide"> <h1>Canyon of Heroes</h1> <div class="student-work-description-container"> <p class="student-work-description">Built in partnership with the Downtown Alliance, this iOS app celebrates Lower Manhattan's historic ticker-tape parades. <a href="https://itunes.apple.com/us/app/canyon-of-heroes/id922279505?mt=8" target="_blank">Now available in the App Store.</a></p> </div> <strong class="author">by Ray Milian, Peter Prosol, and Viktor Falkner </strong> <div class="screen-holder"> <span data-picture data-alt=""> <span data-src="http://flatiron-web-assets.s3.amazonaws.com/images/student-work/screenshots/canyon-of-heroes.png"></span> <span data-src="http://flatiron-web-assets.s3.amazonaws.com/images/student-work/screenshots/canyon-of-heroes.png" data-media="(-webkit-min-device-pixel-ratio:1.5), (min-resolution:1.5dppx)"></span> <span data-src="http://flatiron-web-assets.s3.amazonaws.com/images/student-work/screenshots/canyon-of-heroes.png" data-media="(max-width:767px)"></span> <span data-src="http://flatiron-web-assets.s3.amazonaws.com/images/student-work/screenshots/canyon-of-heroes.png" data-media="(max-width:767px) and (-webkit-min-device-pixel-ratio:1.5), (max-width:767px) and (min-resolution:144dpi)"></span> <!--[if (lt IE 9) & (!IEMobile)]><span data-src="http://flatiron-web-assets.s3.amazonaws.com/images/student-work/screenshots/canyon-of-heroes.png"></span><![endif]--> <noscript><img src="http://flatiron-web-assets.s3.amazonaws.com/images/student-work/screenshots/canyon-of-heroes.png" width="251" height="251" alt=""></noscript> </span> </div> </div> <a id="student-slide-button" href="http://heatseeknyc.com/" target="_blank" class="more">View Project</a> </div> </div> <a class="btn-prev" href="#"><span class="icon-svg_06"></span></a> <a class="btn-next" href="#"><span class="icon-svg_07"></span></a> </div> </div> </section> <section class="section no-bottom-border"> <header class="page-heading page-heading2"> <h1>Community</h1> </header> <div class="social-grid"> <div class="box wide"> <a class="right" href="http://instagram.com/p/uvc7qXDtYo" target="blank"> <span class="ico icon-instagram"></span> <div class="text"><h2>Last night's #FlatironPresents Meetup was a crazy success.</h2></div> <em class="date">Wednesday 10.29.14</em> </a> </div> <div class="box"> <div class="bg-stretch"> <img class="linkable-image" data-href="http://instagram.com/p/uvc7qXDtYo" src="images/community_1.jpg" alt="Flatiron School Presents"> </div> </div> <div class="box empty"> </div> <div class="box empty"> </div> <div class="box"> <div class="bg-stretch"> <img class="linkable-image" data-href="http://instagram.com/p/lx-6RRDtf6" src="images/community_2.jpg" alt="Oculus Rift at Flatiron School"> </div> </div> <div class="box"> <a class="left" href="http://instagram.com/p/lx-6RRDtf6" target="_blank"> <span class="ico icon-instagram"></span> <div class="text"><h2>We got an @oculus to play with.</h2></div> <em class="date">Thursday 3.20.14</em> </a> </div> <div class="box twitter-holder"> <a href="https://twitter.com/jarretmoses/status/538006889494511616" target="_blank" class="twitter"> <span class="ico icon-twitter"></span> <div class="text"> <p>Thankful for @FlatironSchool for not only teaching me what it means to love what you do but also how to be a better person</p> </div> <div class="author-box"> <span data-picture data-alt="@jarretmoses"> <span data-src="images/j_moses.jpg"></span> <span data-src="images/j_moses.jpg" data-media="(-webkit-min-device-pixel-ratio:1.5), (min-resolution:1.5dppx)"></span> <span data-src="images/j_moses.jpg" data-media="(max-width:767px)"></span> <span data-src="images/j_moses.jpg" data-media="(max-width:767px) and (-webkit-min-device-pixel-ratio:1.5), (max-width:767px) and (min-resolution:144dpi)"></span> <!--[if (lt IE 9) & (!IEMobile)]><span data-src="images/j_moses.jpg"></span><![endif]--> <noscript><img src="images/j_moses.jpg" width="251" height="251" alt="image description"></noscript> </span> <span class="author">@jarretmoses</span> </div> </a> </div> <div class="box"> <div class="bg-stretch"> <img class="linkable-image" data-href="http://instagram.com/p/ujPit2jtai" src="images/community_3.jpg" alt="Chromebook Hacking at Flatiron School"> </div> </div> <div class="box wide"> <a class="left" href="http://instagram.com/p/ujPit2jtai" target="_blank"> <span class="ico icon-instagram"></span> <div class="text"><h2>#Chromebook hacking.</h2></div> <em class="date">Friday 10.24.14</em> </a> </div> <div class="box empty"> </div> <div class="box empty"> </div> <div class="box twitter-holder"> <a href="https://twitter.com/mcnameekm/status/298577378274336772" target="_blank" class="twitter"> <span class="ico icon-twitter"></span> <div class="text"> <p>Today I'm going to get paid to do what I truly love for an awesome company @DowJones ! Thanks @FlatironSchool !! #DevOps</p> </div> <div class="author-box"> <span data-picture data-alt="image description"> <span data-src="images/natacha.png"></span> <span data-src="images/natacha.png" data-media="(-webkit-min-device-pixel-ratio:1.5), (min-resolution:1.5dppx)"></span> <span data-src="images/natacha.png" data-media="(max-width:767px)"></span> <span data-src="images/natacha.png" data-media="(max-width:767px) and (-webkit-min-device-pixel-ratio:1.5), (max-width:767px) and (min-resolution:144dpi)"></span> <!--[if (lt IE 9) & (!IEMobile)]><span data-src="images/natacha.png"></span><![endif]--> <noscript><img src="images/natacha.png" width="251" height="251" alt="image description"></noscript> </span> <span class="author">@Natacha11217</span> </div> </a> </div> <div class="box"> <a class="right" href="http://instagram.com/p/vXGDxnDtYG" target="_blank"> <span class="ico icon-instagram"></span> <div class="text"><h2>Monopoly "modding" workshop.</h2></div> <em class="date">Thursday 11.13.14</em> </a> </div> <div class="box"> <div class="bg-stretch"> <img class="linkable-image" data-href="http://instagram.com/p/vXGDxnDtYG" src="images/community_4.jpg" alt="Game design workshop at Flatiron School"> </div> </div> </div> </section> <div class="link-row"> <a href="#" class="btn-more btn-top">BACK TO TOP</a> </div> </main> <footer id="footer"> <div class="footer-row"> <div class="column full"> <strong class="logo"> <a href="#"><img src="images/logo02.png" alt="The Flatiron School"></a> </strong> </div> <div class="column full bottom_mobile_nav"> <a href="http://go.flatironschool.com/apply" target="_blank">Apply</a>    <a href="/hire">Partner</a>    <a href="https://after.flatironschool.com/" target="_blank">Precollege</a>    <a href="http://blog.flatironschool.com/" target="_blank">Blog</a>    <a href="/contact">Contact</a> </div> <div class="column full"> <strong class="title"><a href="/contact">Contact</a></strong> <address> <span>11 Broadway, Suite 260</span> <span>New York, NY 10004</span> </address> <dl> <dd><a href="tel:+18889580569">1-888-958-0569</a></dd> <dd><a href="mailto:info@flatironschool.com">info@flatironschool.com</a></dd> <dd>
<br>For press inquiries: <a href="mailto:press@flatironschool.com">press@flatironschool.com</a>
</dd> </dl> </div> <div class="column"> <strong class="title"><a href="http://blog.flatironschool.com/" target="_blank">From Our Blog</a></strong> <ul> <div id="top_blog_link"></div> <div id="bottom_blog_link"></div> </ul> </div> <div class="column"> <strong class="title"><a href="https://twitter.com/flatironschool" target="_blank">@FLATIRONSCHOOL</a></strong> <ul class="twitter"> <li><div id="tweet1"></div></li> <ul class="twitter" style="display:none"> <a class="twitter-timeline" data-dnt="true" href="https://twitter.com/FlatironSchool" data-chrome="nofooter transparent noborders noheader" data-tweet-limit="2" data-widget-id="536967261408727040"></a> </ul> </ul>
</div> </div> <div class="bar"> <ul class="social-networks"> <li> <a href="https://twitter.com/FlatironSchool" target="_blank"> <span class="icon-twitter"></span> </a> </li> <li> <a href="https://www.facebook.com/FlatironSchool" target="_blank"> <span class="icon-facebook"></span> </a> </li> <li> <a href="http://instagram.com/flatironschool" target="_blank"> <span class="icon-instagram"></span> </a> </li> <li> <a href="https://github.com/flatiron-school" target="_blank"> <span class="icon-github2"></span> </a> </li> <li> <a href="http://www.quora.com/Flatiron-School" target="_blank"> <span class="icon-quora"></span> </a> </li> </ul> <ul class="info"> <li>©2014 THE FLATIRON SCHOOl</li> <li>Made in NYC</li> <li><a href="/careers">CAREERS</a></li> <li><a href="/privacy-policy">PRIVACY POLICY</a></li> </ul> </div> </footer> </div> <script>
    (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
    (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
    m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
    })(window,document,'script','//www.google-analytics.com/analytics.js','ga');
 
    ga('create', 'UA-33578770-1', 'auto');
    ga('send', 'pageview');
    ga('send', 'newsitepageview');
  </script> <script>
    (function(d,s,i,r) {
      if (d.getElementById(i)){return;}
      var n=d.createElement(s),e=d.getElementsByTagName(s)[0];
      n.id=i;n.src='//js.hs-analytics.net/analytics/'+(Math.ceil(new Date()/r)*r)+'/69751.js';
      e.parentNode.insertBefore(n, e);
    })(document,"script","hs-analytics",300000);
  </script> <div style="display:none;"> <script>
    /* <![CDATA[ */
    var google_conversion_id = 962281709;
    var google_custom_params = window.google_tag_params;
    var google_remarketing_only = true;
    /* ]]> */
    </script> <script src="//www.googleadservices.com/pagead/conversion.js"> </script> <noscript> <div style="display:inline;"> <img height="1" width="1" style="border-style:none;" alt="" src="//googleads.g.doubleclick.net/pagead/viewthroughconversion/962281709/?value=0&amp;guid=ON&amp;script=0"> </div> </noscript> </div> <script>
    setTimeout(function(){var a=document.createElement("script");
    var b=document.getElementsByTagName("script")[0];
    a.src=document.location.protocol+"//script.crazyegg.com/pages/scripts/0024/3946.js?"+Math.floor(new Date().getTime()/3600000);
    a.async=true;a.type="text/javascript";b.parentNode.insertBefore(a,b)}, 1);
  </script> </body> </html>
```

Gah! I know this looks awful. It kind of is. But don't worry! Nokogiri will help us parse this. What we're looking at here is all of the HTML that makes up the web page found at www.flatironschool.com. The massive lines above are actually a snapshot of that HTML converted into a structure of nested nodes by Nokogiri.

**What are Nested Nodes?**

Nested nodes refers to any tree of elements in which parent elements branch off to contain children elements. In fact, we've seen similarly nested structures before when we dealt with nested data structures like hashes. By creating a nested structure, Nokogiri allows us to do things like iterate over a collection of element from the HTML document and use brackets,`[]`, and dot notation to access elements within the nested structure.

**USING NOKOGIRI TO EXTRACT DATA**

Visit [this Flatiron School link](http://flatironschool.com/?__hstc=112149531.660f92f25f4668c70675340d010f296e.1447273179419.1456763942408.1456785661001.262&__hssc=112149531.1.1456785661001&__hsfp=3680126679) and use your browser's developer tools to inspect the page. (You can just right-click anywhere on the page and select "inspect element".)

You should see something like this:

![flatiron-web](http://readme-pics.s3.amazonaws.com/Screen%20Shot%202015-08-19%20at%205.58.16%20PM.png)

The element inspector view on the bottom half of the page is revealing all of the page's HTML to us! In fact, the HTML it is showing us is *exactly the same* as the HTML `put` out to our terminal with the help of Nokogiri and open-uri.

Now that we understand what Nokogiri is and seen how it opens the HTML that makes up a web page, let's look at how we use to actually scrape information.

**USING CSS SELECTORS TO GET DATA**

Nokogiri allows you to use CSS selectors in order to retrieve specific pieces of information out of an HTML document.

**What is a CSS Selector**

In the following code:

```
<div id="my-div">
  <p class="my-paragraph"></p>
</div>
```

The id and class attributes of the HTML elements are the CSS selectors. You would refer to the div with this selector: `#my-div` (using the `#` to indicate id), and the paragraph with this selector: `.my-paragraph` (using the `.` to indicate class).

**Nokogiri's `.css` Method**

Nokogiri's `.css` method can be called on the `doc` variable that we set equal to that giant string of HTML that Nokogiri retrieved for us. The `.css` method takes in an argument of the CSS selector you want to retrieve. Let's take a look.

**Choosing a CSS Selector**

How do we determine which selector to use to retrieve the desired information? Remember that the HTML document that Nokogiri retrieved for us to operate on is *exactly the same* HTML that makes up the web page. Let's go back to www.flatironschool.com and use the element inspector to find the selector of a certain piece of information:

![flatiron-350](http://readme-pics.s3.amazonaws.com/Screen%20Shot%202015-08-19%20at%206.11.34%20PM.png)

This nice, big, bold statement, "350+ lives changed, and counting.", looks like a pretty good candidate.

In order to identify its CSS selector, you click on the magnifying class icon on the top left of the element inspector view and hover it over the element we want to ID ("350+ lives changed, and counting.")

That highlights its HTML element for us. Notice that:

```
<span class="grey-text">...</span>
```

is highlighted in the above image. If you press the down carrot on that light, it will open up to show you what that element contains:

```
"350+ lives changed, and counting."
```

We found it! That text lives in a span whose class is `"grey-text"`. Now we're ready to use the `.css` method to grab the text we want.

**Calling the `.css` method**

In our `scraper.rb` file, we had the following code:

```
require 'nokogiri'
require 'open-uri'
 
doc = Nokogiri::HTML(open("http://flatironschool.com/"))
```

Let's call `.css` on doc and give it the argument of our CSS selector:

```
require 'nokogiri'
require 'open-uri'
 
doc = Nokogiri::HTML(open("http://flatironschool.com/"))
doc.css(".grey-text")
```

If we puts out the result of that method call:

```
puts doc.css(".grey-text")
```

We'd see something like this:

```
[#<Nokogiri::XML::Element:0x3ff8bdcc1a64 name="span" attributes=[#<Nokogiri::XML::Attr:0x3ff8bdcc19d8 name="class" value="grey-text">] children=[#<Nokogiri::XML::Text:0x3ff8bdcc12d0 "350+ lives changed,">, #<Nokogiri::XML::Element:0x3ff8bdcc11e0 name="br">, #<Nokogiri::XML::Text:0x3ff8bdcc0ee8 "and counting.">]>]
```

Okay, still kind of gross. But we're almost there. If you look closely at the element above, you'll notice this:

```
children=[#<Nokogiri::XML::Text:0x3ff8bdcc12d0 "350+ lives changed,">,
```

There's our text! Buried in there. To get it out, we can call `.text` on it:

```
doc.css(".grey-text").text
 => "350+ lives changed,and counting."
```

We did it! We used Nokogiri to get the HTML of a web page. We used the element inspector in the browser to ID the CSS selector of the data we wanted to scrape. We used the `.css` Nokogiri method, along with that CSS selector, to grab the element that contains our desired data. Finally, we used the `.text` method to retrieve the desired text.

This was only a brief introduction into the concept and mechanics of scraping. We'll be taking a closer look in the upcoming code along exercise. Keep in mind that scraping is difficult and takes a lot of practice.

**ITERATING OVER ELEMENTS**

Sometimes we want to get a collection of the same elements, so we can iterate over them.

Let's first get a list of the instructors from the www.flatironschool.com/team page.

```
require 'nokogiri'
require 'open-uri'
 
html = open("http://flatironschool.com/team")
doc = Nokogiri::HTML(html)
 
instructors = doc.css("#instructors .team-holder 
.person-box")
```

Even though the Nokogiri gem returns a `Nokogiri::XML::Element` (which looks like an array in ruby), we can use Ruby methods, such as `.each` and `.collect`, to iterate over it.

```
[#<Nokogiri::XML::Attr:0x3fcd82a22b84 name="class" value="icon-github2">]>]>]>, #<Nokogiri::XML::Text:0x3fcd82a238b8 " ">, #<Nokogiri::XML::Element:0x3fcd82a1feac name="li" children=[#<Nokogiri::XML::Element:0x3fcd82a1faec name="a" attributes=[#<Nokogiri::XML::Attr:0x3fcd82a1f8a8 name="href" value="http://twitter.com/aviflombaum">, #<Nokogiri::XML::Attr:0x3fcd82a1f894 name="target" value="_blank">] children=[#<Nokogiri::XML::Element:0x3fcd82a1ebb0 name="span" attributes=[#<Nokogiri::XML::Attr:0x3fcd82a1eb10 name="class" value="icon-twitter2">]>]>]>, #<Nokogiri::XML::Text:0x3fcd82a1be88 " ">, #<Nokogiri::XML::Element:0x3fcd82a1bd20 name="li" children=[#<Nokogiri::XML::Element:0x3fcd82a1b8d4 name="a" attributes=[#<Nokogiri::XML::Attr:0x3fcd82a1b85c name="href" value="http://www.facebook.com/aviflombaum">, #<Nokogiri::XML::Attr:0x3fcd82a1b848 name="target" value="_blank">] children=[#<Nokogiri::XML::Element:0x3fcd82a1ad58 name="span" attributes=[#<Nokogiri::XML::Attr:0x3fcd82a1acf4 name="class" value="icon-facebook2">]>]>]>, #<Nokogiri::XML::Text:0x3fcd82a1a470 " ">, #<Nokogiri::XML::Element:0x3fcd82a1a394 name="li" children=[#<Nokogiri::XML::Element:0x3fcd82a1a0d8 name="a" attributes=[#<Nokogiri::XML::Attr:0x3fcd82a1b9d8 name="href" value="http://www.linkedin.com/in/aviflombaum">, #<Nokogiri::XML::Attr:0x3fcd82a1a9e8 name="target" value="_blank">] children=[#<Nokogiri::XML::Element:0x3fcd82a17734 name="span" attributes=[#<Nokogiri::XML::Attr:0x3fcd82a176bc name="class" value="icon-linkedin2">]>]>]>, #<Nokogiri::XML::Text:0x3fcd82a16e38 " ">]>, … ]
```

Let's iterate over the instructors array by using `.each` and `puts` `"Flatiron School <3 "` proceeded by a instructors name.

```
instructors.each do |instructor| 
  puts "Flatiron School <3 " + instructor.css("h2").text
end
```

We'd see something like this:

```
Flatiron School <3 Avi Flombaum
Flatiron School <3 Joe Burgess
…
…
…
```

**ADVANCED: OPERATING ON XML**

Let's take another look at the element returned to us by our call on the `.css` method:

```
[#<Nokogiri::XML::Element:0x3ff8bdcc1a64 name="span" attributes=[#<Nokogiri::XML::Attr:0x3ff8bdcc19d8 name="class" value="grey-text">] children=[#<Nokogiri::XML::Text:0x3ff8bdcc12d0 "350+ lives changed,">, #<Nokogiri::XML::Element:0x3ff8bdcc11e0 name="br">, #<Nokogiri::XML::Text:0x3ff8bdcc0ee8 "and counting.">]>]
```

This is an XML element. XML stands for Extensible Markup Language. Just like HTML, it is a set of rules for encoding and displaying data on the web. When we use Nokogiri methods, we get a return value of XML elements, collected into an array. Technically, methods like `.css` return a Nokogiri data object that is a collection of Nokogiri::XML::Element objects that *functions* like an array.

The main thing to understand, however, is that Nokogiri collects these objects into hierarchical data structures, much like the nested arrays and hashes we've been building and manipulating for a while now. So, we could iterate over an array of Nokogiri objects, use enumerators, grab the values of attributes that act as hash keys, etc. We'll get practice with all of this in the upcoming exercise.


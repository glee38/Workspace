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
3. Understand how they are related to authentication


###Cookies

Imagine going into Facebook. You type `www.facebook.com` into your browser and are prompted to login. You do so happily, as you cannot wait to procrastinate for a few hours by accidentally creeping on people and reading a myriad of mundane posts.

Facebook logs you in. You are redirected to your homepage, where you see some breaking news - John's dating WHO?! You click on the post in your newsfeed only to be prompted to login again. Ugh. Again? You quickly login again because your nosiness just *needs* to know. Facebook takes you to the post. You read it, tell yourself Megan is basic anyway so who cares, and decide you want to check out some other posts. You click on the link to your newsfeed and there it is again. A prompt to login.

My goodness, you just wanted to stalk people in peace, and now this? What the http is going on?!

**WHAT IS A COOKIE?**

Cookies are used to maintain some state between the client (your browser, e.g., Google Chrome) and the origin server (where you are sending your requests to be fulfilled - in our example's case, Facebook). 

**WHY THEY ARE USEFUL**

In the scenario above, every time you click on a different link or part of the Facebook website, you are sending a new request to Facebook and therefore, being redirected to login every time. This is because HTTP requests are built to be a "stateless protocol". This means that the server processes each request separately from one another - the "state" of the first request where you logged in, is only longer maintained when you request to go to another page. 

Thus, every time you ask your browser to load a different page in Facebook, Facebook will ask you to login again because it *doesn't* remember the fact that you already logged in during a previous request.

This is where cookies come in. To solve this problem, cookies are created and stored in your browser when you visit a website that uses cookies to maintain state. The cookie is essentially a small text file that contains information about you as a user, such as your user id, what ads you click on, and more.

Cookies provide a way for a website to verify who a user is once, and maintain that state for the rest of your visit on that site, or until you logout. Ah, finally. Now you can creep happily ever after.







#AuthSeries Pt. 2: Creating your own authentication

Welcome to **Part 2** of AuthSeries, a series dedicated to authentication in Rails. In today's post, I will go over how to go about creating your own authentication logic from scratch. Rails has a lot of useful gems that get this job done for you, but I think it's always useful to understand what is going on under the hood before using gems that abstract away these basic, but extremely important principles.


#Sessions
##Sessions and Cookies
####HOW DO YOUR WEBSITES RECOGNIZE YOU?
When you visit Facebook, chances are, you'll find you're logged in and be taken to your timeline directly. If you check your Gmail account, you might see advertisements relating to search terms you typed into Google earlier that day. When you shop on Amazon, your shopping cart stays full while you continue to shop all the way up until you checkout. How is this possible? Is your computer spying on you all the time? The answer is, sort of.

Before we can understand what is so amazing about the kind of persistence and recognition we've come to expect from our web applications, we need to understand something about how the web works.

> HTTP is a stateless protocol, treating each request as an independent transaction that is unable to use information from any previous requests. This means there is no way within the hypertext transfer protocol to remember a user’s identity from page to page...

What? Then how does Facebook remember me?

> instead, web applications requiring user login must use a session, which is a semi-permanent connection between two computers (such as a client computer running a web browser and a server running Rails).
–– Michael Hartl, The Ruby on Rails Tutorial

Since HTTP is stateless, we need the browser to be responsible for holding some sort of simple state. On each HTTP request, the server sends some information for the browser. Cookies were created to store that data. A **cookie** is effectively just storing a hash on the browser that then gets sent with every request.

Servers then realized that there was a use case for just storing data very temporarily just while a user is using their website. We call that one usage a session.

A **session** is simply an object, like a hash, that stores data describing a client's interaction with a website at a given point in time. The session hash lives on the server. Your application can access it in any of your controllers at any point in time.

So, how does a web application know who is interacting with it? By using **cookies and session data**.

When you send a request to http://www.facebook.com, the application, on the server, will generate and store some data about you. The application will keep that data in a session hash and it will put that data into a small text snippet called a cookie. The cookie gets sent back to you, the client, and is stored in your browser. Cookies persist from one page to the next when you are browsing the web and are sent back to the application on the server any subsequent time you send another request to http://www.facebook.com. When that happens, the cookie data will be compared to the session data to help the application determine what steps to take and what information to send back to you.

####HOW DO COOKIES WORK?
Here's the general flow:

1. You visit a webpage, www.facebook.com

2. The web application that is Facebook receives that request and creates a cookie with some information about you as a user.

3. Facebook sends that cookie back to the browser where it is stored temporarily. The cookie will last until you log out of Facebook, close your browser or clear your cache.

4. Every subsequent request you, the client, sends to http://www.facebook.com sends the cookies back to the server where Facebook can access it, get information out of it and alter it.

####WHAT IS CONTAINED IN A COOKIE?
Cookies will usually have a URL to the website that generated it, the date on which it was created (and the date on which it expired, if applicable), and other pertinent information that has been requested by the web application (such as remembered login information, user preferences, etc).

####SESSION COOKIES VS. PERSISTENT COOKIES
There are two primary kinds of cookies: session and persistent.

**Session cookies** allow a website to keep track of your movement from page to page for that specific session (from the time you log in to the time you log out/close the browser). Session cookies simply allow you to navigate through a site without repeatedly having to authenticate yourself on every new page you visit within the web application domain. Session cookies expire every time you log out or navigate away from the website.

Let's walk through an example of what would happen without a session cookie. If you were on Amazon, added an item to your shopping cart, and then selected the "Checkout" option, the new page would have no recollection of your prior activity. Your shopping cart would always be empty, even though you explicitly added an item earlier. There would be no record of your activities for that session.

Later on, we'll learn how to use the cookie and session objects to build apps that allow users to log in and log out.

**Persistent cookies** allow a website to remember your user information and preferences for *future* visits. A persistent cookie is stored on your computer, while a session cookie is temporarily stored in your web browser. Persistent cookies allow for faster page loading, automatic login and user authentication, and access to other potential web application features because much of the data that these features require will be hanging out in a persistent cookie, stored on your computer. This means you don't have to go through the cycle of sending a web request to a site and waiting to receive data back from the server in order for your browser to have your login info, for example. Persistent cookies are usually created on the first visit to the page after you have created an account, and typically don't expire unless the web application has a protocol in place for cookie expiration, or the user clears their browser's cookie cache.

What would happen if we didn't have persistent cookies? We would have to go to Facebook's login page and type in our email address and password every single time we wanted to visit Facebook. Our user settings and preferences would not be remembered, and the quality of the user's experience would go way down.

####COOKIES AND SESSIONS WORK TOGETHER
You can think of cookies as client-side counterparts to sessions. They store information in your browser or on your computer and are visible only to the server that created them. Client browsers send cookies to remote servers along with each web request. Then the web application on the server can read the cookie, associate it with an existing session (if any) and decide how to respond. For example, if there is no cookie received, the application might show the login page. If a cookie is received and it's data doesn't match the data stored in the session on the server-side of the application, the app might show the login page with the user name pre-filled, having gotten that data from the cookie. If a cookie is received that does match the data in a session on the server-side of the application, the app might respond with that user's data.

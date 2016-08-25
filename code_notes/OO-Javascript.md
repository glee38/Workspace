#Object Oriented Javascript
##Intro to OO-JS
####OBJECTIVES
+ Explain what an object is in JavaScript
+ Explain the parts of a JS object
+ Create an object in JS using literal syntax

####RUBY VERSUS JAVASCRIPT
You've already worked with objects in JavaScript, but so far we've only treated them like hashes. Now it's time to change frame of mind and start to view them as objects with properties and values, just like the objects we make in Ruby using classes.

This frame of reference from Ruby is going to make dealing with objects in JavaScript that much easier. You already know what an object is, so now it's time to learn how JavaScript handles them.

Let's create a user class and object in Ruby. The user will have a `name` and `email` attribute:

```ruby
class User
  attr_accessor :name, :email
 
  def initialize(name, email)
    @name = name
    @email = email
  end
end
 
kevin = User.new("kevin", "kevin@aol.com")
```

Now let's recreate the same thing in JS:

```javascript
var kevin = {
  name: "kevin",
  email: "kevin@aol.com"
}
```

####PROPERTIES AND DATA
How come when we create our `kevin` JavaScript object, it looks just like a hash but in Ruby our `kevin` object looks more like what we typically think of as an object?

Objects in Javascript are a lot more versatile than they are in Ruby. Essentially hashes in Javascript are objects, but they can also have functions attached to them (methods). In Ruby we think of Objects and Hashes as fundamentally different ideas (technically Hashes in Ruby are objects). However, in Javascript, we can use objects like Hashes, just as a set of key value pairs, or we can attach functions to them and use them more like we traditionally think of using objects in Ruby. In Ruby objects contain both data and behavior and we'll soon learn to use our Javascript objects in the same manner.
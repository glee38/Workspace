##Mass Assignment
###Introduction

At this point, we're very familiar with the fact that methods can be defined to take in arguments. We also know that methods can be defined to take in multiple arguments. Let's take a look at an example:

```
def print_name_and_greeting(greeting, name)
  puts "#{greeting}, #{name}"
end
 
print_name_and_greeting("'sup", "Hillary Clinton")
  "'sup, Hillary Clinton"
  => nil
```

As it currently stands, whoever uses our method needs to remember exactly what order to pass in the arguments. They need to know that the first argument is a greeting and the second argument is a name. What happens if they forget? What happens if another developer who is working on our code base doesn't see the file where the method is defined, but only sees the method being invoked? Maybe it's not clear to them which argument is which––after all, when you invoke methods, the arguments aren't labeled or anything. Let's take a look at what type of disaster would befall us:

```
print_name_and_greeting("Kanye", "hello")
  "Kanye, hello"
  => nil
```

That would be a weird way to greet Kanye. Let's take a look at another example, this time using arguments of two different data types:

```
def happy_birthday(name, current_age)
  puts "Happy Birthday, #{name}"
  current_age += 1
  puts "You are now #{current_age} years old"
end
 
happy_birthday("Beyonce", 31)
  Happy Birthday, Beyonce
  You are now 32 years old
  => nil
```

But what happens if we accidentally pass the arguments in in the wrong order?

```
happy_birthday(31, "Beyonce")
  Happy Birthday, 31
  TypeError: no implicit conversion of Fixnum into String
```

Oh no! We broke our program! Clearly, we have a need to regulate the passing in of multiple arguments. It would be especially helpful if we could *name* the arguments that we pass in, *when we invoke the method*. Guess what? We can! (Okay, you probably saw that one coming).

###Keyword Arguments

Keyword arguments are a special way of passing arguments into a method. They behave like hashes, pairing a key that functions as the argument name, with it's value. Let's walk through it together and refactor our `happy_birthday` method:

```
def happy_birthday(name: "Beyonce", current_age: 31)
  puts "Happy Birthday, #{name}"
  current_age += 1
  puts "You are now #{current_age} years old"
end
```

Here, we've defined our method to take in keyword arguments. Our keyword arguments consist of two key/value pairs, `:name` and `:age`. We've given our keyword arguments default values of `"Beyonce"` and `31`, but we didn't have to:

```
def happy_birthday(name:, current_age:)
  puts "Happy Birthday, #{name}"
  current_age += 1
  puts "You are now #{current_age} years old"
end
```

Notice that we can reference `name` and `age` inside our method body, as if they were barewords, *even though they are the keys in our argument hash*. That's how keyword arguments work, they allow you to name the arguments that you pass in as keys in a hash. Then, the method body can use the values of those keys, referenced by their name. Let's call our method:

```
happy_birthday(current_age: 31, name: "Carmelo Anthony")
 "Happy Birthday, Carmelo Anthony"
 "You are now 32 years old"
```

Notice that even though we changed the order of our key/value pairs, our method didn't break! Not only is this method more robust (i.e. more resistant to breakage) than the previous one, it is also more explicit. Anyone looking at it's invocation can tell exactly what kind of data you are passing in.

**MASS ASSIGNMENT**

Another benefit of using keyword arguments is the ability to "mass assign" attributes to an object. Let's revisit our `Person` class from an earlier lesson. We'd like to initialize individual people with a name and an age:

```
class Person
  attr_accessor :name, :age
 
  def initialize(name, age)
    @name = name
    @age = age
  end
end
```

As it stands, our initialize method is vulnerable to the same issues we discussed above. Let's refactor it to take in a person's attributes as keyword arguments:

```
class Person
  attr_accessor :name, :age
 
  def initialize(name:, age:)
    @name = name
    @age = age
  end
end
```

Now, we have the added benefit of being able to use something called **mass assignment** to instantiate new people objects. If a method is defined to accept keyword arguments, we can create the hash that the method is expecting to accept as an argument, set that hash equal to a variable, and simply pass that variable in to the method as an argument. Let's take a look:

```
person_attributes = {name: "Sophie", age: 26}
sophie = Person.new(person_attributes)
=> #<Person:0x007f9bd5814ae8 @name="sophie", @age=26>
```

This might not seem particularly useful now, but when we start building web applications, we'll understand more about how necessary this trick really is. For now, just take our word for it.

##Mass Assignment and Metaprogramming
###Introduction

You might recall that metaprogramming is the practice of writing code that writes code for us. So, what does that have to do with mass assignment? Let's revisit our Twitter API challenge from an earlier lesson. We've learned that we can write a Ruby program that sends a web request to an API and returns particular data to the program.

Let's say we want to use the Twitter API to create users for our own application. The scenario is that we are developing a web application and we want our users to be able to sign in via Twitter. Thus, our own users are pulled from Twitter and we need to take the data we get from Twitter––for example a user's name, age and location, and use them to make instances of our own `User` class. Let's take a look at a code snippet:

```
class User
  attr_accessor :name, :age, :location, :user_name
 
  def initialize(user_name:, name:, age:, location:)
    @user_name = user_name
    @name = name
    @location = location
    @age = age
  end
end
```

Here we have our `User` class. It initializes with keyword arguments, i.e., a hash of attributes. For the purposes of this example, we won't get into the specifics of how we request and receive data from the Twitter API. Suffice to say that we send a request to the Twitter API and get a return value of a hash full of user attributes. For example:

```
twitter_user = {name: "Sophie", user_name: "sm_debenedetto", 
age: 26, location: "NY, NY"}
```

With what we've learned of mass assignment so far, we can use the `twitter_user` hash to instantiate a new instance of our own `User` class:

```
sophie = User.new(twitter_user)
 => #<User:0x007fa1293e68f0 @name="Sophie", @age=26, 
 @user_name="sm_debenedetto", @location="NY, NY"> 
```

So far so good. But, what if Twitter changes their API without telling us? (How could they? Don't they know who we are?). After all, we are not in charge of Twitter or their API, they can do whatever they want, whenever they want, with no regard to our application which relies on their data. Let's say Twitter makes a change that we're unaware of. Now when we request data from their API, we get this return value:

```
new_twitter_user = twitter_user = {name: "Sophie",
 user_name: "sm_debenedetto", location: "NY, NY"}
```
Notice that the `twitter_user` no longer has an age. Let's see what happens if we try to create new `Users` using the same old `User` class code:

```
User.new(new_twitter_user)
=>ArgumentError: missing keyword: age
```

Our program broke! Let's play it with another scenario. Let's say the Twitter API changed and now returns data to us in the following manner:

```
newest_twitter_user = {name: "Sophie", user_name: 
"sm_debenedetto", age: 26, location: "NY, NY", 
bio: "I'm a programmer living in NY!"}
```
Now let's see what happens when we try to make a new instance of our `User` class with the same old `User` class code:

```
User.new(newest_twitter_user)
=> ArgumentError: unknown keyword: bio
```

Our program breaks! Clearly, we need a way to abstract away our `User` class' dependency on specific attributes. If only there was a way for us to tell our `User` to get ready to accept some unspecified number and type of attributes.

###Mass Assignment and Metaprogramming

Guess what? We can achieve exactly that goal using metaprogramming and mass assignment. Let's take a look at how it's done, then we'll break it down together. Here's our new and improved `User` class:

```
class User
  attr_accessor :name, :use_name, :age, :location, :bio
 
  def initialize(attributes)
    attributes.each {|key, value| self.send(("#{key}=")
    , value)}
  end
end
```

We define our initialize method to take in some unspecified `attributes` object. Then, we iterate over each key/value pair in the attributes hash. The name of the key becomes the name of a setter method and the value associated with the key is the name of the value you want to pass to that method. The ruby `.send` method then calls the method name that is the key’s name, with an argument of the value. In other words:

```
self.send(key=, value)
```

Is the same as:

```
instance_of_user.key = value
```

Where each key/value pair is a member of our hash, one such iteration might read:

```
...
instance_of_user.name = "Sophie"
...
```

And have the same result as:

```
instance_of_user = User.new
instance_of_user.name = "Sophie"
```

**A CLOSER LOOK AT `.SEND`**

The `.send` method is just another way of calling a method on an object. For example, we know that instances of the `User` class have a `.name=` method that allows us to set the name of a user to a particular string:

```
sophie = User.new
sophie.name = "Sophie"
```

Now, when we use the `.name` getter method, it will return the correct name:

```
sophie.name 
  => "Sophie"
```

Let's look at the same behavior using `.send`

```
sophie = User.new
sophie.send(name=, "Sophie")
```

That is generally considered to be clunky and ugly. It's whats known as "syntactic vinegar". We prefer the "syntactic sugar" of the first approach.

The `.send` method, however, is a very useful tool for our metaprogramming purposes. It allows us to abstract away the specific method call:

```
sophie = User.new
sophie.send("#{method_name}=", value)
```

This is exactly what's happening in our initialize method in the example above, where `self` refers to the `User` instance that is being initialized at that point in time.

###Why is this useful?

With this pattern, we have made our code much more flexible. We can easily alter the number of attributes in the class and change the hash that we initialize the class with, *without editing our initialize method*. Now, we're programming for the future. If and when that data with which we want to initialize our class changes, we *only have to change our attr_accessors*. Our initialize method is flexible and we can leave it alone. That is one major goal of design in object oriented programming––the writing of code that accommodates future change, the writing of code that doesn't require a lot of modification, even as it grows.
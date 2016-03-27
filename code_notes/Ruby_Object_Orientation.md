#Ruby: Object Orientation

##Object-Oriented Programming (OOP)

A code object representing a water pipe (instead of a smoking pipe) might contain values for `length`, `diameter`, `material`, and `manufacturer`. The bundling of these individual pieces of information together begins to form a larger whole.

**Object-Oriented Programming**, however, does more than just bundling up individual pieces of data that represent a "thing" — it also bundles customized functions that can be performed on that data. These are called *methods*: behaviors that an object performs upon its internal data and even upon other code objects.

**An object in code is a thing with all the data and all the logic required to complete a task.** Objects are models and metaphors for the problems we solve in code.

##CLASS

**A Ruby class contains the instructions for creating new objects and it has the ability to create those objects.**


Ruby comes with a few types of Objects to get us started, things like `Integer`, `String`, `Array`, etc. We call these base types of Objects "Primitives". But what if we wanted to add a new kind of thing to our programming universe, a new kind of object we can create in our code? That's what the `class` keyword and object orientation allows us to do.

###Defining a Class
Here's what our `Dog` class would look like:

```ruby
class Dog
  # some code to describe a dog
end
```

The `Dog `class is defined with the `class` keyword, followed by the class name and closed with an `end`. The body of this class is between the `class` and `end` keywords.

Class names begin with capital letters because they are stored in Ruby constants. Constants may be defined within classes, but unlike instance variables, they are accessible outside the class. If your class name contains two words, the name should be CamelCased, like this:

```ruby
class MyClass
  # some code all about your awesome class
end
```

With this code alone, we can now make new dogs!

###Creating Instances of Classes

```ruby
class Dog
end

fido = Dog.new
fido #=> #<Dog:0x007fc52c2d7d20>
```

In the code sample above, once we've defined our `Dog` class with the `class` keyword, we immediately can bring to life new individual dogs, the variable `fido` which points to a new instance of a dog.

On the `Dog` class, we call the `.new` method and that will *instantiate* a new dog.

**Instantiate means bringing a new object to life**, a new individual, like a particular dog, like Snoopy or Lassie or Rover. Each particular dog is an individual that was *instantiated* when we called `Dog.new` to birth it into our world of programming.

We call these individuals, each specific dog or version of our class, *instances*. **An instance is a single occurrence of an object. Instances refer to the individual objects produced from the factory that is the class.**

```ruby
class Dog
end

fido = Dog.new
fido #=> #<Dog:0x007fc52c2d7d20>

snoopy = Dog.new
snoopy #=> #<Dog:0x007fc52c2d4170>
```

`snoopy` and `fido` are two different variables pointing at separate instances of the `Dog` class.

###DIFFERENT INSTANCES ARE DIFFERENT OBJECTS

Let's make three dogs:

```ruby
class Dog
end

fido = Dog.new
fido #=> #<Dog:0x007fc52c2d7d20>

snoopy = Dog.new
snoopy #=> #<Dog:0x007fc52c2d4170>

lassie = Dog.new
lassie #=> #<Dog:0x007fc52c2cc588>
```

Notice that every time you make an instance of a class, Ruby tells you that the return value is something that looks like `#<Dog:0x007fc52c2cc588>`. This syntax is called **Ruby Object Notation** and it's just the default way that Ruby communicates to you that you are dealing with an object or instance of a particular class. The `Dog:0x007fc52c2cc588` tells you that the object is an instance of `Dog` and the `0x007fc52c2cc588` is called it's object identifier and it basically means this is where the object lives inside your computer.

Each of these instances are totally unique, even though they are all born from `Dog`.

```ruby
class Dog
end

fido = Dog.new
fido #=> #<Dog:0x007fc52c2d7d20>

snoopy = Dog.new
snoopy #=> #<Dog:0x007fc52c2d4170>

snoopy == fido #=> false - these dogs are not the same.
```

Classes are factories for our objects. They let us manufacture and instantiate new instances.

##INSTANCE METHODS
###The Behavior of Objects

We know that classes act as a factory for our objects, capable of instantiating new instances.

```ruby
class Dog
end

fido = Dog.new #=> #<Dog:0x007fc52c2cc588>
```

But what can this instance of a dog stored in the local variable `fido` do? In fact, how do we even ask this object to do something?

###DOT NOTATION

We send objects messages asking them to perform an operation or task through a syntax known as "Dot Notation".

```ruby
class Dog
end

fido = Dog.new #=> #<Dog:0x007fc52c2cc588>

fido.object_id #=> 70173135795280
```

In the example above, we send the `fido` instance a message `object_id` by separating the receiving object, `fido` and the message, `object_id` by a dot (`.`). When we send an object a message through dot notation, we are evoking the corresponding method on the object. We are calling the `object_id` method on `fido`. 

The `#object_id` method simply tells you the object's identifier in your computer's memory (the place that all things live in your computer).

**In dot notation, we call the object that received the method message the "receiver" and we call the method the "message".**

```ruby
# The receiver is this very string      # reverse is 
                                          the message
"Strings are instances and objects too".reverse

```

###INSTANCE METHODS

All objects respond to methods and messages, like `#object_id` in the example above. One interesting method provided is the `#methods` method that returns an array of all the methods and messages an object responds to. We can evoke this method via dot-notation. One of the great things you can ask every object in ruby is "What methods do you respond to?"

```ruby
class Dog
end

fido = Dog.new
fido.methods
#=> [:psych_to_yaml, :to_yaml, :to_yaml_properties, :local_methods, :try, :nil?,
# :===, :=~, :!~, :eql?, :hash, :<=>, :class, :singleton_class, :clone, :dup,
# :itself, :taint, :tainted?, :untaint, :untrust, :untrusted?, :trust, :freeze,
# :frozen?, :to_s, :inspect, :methods, :singleton_methods, :protected_methods,
# :private_methods, :public_methods, :instance_variables,
# :instance_variable_get, :instance_variable_set, :instance_variable_defined?,
# :remove_instance_variable, :instance_of?, :kind_of?, :is_a?, :tap, :send,
# :public_send, :respond_to?, :extend, :display, :method, :public_method,
# :singleton_method, :define_singleton_method, :object_id, :to_enum, :enum_for,
# :==, :equal?, :!, :!=, :instance_eval, :instance_exec, :__send__, :__id__]
```

###BUILDING YOUR OWN INSTANCE METHODS

How do we add our own methods to our classes? In our Dog example, can we teach our Dog a new trick? Can we teach our Dog to bark for example?

We can. We're used to defining methods already with the `def` keyword. If we place this method definition within the body of a class, that method becomes a specific behavior of instances of that class, not a generic procedure we can just call whenever we want.

We call the methods defined within the object's class **Instance Methods** because they are methods that belong to any instance of the class.

```ruby
class Dog
  # Class body

  # Instance Method Definition
  def bark
    puts "Woof!"
  end
end

fido = Dog.new
fido.bark #> "Woof!"
```

By defining `#bark` within the `Dog` class, `bark` becomes a method of all instances of Dogs. If we make more dogs, they can all bark.

```ruby
class Dog
  def bark
    puts "Woof!"
  end
end

fido = Dog.new
fido.bark #> "Woof!"

snoopy = Dog.new
snoopy.bark #> "Woof!"
```

Objects can only do what we teach them to do via the code we write and the methods we define. For example, currently, Dogs do not know how to sit.

```ruby
class Dog
  def bark
    puts "Woof!"
  end
end

fido = Dog.new
fido.bark #> "Woof!"
fido.sit # NoMethodError: undefined method `sit' for
 #<Dog:0x007fa4e9a9e8a0>
```

In the same manner, instance methods, the methods that belong to particular instances of particular classes, are not globally evocable like procedural methods. They cannot be called without an instance.

```ruby
class Dog
  def bark
    puts "Woof!"
  end
end

fido = Dog.new

# Let's try just calling bark without fido
bark # NameError: undefined local variable or method
 `bark' for main:Object
```

###CLASSES AS BLUEPRINTS

The ability to define methods and behaviors in our classes for our instances makes Ruby classes behave not just as factories, capable of instantiating new individual instances, but also as a blueprint, defining what those instances can do.

##Instance Variables
###What Is An Instance Variable?
We've been working with variables for a while now. For example:

```
bro_greeting = "Sup, bro?"
```

The code above sets a variable, `bro_greeting`, equal to the string `"Sup, bro?"`. Now we can use that variable to read and operate on that string.

```
bro_greeting #=> "Sup, bro?"

bro_greeting.upcase #=> "SUP, BRO?"
```

The `bro_greeting` variable is what's known as a **local variable, so named because it can only be accessed in a specific, local environment.**

A local variable that is defined inside one method, for example, cannot be accessed by another method. In order to get around this limitation, we can use **instance variables** inside our Ruby classes.

**An instance variable is a variable that is accessible in any instance method in a particular instance of a class.**

###We Need Instance Variables

Let's say we have a class called `Dog` that is responsible for producing individual dog objects. We want each dog to be able to have a name and show its name. So we need to write some methods:

```
class Dog
  def name=(dog_name)
    this_dogs_name = dog_name
  end

  def name
    this_dogs_name
  end
end
```

Here we've defined two instance methods, the `name=`, or "name equals" method, and the `name` method. The first method takes in an argument of a dog's name and sets that argument equal to a variable, `this_dogs_name`. The second method is responsible for reporting, or reading the name. The methods act as mechanisms to expose data from inside of the object to the outside world.

Our two methods therefore are responsible for "setting" and "getting" an individual dog's name.

Here's how it *should* work in practice:

```
lassie = Dog.new
lassie.name = "Lassie"

lassie.name #=> "Lassie"
```

Run the file in your terminal by typing `ruby dog.rb`. You should see an error message, with the following snippet inside it:

```
 `name': undefined local variable or method `this_dogs_name''
```

Uh-oh. Looks like the `#name` method doesn't know about the `this_dogs_name` variable from the `#name=` method. That is because `this_dogs_name` is a **local variable**. A local variable has a **local scope**. That means that it cannot be accessed outside of the method in which it is defined.

###Implementing Instance Variables

We define an instance variable by prefacing the variable name with an `@` symbol.

Instance variables are bound to an instance of a class. That means that the value held by an instance variable is specific to whatever instance of the class it happens to belong to. Instance variables hold information about an instance, usually an attribute of that instance, and can be called on throughout the class, without needing to be passed into other methods as arguments (as would be the case with local variables).

Let's refactor our `Dog` class to use an instance variable instead of a local variable to set and get an individual dog's name.

Open up `dog.rb` and change the `Dog` class in the following way:

```
class Dog

  def name=(dogs_name)
    @this_dogs_name = dogs_name
  end

  def name
    @this_dogs_name
  end
end

lassie = Dog.new
lassie.name = "Lassie"

puts lassie.name
```

Run the file again by typing `ruby dog.rb` in your terminal and you should see `Lassie` outputted to your terminal.

It worked! Why did it work? Inside the `#name=` method, we set the value of `@this_dogs_name` equal to whatever string is passed in as an argument. Then, we are able to call on that same instance variable in a totally separate method, the `#name` method.

###Conclusion

As we dive deeper into object oriented Ruby, we'll be using instance variables frequently to pass information around the instance methods of a class. Think of instance variables as the containers for instance-specific information. The ability of instance variables to store information and be accessible within different instance methods is one of the things that makes it possible for us to create similar, but unique objects in object orientated Ruby.

##Class Variables and Methods

###Why Use Class Variables and Methods

Let's say you wanted to keep a counter for how many albums you had in your music collection. That way, you can brag to your friends about what a music aficionado you are. The current code in our `Album` class has no way to keep such a count. Looks like we will have to write some code to accommodate this new feature of our program.

When it comes to adding new features or functionalities to our code, we start out by asking a question: whose responsibility is it to enact this behavior or functionality?

Right now, our program is pretty simple. We have an `Album` class and we have album instances. So, is it the responsibility of an individual album to keep a count of all of the other albums? Or is it the responsibility of the `Album` class, which actually produces the individual albums, to keep a running count? I think we can agree that it *isn't* the job of the individual albums, but the job of the `Album` class to keep a count of all of instances it produces.

Now that we've decided whose job it is to enact the "keep a count of all albums" behavior, we can talk about *how* we enact that behavior.

We do so with the use of class variables and methods. Our goal is to be able to ask the `Album` class: "how many albums have you produced?" When we ask an object to tell us something about itself, we use methods. It would be great if we could do something like:

```
Album.count
```

and return the number of existing albums. Let's build out this capability now.

###Building Class Methods and Using Class Variables

An instance variable is responsible for holding information regarding an instance of a class and is accessible only to that instance of the class. A class variable is accessible to the entire class––it has **class scope**. A class method is a method that is called on the class itself, not on the instances of that class. Class variables store information regarding the class as a whole and class methods enact behaviors that belong to the whole class, not just to individual instances of that class.

**DEFINING A CLASS VARIABLE**

A class variable looks like this: ```@@variable_name```. Just like an instance or a local variable, you can set it equal to any type of data.

Let's create a class variable, ```@@album_count``` and set it equal to ```0```.

```
class Album
 
  @@album_count = 0
 
  def release_date=(date)
    @release_date = date
  end
 
  def release_date
    @release_date
  end
end
```

Great, now we have a class variable to store our count of albums in. We can't yet access that variable from outside of our class though. How can we expose the contents of that variable? With a class method.

**DEFINING A CLASS METHOD**

A class method is defined like this:

```
def self.class_method_name
  # some code
end
```

Here, the `self` keyword refers to the entire class itself, *not to an instance of the class*. In this case, we are inside the class only, not inside an instance method of that class. So, we are in the class scope, not the instance scope.

Let's define a class method `.count` that returns the current count of albums.

```
class Album
  @@album_count = 0
 
  def self.count
    @@album_count
  end
end
```

Great, now if we call:

```Album.count```

It will return `0`.

**OPERATING ON A CLASS VARIABLE INSIDE AN INSTANCE METHOD**

Currently, however, our `@@album_count` is stuck at `0`. When and how should we increment it? The count of albums should go up as soon as a new album is created, or initialized. We can hook into this moment in time in our `#initialize` method.

```
class Album
  @@album_count = 0 
 
  def initialize
    @@album_count += 1
  end
 
  def self.count
    @@album_count
  end
end
```

Here we are using the `@@album_count` class variable, inside of our `#initialize` method, which is an instance method. We are saying: when a new album is created, access the `@@album_count` class variable and increment its value by `1`.

We can access our class variables anywhere in our class: in both class and instance methods.

Now our code should behave in the following manner:

```
Album.new
Album.new
Album.new
 
Album.count
  # => 3
```
##Public vs. Private Methods

**PUBLIC METHODS**

We've already been writing public methods: **instance** & **class methods**. We can call them from outside the scope of the class declaration, like on the instance of the class or the class itself. Public methods are called by an *explicit* receiver: **the instance of the object explicitly receives the method**.

**PRIVATE METHODS**

Private methods cannot be called by an explicit receiver. What does that mean? It means we can only call private methods within the context of the defining class: the receiver of a private method is always `self`.

**WHY USE PRIVATE METHODS?**

Private methods help us to obscure information and functionality from the user. For example, let's say we have an application on the web. Users need to sign in to our application. A user will input their username and password to do so. The action of taking in that password and using it to log someone in would be a good candidate for a private method. We would never want someone to be able to call user.password and reveal our users' super-secret password.

There is another reason to designate a method as private. Doing so will signal to other developers that this method is depended on by other methods in your program. It signals that they should beware of refactoring such a method for fear of breaking other parts of the program that they may not realize rely on it.

**BUILDING PRIVATE METHODS**

We've already written a private method in our `Cat` class: `initialize`.

```
bub.initialize
#=>NoMethodError: private method `initialize' called
 for #<Cat:0x00000101d1ad10 @name="lil Bub">
```

Private methods, aside from `initialize`, are usually written with the word `private` above them. Let's make a private `attr_accessor` method called `age` (most cats don't like for humans to just know their ages). Remember that an `attr_accessor` is just a shorthand for two methods: a getter and a setter.

```
class Cat
  attr_accessor :name
 
  CATS = []
 
  def initialize(name, age)
    @name = name
    @age = age
    CATS << self
  end
 
  def self.all
    CATS
  end
 
  def meow
    "Meow, my name is #{name}!"
  end
 
  private
  attr_accessor :age
 
end
```

If we try to call `.age` with an instance of cat, we get an error:

```
bub.age
NoMethodError: private method `age' called for #<Cat:
0x000001020bfce0 @name="Lil Bub", @age=2>
```

**WHY HAVE A PRIVATE `ATTR_ACCESSOR`?**

Previously we were using getters/setters so the outside world could access our instance variables. But now we are using them so that we can locally access them. Why is that valuable? A lot of software bugs come from the incorrect modification of state, aka variables. If we can centralize all modification into one place we can simplify our debugging efforts. That's the beauty of enforcing getters/setters. Let's say you want to see all the places an instance variable is modified. With a private `attr_accessor` you can just override it and put a `binding.pry`.

So instead of having this

```
private
 
attr_accessor :age
```

You can override it with the following and put in a `binding.pry`

```
private
 
def age=(value)
  @age = value
  binding.pry
end
```

Another nice feature of using setters/getters is future proofing. Maybe in the future you want to add validations whenever someone sets one of your instance variables? No problem! Just override the setter method. No need to find all the places that you modify that instance variable and change it.

Let's make a method in the `Cat` class that receives age, through the only way it can: `self`. A cat can have a birthday, where it ages 1 year when the birthday happens.

```
class Cat
  attr_accessor :name
 
  CATS = []
 
  def initialize(name, age)
    @name = name
    @age = age
    CATS << self
  end
 
  def self.all
    CATS
  end
 
  def meow
    "Meow, my name is #{name}!"
  end
 
  def birthday! #why the ! at the end? Just a stylistic
   choice to indicate this method is an action
    self.age = age + 1
  end
 
  private
  attr_accessor :age
 
end
```

Then when we call bub.birthday!, Lil Bub's age becomes 3. Happy birthday, Bub!

##Object Attributes

###Setter vs. Getter Methods

Our Person class' `.name` method is referred to as a **"getter"** or reader method. It returns information stored in an instance variable. In order to make a person's name attribute writable, we need to define a **"setter"** method.

**DEFINING A SETTER METHOD**

```
class Person

  def name
    @name
  end

  def name=(new_name)
    @name = new_name
  end

end
```

A setter method is defined with an `=`, equals sign, appending to the name of the method. The `=` is followed by the `(argument_name)`. Now that we've defined our setter method on the Person class, we can change Kanye's name.

**CALLING A SETTER METHOD**

To call a setter method, you use the `.` notation (dot notation) to call the method and set it equal to a new value.

```
kanye = Person.new

kanye.name = "Kanye"
kanye.name
  => "Kanye"

kanye.name = "Yeezy"
kanye.name
  => "Yeezy"
```

Let's break it down. We:

+ Instantiate a Person instance and name him "Kanye".

+ Call our getter method, `.name` to return his name, `"Kanye"`

+ Call our setter method `.name=` to change his name to `"Yeezy"`

+ Call our getter method again and see that kanye's name is now `"Yeezy"`.

You can also call a setter method like this:

```
kanye.name=("Yeezy")

```

But we prefer the first notation.

###The Abstraction of Instance Methods

In Ruby, it is possible to simply set an instance variable with the following method:

```
kanye.instance_variable_set(:@name, "Yeezy")
```

It is also possible to simply retrieve an instance variable from an object with the following method:

```
kanye.instance_variable_get(:@name)
  => "Yeezy"
```

Since this is the case, why do we even use instance setter and getter methods? In fact, there are a number of reasons:

**SYNTACTIC VINEGAR VS. SYNTACTIC SUGAR**

The first reason is a stylistic one but it is important. As object-oriented Rubyists, we care about our program's readability and design. The above method is ugly. It places a verb at the end of the method name. The weird grammar of this method should remind us not to use it.

###EXPOSING LITERAL VARIABLES VS. ABSTRACTING ATTRIBUTES

The `instance_variable_set` method depends on a literal, concrete variable, `@name`. It exposes it directly to the person executing our code. This is bad practice because it forces our program to rely directly on the `@name` variable. Why is this so terrible? Let's take a look at the following use case:

For example, Kanye (who by the way has commissioned you to write this amazing Person class program) has decided that our program should store both a first and last name. Let's do a quick refactor of our Person class.

```
class Person

  def first_name(first_name)
    @first_name = first_name
  end

  def last_name(last_name)
    @last_name = last_name
  end

  ...

end
```

With this change, our program does more than just make Kanye happy. It has some added functionalty. We could imagine collecting all of our instances of Person and sorting them by last name, for example.

BUT, now, any other part of our program that was calling `instance_variable_get(:@name)` is broken! Additionally, any part of our program that is calling `instance_variable_set(:@name)` isn't taking advantage of our new first name and last name functionality. Any attempt to change a person's name with `instance_variable_set(:@name)` wouldn't really change their name, because it wouldn't touch the `@first_name` and `@last_name` variables. It would just give them an `@name` variable set to a different value than the `@first_name` and `@last_name` variables. That would get confusing, fast.

Allowing our code to rely on an instance variable directly created a program that is **not flexible**. 

**Instead of writing code that depends on instance (or any type of) variables, we write methods that contain instance variables.** This is a form of abstraction, whereas the instance variable `@name` is a literal value. The literal value reference, the variable `@name`, may change as our application grows and we want our application to seamlessly accommodate that change.

Let's create our abstraction: the `.name=` and `.name` setter and getter instance methods:

```
class Person

  def name=(name_string)
    # this method will now take in a string that
     contains
    # a first name and a last name, separated by a
     space,
    # like this: "Kanye West"

    @first_name = name_string.split(" ").first
    @last_name = name_string.split(" ").last
  end

  def name
    "#{first_name} #{last_name}"
  end

end
```

Now, even if the content of the `.name` method changes (for example, Kanye changes his mind again and wants to be referred to only as "Yeezy"), the interface, how our application uses that content, remains constant. In other words, we can change the content of these methods according to our needs, without needing to hunt down every appearance of them in our program and change them as well, like we would need to do with our `instance_method_set` and `instance_method_get` usages.

**Another Benefit of Abstraction**

By wrapping the behaviors of assigning a name and retrieving a name inside instance methods, we make our program easier to debug. For example, let's say that a bug has developed in which our program breaks and stops running every time we try to assign a Person instance a name. If you're using `instance_variable_set`, it can be tough to ID the problem. If we wrap that setting of `@name` or` @first_name` and `@last_name` variables inside a method, we can place a binding.pry inside that method and run our program for easy, hands-on debugging.

##Object Accessors

###What is a Macro?

A macro is any code that writes code for you. The implementation of macros is considered metaprogramming––the writing of programs that operate on other programs.

Implement macros to abstract away the manual, explicit definition of setter and getter methods in a Ruby class.

###Attribute Readers, Writers and Accessors

In object-oriented Ruby, there is a strong convention to have a setter and a getter method that pertain to the same attribute. For example, a .name getter and a .name= setter on our Person class. Because this pattern is so common, we find ourself making these manual setter and getter definitions again and again. As Rubyists, we are lazy, and this is a virtue. If we can achieve the same result with less code, we'll do it.

Check out the example below:

```
class Person
 
  attr_reader :name
  attr_writer :name
 
end
```

+ The `attr_reader` macro, followed by the attribute name `:name`, *created a getter method for us.*

+ The `attr_writer` macro, followed by the attribute name `:name`, *created a setter method for us*.

We can now do the following, without explicitly defining a `.name` or `.name=` method ourselves:

```
jay_z = Person.new
jay_z.name = "Shawn Carter"
jay_z.name
  => "Shawn Carter"
```

Let's compare a Person class that uses macros to one that uses explicit setter and getter methods:

**Macros**

```
class Person
  attr_writer :name
  attr_reader :name
 
end
```

**Explicit Method Definitions**

```
class Person
 
  def name=(name)
    @name = name
  end
 
  def name
    @name
  end
end
```

**Attribute Accessors**

If we know that we are going to be reading and writing to the `name` attribute on our `Person` class, we can use another macro called an attribute accessor.

+ Attribute Readers
	+ Attribute readers give us a getter, or reader, for free.
	+ In other words, if we have an attribute reader (`attr_reader`) for `:name`, Ruby will create a `name` method for us.
	
+ Attribute Accessors
	+ Attribute accessors give us both a getter and a setter for free!
	+ In other words, if we have an attribute accessor (`attr_accessor`) for `:name`, Ruby will create both `name` and `name=` methods for us.

So this code:

```
class Person
  attr_writer :name
  attr_reader :name
 
end
```

Can now be written like this:

```
class Person
  attr_accessor :name
 
end
```

We now have access to reader and writer methods from one macro!

**Using Macros is Usually Better**

The usage of macros is preferred over the explicit definition of setter and getter methods, *unless you need to customize the implementation of a method*, like in our previous lesson when we defined `.name` as returning the first and last name variables combined.

To understand why, simply look at the code above. Which class would you rather write? The longer one, or the shorter one?

When opening up a class, `attr_accessor` and friends allow you to get a high level overview of the class right from the start.

As developers we spend more time reading rather then writing code, so it's important that the code we are reading is challenging, not the easy stuff.

##Class Constants

Class Constants are available to all instances of a particular class. Whereas instance variables, i.e. title, author, etc., are individual to each instance of a class, class constants are shared among **all** instances. They all have access to the same data, and if that data should change for some reason, all instances will know about that change.

**Note: constants are written in all caps.**

**EXAMPLE**

Let's go ahead and define the `GENRES` constant. Since it's plural, I'm going to guess that it should be an array, so I'll start by defining it as an empty array:

```
# book.rb
 
class Book
  attr_accessor :author, :page_count, :genre
  attr_reader :title
 
  GENRES = []
 
  def initialize(title)
    @title = title
  end
 
  def turn_page
    puts "Flipping the page...wow, you read fast!"
  end
 
end
```

##Object Initialization

###Instantiating Instances of Classes

We've already seen new instances of classes being created with the `#new` method. For example:

```
class Dog
end

snoopy = Dog.new #=> #<Dog:0x007f970a2edfd0>
```

The code above creates a new instance of the `Dog` class and sets that object equal to a variable, `snoopy`. If we want to give our dog a breed, we have to use the following code:

```
class Dog
  def breed=(breed)
    @breed
  end

  def breed
    @breed
  end
end

snoopy = Dog.new #=> #<Dog:0x007f970a2edfd0>
snoopy.breed #=> nil
snoopy.breed = "Beagle"
snoopy.breed #=> "Beagle"
```

However, most dogs are born *with* a breed, not assigned a breed afterwards. How can we model the behavoir of dogs being born with a breed in our `Dog` class? If only there was a way for us to assign an individual dog a breed automatically upon creation, or instantiation.

Lucky for us, there is! It's called the `#initialize` method.

###The #initialize Method

We already know that any Ruby class can produce new instances of itself, via the `<Class Name>.new` method, whether or not that class has an `#initialize` method. However, if we want each instance of our class to be created with certain attributes, we must define an `#initialize` method. An `#initialize` method is a method that is called automatically whenever `#new` is used.

Let's define an `#initialize` method that takes in an argument of a dog's breed and sets an `@breed` variable equal to that argument. In other words, let's define our `#initialize` method to contain the functionality of the `#breed=` method, so that a dog instance will get a breed assigned to it right away when it is created, without us having to explicitly use the `#breed= method`.

**DEFINING AN #INITIALIZE METHOD**

```
class Dog
  def initialize(breed)
    @breed = breed
  end

  def breed=(breed)
    @breed = breed
  end

  def breed
    @breed
  end
end
```

Now, we can call `#new` like this:

```
lassie = Dog.new("Collie")

lassie.breed #=> "Collie"
```

**HOW DOES IT WORK?**

When `#new` is called with an argument, it will pass that argument (or arguments) to the `#initialize` method and invoke that method. The code in `#initialize` will then run, using any arguments from `#new`.

The initialize method is what's called a callback method, because it is automatically invoked every time the `#new` method is used to create a new instance of the class.

You can also think of the initialize method as a constructor method. A constructor method is invoked upon the creation of an instance of a class and used to help define the instance of that class.

So, because of how we defined our initialize method, every time you type `Dog.new("some breed")`, a new dog instance is created that has a breed of `"some breed"` (i.e. whatever string you give the `#new method`).

##Self

###Introduction

When we create a class, each new instance of a class is considered to be an object. An object is a bundle of code that contains both characteristics and behaviors.

For example, if we create a `Dog` class like this:

```
class Dog
 
  attr_accessor :name
 
  def initialize(name)
    @name = name
  end
 
  def bark
    "Woof!"
  end
 
end
```

We could create a new instance of `Dog` like this:

```
fido = Dog.new("Fido")
```

We could then access Fido's name like this:

```
fido.name
  => "Fido"
```
or tell Fido to bark like this:

```
fido.bark
  => "Woof!"
```
Fido, the individual dog that we created, has a number of methods we can call on it that will reveal it's attributes, like Fido's name, and enact certain behaviors, like barking.

If an object, like `fido`, is a neat package of attributes and behaviors, does an object know about itself? In other words, does each individual object we create have the ability to enact behaviors on *itself*, instead of in isolation, like our `#bark` method?

In fact, every object is aware of itself and we can define methods in which we tell objects to operate on themselves. We do so using the `self` keyword, inside the body of an instance method, to refer to the very same object the method is being called on.

This is where the analogy of our objects as being alive really resonates. Every object is, quite literally, self aware.

###Using `self`

Try this:

Copy and paste the following code into IRB:

```
class Dog
  def showing_self
    puts self
  end
end
```

Now that we have our Dog class ready to go, copy and paste the following method invocations into IRB:

```
fido = Dog.new
fido.showing_self
```

The call to `#showing_self` should output:

```
#<Dog:0x007faf90a88cd8>
```

How does this work? Inside the `#showing_self` method we use the `self` keyword. The `self` keyword refers to the instance, or object, that the `#showing_self` method is being called on.

So, when we call `#showing_self` on `fido`, the method will puts out to the terminal the `Dog` instance that is `fido`.

###Operating on `self` in an Instance Method

Let's say that Fido here is getting adopted. Fido's new owner is Sophie. Let's write an `attr_accessor` on our `Dog` for the owner attribute.

```
class Dog
 
  attr_accessor :name, :owner
 
  def initialize(name)
    @name = name
  end
 
end
```

Now we can set Fido's `owner` attribute equal to the string of `"Sophie"`. The name of his new owner:

```
fido.owner = "Sophie"
 
fido.owner
  => "Sophie"
```

Great, Fido now knows the name of his owner. Let's think about the situation in which `fido` gets a new owner. This would occur at the moment in which `fido` is adopted.

To represent this with code, we could write an `#adopted` method like this:

```
def adopted(dog, owner_name)
  dog.owner = owner_name
end
```

Here we have a method that takes in two arguments, an instance of the `Dog` class and an owner's name. We could call our method like this:

```
adopted(fido, "Sophie")
 
# now we can ask Fido who his owner is:
 
fido.owner
  => "Sophie"
```

However, the beauty of object-oriented programming is that we can encapsulate, or wrap up, attributes and behaviors into one object. Instead of writing a method that is not associated to any particular object and that takes in certain objects as arguments, we can simply teach our `Dog` instances how to get adopted.

Let's refactor our code above into an instance method on the `Dog` class.

```
class Dog
 
  attr_accessor :name, :owner
 
  def initialize(name)
    @name = name
  end
 
  def bark
    "Woof!"
  end
 
  def get_adopted(owner_name)
    self.owner = owner_name
  end
 
end
```

Here, we use the `self` keyword inside of the `#get_adopted` instance method to refer to whichever dog this method is being called on. We set that dog's `owner` property equal to the new owner's name by calling the `#owner=` method on `self` inside the method body.

Think about it: if `self` refers to the object on which the method is being called, and if that object is an instance of the `Dog` class, than we can call any of our other instance methods on `self`.

##Remembering Objects

**CREATING THE CLASS VARIABLE**

**Example**: remembering all songs created in `Song` class. 

Let's take a step back and think about the concept of responsibility. Whose job is it to know about every instance of the `Song` class? We have two choices right now: an instance of the song class or the `Song` class itself.

It is not the responsibility of an individual song to know about all of the other songs. Keeping track of all of the songs that it creates, however, fits right into the purview of the `Song` class.

So, how can we tell the `Song` class to keep track of every instance that it creates? We use a class variable.

Let's create a class variable, `@@all`, that will store every instance of the `Song` class. Recall that `@@` before a variable name is how we define a class variable.

**ADDING INSTANCES TO THE `@@ALL` ARRAY**

This should happen at the time on instantiation––when a new song gets created, it should be immediately stored by our `Song` class' `@@all` class variable.

We can implement this by simply adding the new instance that gets created into the array stored in @@all inside our #initialize method.

Let's take a look:

```
class Song
 
    @@all = []
 
    attr_accessor :name
 
    def initialize(name)
        @name = name
        @@all << self
    end
end
```

Notice that we set our class variable equal to an empty array. Arrays are perfect for storing lists of data, so we'll use an array to store our lists of `Song` instances.

Now that our class is set up to store the instances that it produces, we have to ask: how does it store these instances?

In `#initialize` we use the `self` keyword to refer to the new object that has just been created by `#new`. Remember that when `#new` is called, it creates a new instance of the class and then calls `#initialize` on that new instance. So, `#initialize` is technically an instance method. Inside an instance method we are in what is called **method scope** and `self` will refer to whichever instance the method is being called on.

We push `self` into the array that is stored in `@@all`. In this way, the `@@all` class variable will point to an ever-growing array that contains every instance of the `Song` class that gets created.

**BUILDING A CLASS METHOD TO ACCESS A CLASS VARIABLE**

Let's call our class method `#all` and code it such that it iterates over all of the individual song instances stored in the `@@all` array, `puts`-ing out the name of each song. This is called a **class getter** method.

```
class Song
 
    @@all = []
 
    attr_accessor :name
 
    def initialize(name)
        @name = name
        @@all << self
    end
 
    def self.all
        @@all.each do |song|
            puts song.name
        end
    end
end
```

Recall that to define a class method we use the def `self.method_name` syntax. In this case, `self` refers to the class on which the class method will be called. This is because we are in the **class scope** right now, not inside the method scope, i.e. in between the `def`/`end` method definition keywords.

Now we can try again to call our class method on the `Song` class:

```
Song.all
```

This should output:

```
99 Problems
Thriller
```

##Procedural vs. Object Orientation

###Overview

What's the difference? Well, in **procedural programming**, we have data and we have the procedures or instructions for operating on that data. In procedural programming, data and procedures, or instructions, are two separate things. In **object-oriented programming**, we have units of code that contain both data and instructions, such that an "object" operates on it's own data structure.

Let's take a look at an example of this distinction using some methods you might find in a procedural implementation of Tic Tac Toe.

###Procedural Programming: Tic Tac Toe

```
board = Array.new(9, " ") # Creates an array with 9
 elements filled with " "

def current_player(board)
  turn_count(board) % 2 == 0 ? "X" : "O"
end

def turn_count(board)
  board.count{|token| token == "X" || token == "O"}
end

def display_board(board)
  puts " #{board[0]} | #{board[1]} | #{board[2]} "
  puts "-----------"
  puts " #{board[3]} | #{board[4]} | #{board[5]} "
  puts "-----------"
  puts " #{board[6]} | #{board[7]} | #{board[8]} "
end
```

In the code above, we have a `board` variable which points to an Array that represents our game board. Each of our methods *must* take the `board` array in as an argument and operate on that array. Here, our data structure, the `board` array, is operated on by each method. The data is separate from the behavior or operations.

In fact, the `board` array is so separate from the behaviors defined in our method that the only indication that our data and our methods are related is the fact that the `board` array is defined in the same general area (i.e. in the same file, close to the lines where we define our methods) as our methods.

Another thing to note about our procedural approach to tic tac toe above is that we constantly have to pass around the `board array` to every method. This is tedious and potentially very troublesome as any method can modify that variable and continue passing it around, thus causing errors in our program.

**What's so bad about our procedural approach?** Ultimately, we don't want to manage our data through proximity and arguments, we want to teach our objects to manage their own data. What happens if we want to grow our tic tac toe game by adding more data or more behaviors? Our program gets messy, fast. The more we add different data and behaviors that are linked only by proximity or via arguments, the more confusing and buggy our program is likely to be.

Consider the following object-oriented implementation of our tic tac toe game:

###Object-Oriented Tic Tac Toe: Tic Tac Toe

```
class TicTacToe
  def initialize(board = nil)
    @board = board || Array.new(9, " ")
  end

  def current_player
    turn_count % 2 == 0 ? "X" : "O"
  end

  def turn_count
    @board.count{|token| token == "X" || token == "O"}
  end

  def display_board
    puts " #{@board[0]} | #{@board[1]} | #{@board[2]} "
    puts "-----------"
    puts " #{@board[3]} | #{@board[4]} | #{@board[5]} "
    puts "-----------"
    puts " #{@board[6]} | #{@board[7]} | #{@board[8]} "
  end
end
```

**What's so great about our object-oriented approach?** Notice that every method became easier to understand. They no longer require arguments and instead can rely on the *internal state of the object*. What do we mean by that? Well, in our `TicTacToe class`, the board array is a *property of an instance of the TicTacToe game*. The `@board` instance variable, and the data that is stored in it, is attached to the instance of the game that is playing out an a given point in time. The *state* of the game, i.e. who is winning, what squares have been filled in, is expressed by the content of the `@board` variable, which itself is a property of the given instance of `TicTacToe`.

The methods of our `TicTacToe` class are also part of an object. When we called `TicTacToe.new` and create a new instance of the class (and start a new game), that instance, that tic tac toe object, has properties, like the `@board` variable, that describe it's state. That tic tac toe instance will also have methods that allow it to enact behaviors and operate on the data stored in it's properties. Our tic tac toe object has the whole package––attributes that contain data describing the state of the object *and* methods that enact behaviors on that data.

Not only is our code now more organized and easier to read and understand, it is also accommodating of future growth and change. We can add more methods and more attributes to our `TicTacToe` class without over complicating our program.

###Moving from Procedural to Object-Oriented

How do we refactor a procedural program into an object-oriented one? The things you want to look for when refactoring procedural code into object-oriented code are:

+ 1) Find any data the methods rely on. Is this data related to the core functionality of our program? If so, think about where this data belongs. Should you pass it in as an argument to an #initialize method? Can the class you are writing create the data structure itself?

+ 2) Are your methods passing data around as arguments? Could this data instead be made into an instance variable?

+ 3) Is there any code that isn't contained in a method? Could that code be placed inside a method in your class and therefore become behavior that belongs to your object?

Anything that works procedurally can also work in an object-oriented fashion and being able to move between these two styles of programming is crucial.
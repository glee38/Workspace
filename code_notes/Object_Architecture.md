#Object Architecture
##Intro to Inheritance
###Introduction: Why Inheritance?

In the real-world, different entities (people, animals, cars, you name it) are related in various ways. Within a single entity or group, there exist systems of classification. For example, the "dogs" entity or category includes pugs, corgis, labs, etc. All of these breeds share common features because they are all dogs. But they all have certain unique traits as well.

Another example: you are writing a web application in which users are either admins, instructors or students. All of these entities are "users" and have common features, but they all have some unique traits as well.

How can our code reflect that fact that these different categories of things all share some, or even many, characteristics but all have some unique attributes as well? Well, we could write separate admin, instructor and student class that each contain repetitious code to lend each of these classes shared attributes and behaviors. We know, however, that repetitious code is always something to be avoided. Not only is it time consuming but, what happens when we need to make a change to this shared behavior? We'd have to code the same change in three places.

Instead, we can use **inheritance**. The use of inheritance allows us to create a family of classes with shared behavior, while still differentiating those classes. With inheritance, we could *inherit* the admin, instructor and student classes from a user class. Then, any changes made to the user class would apply to the other class.

While you may not write your own classes that use inheritance very frequently, you will encounter it frequently as a Ruby on Rails web developer. Once we introduce the use of databases and the challenge of connecting our programs to our database, you'll encounter inheritance in nearly every program you write for the web. More on that (much) later.

###What is Inheritance?

In Ruby, classes can inherit from one another. This means that they adopt all of the attributes and behaviors (i.e. all of the methods) of the parent, also called the **super** class. In this exercise, we'll be building our own chain of inheritance.

###Code Along: Basic Inheritance

In this domain model, we have class `Vehicle` that will act at the parent, or super, class. We will create child classes, also known as **subclasses** for different types of `Vehicles`, such as car.

**STEP 1: DEFINING THE SUPER CLASS**

Open up `lib/super_vehicle.rb`. We're going to define some methods in this parent class so that our subclasses, when we make them, will have access to them.

```
class Vehicle
 
  attr_accessor :wheel_size, :wheel_number
 
  def initialize(wheel_size, wheel_number)
    @wheel_size = wheel_size
    @wheel_number = wheel_number
  end
 
  def go
    "vrrrrrrrooom!"
  end
 
  def fill_up_tank
    "filling up!"
  end
 
end
```

Instances of `Vehicle` initialize with a wheel size and number. We also have `#go` and `#fill_up_tank` instance methods that describe some common vehicle behavior.

Go ahead and run the test suite and you'll see that you are passing all of the tests for the `Vehicle` class but none of the tests for the `Car` class.

**STEP 2: DEFINING THE SUBCLASS**

Open up `lib/sub_car.rb`. Notice that we are requiring `lib/super_vehicle.rb`. That is because our `Car` class will need access to the `Vehicle` class and will therefore need access to the file that contains that class.

Go ahead and define the class in the following way:

```
class Car < Vehicle
 
end
```

We use the `<` to inherit the `Car` class from `Vehicle`. Notice that there are no methods defined in the `Car` class.

Run the test suite again and you'll see that you are passing a number of tests for the `Car` class.

Wow! We didn't write anything in our `Car` class but instances of `Car` class inherit all of the `Vehicle` methods and therefore have access to them. We're still failing the `#go` test however. Looks like the test is expecting the `#go` method on an individual car to return the phrase: `"VRRROOOOOOOOOOOOOOOOOOOOOOOM!!!!!"`. This is different than the return value of the `#go` method that we inherited from the `Vehicle` class.

Let's overwrite the inherited go method with one specific to `Car`.

**STEP 3: OVERWRITING INHERITED METHODS**

In `lib/sub_car.rb`, write the following method:

```
class Car < Vehicle
  def go
    "VRRROOOOOOOOOOOOOOOOOOOOOOOM!!!!!"
  end
end
```

Now, run the tests again and you should be passing all of them.

**Method Look-Up in Ruby**

How does our above example work? Well, when your program is being executed, at the point at which the #go method is invoked, the compiler will first look in the class to which the instance of car that we are calling the method on belongs. If it finds a `#go` method there, it will execute that method. If it doesn't find such a method there, it will move on to look in the parent class that this class inherits from.

##Intro to Modules
###Introduction
In the previous lesson, we discussed the concept of inheritance. We learned that through subclasses, a certain class under a super class, the subclass has access to all of the methods of its parent. Inheriting one class from another makes sense. The subclass can be understood as a child or subordinate of the super class. For example, a car is a type of vehicle, so it makes sense for the `Car` class to *inherit* from the `Vehicle` class.

Let's think about a slightly different type of example, one that is less hierarchical. We could easily envision writing an app that models the environment of a dance performance. Such an app might have a `Ballerina` class. Ballerinas, we know, perform dances. Similarly, we could imagine a little girl going to see the Nutcracker ballet one Christmas, coming home and wanting to practice all of the ballet moves from the show. So, we might write a `Kid` class in which an instance of that class, our little girl who has gone to see the ballet, should have access to all those ballet moves (her performance skill not withstanding). This situation is not hierarchical, like our `Car` and `Vehicle` example. Instead, `Kid` and `Ballerina` simply need to share some functionality, without being related in any other meaningful way.

This is where modules come it. Modules allow us to collect and bundle a group of methods and make those methods available to any number of classes. In this exercise, we'll be defining a `Dance` module and making it available to both the `Ballerina` and `Kid` class.

###Code Along I: Including Module Methods as Instance Methods

This is a code along exercise. Fork and clone this repo by clicking the Github link at the top of the page. Follow along with the walk-through below to get your code working. Get the tests to pass.

**STEP I: DEFINING OUR MODULE**

We'll code our Dance module inside the `lib/dance_module.rb` file. Open up that file and define your module with the following code:

```
module Dance
end
```

Let's give our `Dance` module some fabulous moves:

```
module Dance
  def twirl
    "I'm twirling!"
  end
 
  def jump
    "Look how high I'm jumping!"
  end
 
  def pirouette
    "I'm doing a pirouette"
  end
 
  def take_a_bow
    "Thank you, thank you. It was a pleasure to dance for you all."
  end
end 
```

Okay, now we'll define our `Kid` class and tell it to include the capabilities of the `Dance` module.

**STEP 2: DEFINING THE CLASSES**

Open up `lib/kid.rb` and define your `Kid` class:

```
class Kid
end
```

Let's do the same for the `Ballerina` class in `lib/ballerina.rb`:

```
class Ballerina
end
```

Now we're ready to include our module in our classes:

**STEP 3: INCLUDING THE MODULE**

To lend our two classes all of the methods of the Dance module, we use the include keyword:

class Kid
  include Dance
 
  attr_accessor :name
 
  def initialize(name)
    @name = name
  end
end
class Ballerina
  include Dance
 
  attr_accessor :name
 
  def initialize(name)
    @name = name
  end
end
If we use include keyword, we allow our classes to use all of the methods of the included module as instance methods. We'll talk about how to lend a module's methods as class methods in a minute.

Now that we've included the module, open up bin/dance_party and get familiar with following code:

require_relative "../lib/kid.rb"
require_relative "../lib/ballerina.rb"
 
angelina = Kid.new("Angelina")
mikhail_barishnkov = Ballerina.new("Mikhail")
 
puts "#{angelina.name} says: #{angelina.twirl}"
puts "#{mikhail_barishnkov.name} says: #{mikhail_barishnkov.take_a_bow}"
Now, run the file by typing ruby bin/dance_party and you should see the following output in your terminal:

Angelina says: I'm twirling!
Mikhail says: Thank you, thank you. It was a pleasure to dance for you all.
Code Along II: Extending Module Methods as Class Methods

In order to lend a module's methods to a class as class methods, we use the extend keyword. Let's write yet another module that we can extend into our classes as class methods. For the purposes of this example, let's create a shareable class method, metadata, which will report on some pertinent (shared) information regarding both classes.

Open up the lib/class_method_module.rb and define the following module and methods:

module MetaDancing
 
  def metadata
    "This class produces objects that love to dance."
  end
end
Let's extend this module into both the Kid and Ballerina classes:

class Ballerina
  extend MetaDancing
end
class Kid
  extend MetaDancing
end
Now, open up the bin/extending file and familiarize yourself with the following code:

require_relative "../lib/kid.rb"
require_relative "../lib/ballerina.rb"
 
puts Kid.metadata
puts Ballerina.metadata
Run the file with ruby bin/extending and you should see the following output in your terminal:

This class produces objects that love to dance.
This class produces objects that love to dance.
Run the tests to make sure all of your tests are passing.
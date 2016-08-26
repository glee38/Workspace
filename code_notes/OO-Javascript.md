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

##Creating Objects in JS
####OBJECTIVES
+ Create a constructor function
+ Use a constructor function to create an object
+ Explain what a constructor function is and how it works
+ Explain what `this` is in the context of an object

####INTRODUCTION
Simple variables are great for holding primitive data types, like strings and integers, but we often need a way to represent more complex data, associating many values to a single idea. In JavaScript, the `Object` is the basic associative data structure, and it works just like a dictionary. We can use these objects to associate data *values* with unique *keys*, giving us a human-readable representation of a logical collection of data.

We can construct objects in JavaScript using the literal constructor: `{}` and giving it some properties. Let's make a few sandwiches:

```javascript
var blt = {
  breadType: "white",
  crust: false,
  meat: "bacon",
  condiments: "mayo",
  veggies: ["lettuce", "tomato"],
  cheese: "none"
}
 
var turkeyClub = {
  breadType: "sourdough",
  crust: true,
  meat: ["turkey", "bacon"],
  condiments: "mayo",
  veggies: ["lettuce", "tomato"],
  cheese: "cheddar"
}
 
var grilledCheese = {
  breadType: "white",
  crust: false,
  meat: "none",
  condiments: "none",
  veggies: "none",
  cheese: "cheddar"
}
```
 
Great. Three sandwiches. 

You probably felt like this got tedious, however, and we only made three sandwiches. We're repeating ourselves a lot, copying and pasting (or, even worse, re-typing!) all of the same keys for our objects.

####OBJECTS VS. OBJECT-ORIENTED
When we talk about objects in terms of data structures it's simple. The language provides us with an `Object` type that helps us encapsulate data into key/value pairs.

What we want to do now though is talk about objects in terms of *object-oriented programming*, which goes beyond simple data structures.

In object-oriented programming, we use objects to represent logical and often physical concepts, such as students, books, and even delicious sandwiches. In object-oriented programming (OOP), our objects should not only allow us to *encapsulate* data (i.e. gather and store values that are attributes of the object, such as `meat` and `condiments` in a `sandwich`), but also allow us to *reuse* the data structure without constantly redefining it.

In other words, we should only have to define the properties of a sandwich one time and then be able to create as many different sandwiches as we want without repeating ourselves.

Is there a way to use JavaScript to create a template for a sandwich object that we can use to construct many different sandwiches?

####CONSTRUCTOR FUNCTION
Of course there is! It's called a *constructor function*, and its job, as you might guess from the very on-the-nose name, is to construct new objects. We use the constructor function pattern to essentially build a *prototype* for what an object will look like, including all the properties.

**Advanced**: We call the constructor function a *pattern* because it's not a concept that's built-in to the JavaScript language, but rather a design pattern that has evolved in to common usage as an accepted standard way to instantiate an object. Patterns can be small, task-oriented recipes, such as this constructor function, or they can be big, architecture-oriented guidelines, such as the MVC pattern at the root of a framework like Ruby on Rails or AngularJS. You can read more about design
patterns [here](http://www.oodesign.com/).

Let's build a constructor function for our sandwich objects:

```javascript
function Sandwich(bread, crust, meat, condiments, veggies, cheese) {
  this.breadType = bread;
  this.crust = crust;
  this.meat = meat;
  this.condiments = condiments;
  this.veggies = veggies;
  this.cheese = cheese;
}
```

You'll notice the name of the constructor function `Sandwich` starts with a capital letter. This is important. While the capitalization of a function does not affect how it behaves, it serves as an important signal to our fellow programmers that this function should ONLY be used as a constructor. Adhering to this *convention* is a good way to communicate intent to other developers who may have to maintain this code.

Next, we define the function to accept a whole bunch of parameters. When we create objects with this constructor function, we'll pass in the value of the properties we want our object to have.

**Top-tip**: You'll notice inside the body of the constructor function we're using `this` in front of each of the property names. In this case, `this` will refer to the current object being created, and it's how we differentiate between the *property* `crust` and the local variable `crust` that we got from the function arguments.

####CREATING AN INSTANCE FROM A CONSTRUCTOR FUNCTION
Now that we have a constructor function, let's create our sandwiches:

```javascript
var blt = new Sandwich("white", false, "bacon", "mayo", ["lettuce", "tomato"], "none");
 
var turkeyClub = new Sandwich("sourdough", true, ["turkey", "bacon"], "mayo", ["lettuce", "tomato"], "cheddar");
 
var grilledCheese = new Sandwich("white", false, "none", "none", "none", "cheddar");
```

Notice that when we call these functions, we always call them with the `new` keyword. JavaScript needs us to use the `new` keyword to instantiate a new instance of an object. Without it, we're just invoking the function and setting it to the value of a variable, and since the function doesn't return anything, our variable will be `undefined`.

All functions in JavaScript can be invoked with the `new` keyword, but we only want to do it with functions that are intended to be used as constructor functions. The way we let ourselves and others know when to use the `new` keyword is by making constructor functions start with capital letters! If we forget the new keyword we'll run into all sorts of problems.

How do we know that these are objects and that they were all created using the same constructor function? We can look at the `constructor` property, which gets set automatically during the initialization of the object.

```javascript
blt.constructor;
// returns the Sandwich constructor function
turkeyClub.constructor;
//returns the Sandwich constructor function
grilledCheese.constructor;
//returns the Sandwich constructor function
```

####READING PROPERTY VALUES
So now that we used the constructor function, how do we read the properties of an object?

You can access the properties just like you did when we were treating objects as hashes:

```javascript
blt["breadType"];
//returns white
turkeyClub["meat"]
// returns ["turkey", "bacon"]
grilledCheese["crust"]
//returns false
```

Or, you can use the dot-notation you're familiar with from Ruby:

```javascript
blt.breadType;
//returns white
turkeyClub.meat;
// returns ["turkey", "bacon"]
grilledCheese.crust;
//returns false
```

####REASSIGNING PROPERTY VALUES
Let's say you actually like to eat your grilled cheese with a slice of bacon and tomato, we would need to change the values of the `meat` and `veggies` properties:

```javascript
grilledCheese["meat"] = "bacon";
grilledCheese.veggies = "tomato";
```

####SUMMARY
We've reviewed working with objects in JavaScript, and started to think about *object-orientated programming* by applying the *constructor function* pattern when creating objects so that we can easily define and reuse objects that we design.

##Object Methods in JS
####OBJECTIVES
+ Explain what a method is and the difference between a method and a function
+ Add an action to a constructor function
+ Explain what `this` is in the context of an object
+ Create ES6 classes
+ Explain ES6 class inheritance with `extends`

####INTRODUCTION
Objects have both data and behavior. Data comes in the form of properties that store information, such as the `length` of an array, or the `name` of a Person. But objects also can have properties that store behavior, or functions, such as the `slice()` method of an array. When a function is a property of an object, it is known as a *method* of that object.

####ADDING METHODS TO AN OBJECT
Let's create a constructor function for some `User` objects.

```javascript
function User (name, email){
  this.name = name;
  this.email = email;
}
```

How do we give our JavaScript user objects the ability to say hello?

We already know how to create functions. Now we need to attach a function to an object as a property.

```javascript
function User (name, email){
  this.name = name;
  this.email = email;
  this.sayHello = function(){
    console.log("Hello, my name is " + this.name);
  }
}
```

We've now added the `sayHello` method to our `User` constructor function. Because a method is just a function that is attached to an object via a property, `sayHello` is a method. We call `User` a function, and not a method, because it's a standalone function and not a property of any object.

It's a semantic distinction. All methods are also functions. We just use "method" as a convention when we communicate that lets other people know that we mean a function that is part of an object.

It's important to note that we use this `twice` in relation to the `sayHello` method. We use it once: `this.sayHello`, where this is referencing the object we'll create (as long as we invoke the function with the new keyword). The this keyword is probably the most confusing concept in JS so for now let's just assume it works like Ruby's self and refers to the instance of the object we're referring to.

Let's make a few users:

```javascript 
carl = new User("Carl", "sparkles@aol.com");
 
betsy = new User("Betsy", "betsy@flatironschool.com")
 
george = new User("George", "george@me.com")
```

We can have the users greet us too:

```javascript
carl.sayHello();
// prints "Hello, my name is Carl" to the console
betsy.sayHello();
// prints "Hello, my name is Betsy" to the console
george.sayHello();
// prints "Hello, my name is George" to the console
```

But there's a problem here. When build the method directly into the constructor function like this, we're using a lot of space in memory. Every single time a `User` object is created and stored in memory, the `sayHello` function is created and stored in memory with it. What if you're Facebook and have 1.19 billion active users a month? If you were to instantiate all those users at once, you'd be recreating that function in memory 1.19 billion times! (incidentally this is how Ruby does it)

####ADD METHOD TO PROTOTYPE
Javascript objects have something called a Prototype. For now, we won't get into an extremely detailed discussion of what Prototypes are, but we will use them as a place to keep our "instance" methods. In Javascript, when you call a property, the interpreter will look on the instance of the object for a property, and when it finds none, it will look at the Object's Prototype for that property. If we've attached a function as the property of that name it will call that function in a similar way that Ruby's method lookup chain works.

```javascript
function User (name, email){
  this.name = name;
  this.email = email;
}
 
User.prototype.sayHello = function(){
  console.log("Hello, my name is "+ this.name);
}
 
var sarah = new User("sarah", "sarah@aol.com");
 
sarah.sayHello();
```

For all intents and purposes, we've created a JS class following a common pattern that combines the use of constructor functions with extending behavior via the object's prototype. This works, but is incredibly verbose, and you always run the risk of forgetting to add methods to the prototype instead of directly to the constructor.

It would be nice if there were an approach that allowed us to construct true classes while still taking advantage of the prototypal nature of JavaScript. But there's no way to do that.

OR IS THERE?

(There is)

####ES6 CLASSES
ECMAScript 6 introduces the concept of a `class` to JavaScript that provides a handy shortcut for organizing our objects.

It's important to note that the `class` keyword doesn't actually turn JavaScript into a class-based object-oriented paradigm. It's just *syntactical sugar*, or a nice abstraction, over the prototypal object creation we've been doing.

Let's convert our user to a class.

```javascript
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }
 
  sayHello() {
    console.log("Hello, my name is "+ this.name);
  }
}
 
var sarah = new User("Sarah", "sarah@aol.com");
sarah.sayHello();
```

Instead of our `User` constructor function, we now have a `class User`. Within the body of the class, we can define a special function named `constructor` to be our constructor function. In the end, we still instantiate a `new User` the same way.

We also define our `sayHello` function directly in the body of the class. However, unlike defining it in the constructor function, we can verify that `sayHello` is defined on the User prototype by examining `User.prototype`.

####ES6 CLASS INHERITANCE WITH EXTENDS
We can also easily inherit from ES6 classes without having to go through the trouble of assigning `prototype` via `Object.create`.

Say we want to create a `Teacher` class for our school system that inherits from `User`. We can just define a new class and use the `extends` keyword.

```javascript
class Teacher extends User {
    sayHello() {
      super.sayHello()
      console.log("I am a teacher");
    }
}
 
var t = new Teacher("Tom", "tom@geocities.edu")
t.sayHello()
```

Here, we've *extended*, or inherited from `User` when creating new class `Teacher`. We also created an *override* to the `sayHello` method so that it would reflect our teacher object better.

If you look at the line `super.sayHello()`, what we're doing there is calling the `sayHello` method of the *superclass*, or class our class inherits from. We wanted to preserve the behavior that was already there and then add to it, so rather than repeat the code, the `super` object gives us access to it programmatically.

####SUMMARY
In this lesson, we've learned the differences between methods and functions, and seen how to add methods to objects through both the constructor and the prototype.

We also explored the new `class` syntax of ES6 and how to create and extend classes using it.

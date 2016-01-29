#Intro to Object Relationships

##Introduction

So far, we've mainly worked with classes that do not play well with other custom classes. In other words, we've defined classes that create objects that do not interact with other objects that we've created. Instead, they have methods that allow them to operate on themselves, or operate with some of the built-in Ruby classes. For example, a `Dog` class might have methods that describe an individual dog's attributes and behaviors. A single dog could have a name (as a Ruby String) and tell you their name but not have it play with another dog object.

In object-oriented programming, however, we write programs that model real-world situations and environments. In the real world, different entities are related to one another and interact with one another in various ways. Luckily for us, individual objects in object-oriented Ruby can interact with one another in ways that reflect that real-world relatedness. In fact, it's hard to imagine an application without some degree of interaction, or association, between your classes, or models.

**Here are just a few examples:**

+ Your users sign in to your app and "friend" other users. All of a sudden, users are associated through friendship.

+ Your app connects users to animal shelters through which they can adopt pets. Users are associated to shelters and to pets. At the same time shelters are associated to pets.

+ Your app allows users to aggregate their most recent tweets and those of their friends and followers. In this example, users are associated to tweets and users might even be associated to each other.

These are just a few examples of the sorts of domain models you will soon develop, almost all of which will involve object relations––the idea that instances of your classes (also referred to as models in such a situation) can interact with each other and be associated with one another.

In this lesson, we'll be taking a look at one of the most basic ways that two classes, or models, can be related to one another: the "belongs to" relationship.

##The "Belongs To" Relationship

Imagine we are creating an app that allows users to list and interact with their music library. We're used to writing single classes to represent single concepts in our program. In this application, it makes sense for us to have a class to represent an individual song. Our `Song` class might look something like this:

```
class Song
 
  attr_accessor :title
 
  def initialize(title)
    @title = title
  end
end
```

Now we can create a new song like this:

```
hotline_bling = Song.new("Hotline Bling")
```

And we can return it's title to us like this:

```
hotline_bling.title
  # => "Hotline Bling"
```

As far as modeling our program on the real world however, this isn't very realistic. Songs have many more attributes that just a title. The most important of which, from a user's point of view at least, is the song's artist. In the real world, a song belongs to an artist and an artist owns the many songs he or she has created. How can we model this relationship through our code? Let's give individual songs an artist attribute:

```
class Song
 
  attr_accessor :title, :artist
 
  def initialize(title)
    @title = title
  end
 
end
```

Now that we have a setter and getter for a song's artist attribute, we can do this:

```
hotline_bling.artist = "Drake"
hotline_bling.artist
  # => "Drake"
```

Now that a song can have an artist, we might be wondering what other attributes might be related to songs and artists in this domain model.

For example, users of our music app might want to know some more info about an individual artist. What albums has Drake created, for example? What about the genre of Drake's work?

Let's ask him:

```
hotline_bling.artist.genre
  NoMethodError: undefined method `genre' for
   "Drake":String
```

Uh-oh! Looks like the string, `"Drake"`, that we assigned this song's `artist` attribute equal to, doesn't (shockingly) have a `#genre` method. The relationship model that we have set up is incomplete. An individual song does have an artist attribute, but instead of setting it equal to a complex object, such as an instance of some kind of `Artist` class that we can get more information from, we've set it equal to a simple string. A string can't tell us what genre of music it makes, how many albums it has created or anything else necessary to modeling our music app. This makes sense. The developer of Ruby can only make building blocks. They can only create general purpose blocks that we compose into great creations.

So, instead of setting the `#artist=()` method equal to a string of an artist's name, let's create an Artist class and assign an individual song's artist attribute equal to an instance of that class.

```
class Artist
  attr_accessor :name, :genre
 
  def initialize(name, genre) 
    @name = name
    @genre = genre
  end
 
end

drake = Artist.new("Drake", "rap")
hotline_bling = Song.new("Hotline Bling")
 
hotline_bling.artist = drake
```

Just like we were able to set the artist attribute equal to the string, `"Drake"`, we can set the attribute equal to an instance of the `Artist` class, stored in the `drake` local variable.

Now we can ask for the genre of the artist of `hotline_bling`:

```
hotline_bling.artist.genre
  # => "rap"
```

And the name of the artist of `hotline_bling`:

```
hotline_bling.artist.name
  # => "Drake"
```

Now our relationship between songs and their artists is complete. **This is called the "belongs to" relationship**. A song can only have one artist (at least in our domain model), so we say that a song "belongs to" an artist. We enact this relationship by giving songs a setter and a getter method for their artist. There is nothing that requires that the artist attribute be filled with an instance of the `Artist` class. This is an internal contract that you must keep. As the developer you must make sure that you only put `Artist` instances in there!
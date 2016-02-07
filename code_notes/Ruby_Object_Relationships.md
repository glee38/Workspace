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

##Introduction

We know that the programs we write are meant to model real-world environments. This is because the programs we write are designed to carry out real-world jobs and solve real-world problems. Whether you're creating an app that connects users around the world in some kind of social network or writing a program for a major university that manages their course offerings and students, your code will need to be able to realistically map the relationships between different entities.

We already know about the "belongs to" relationship. Let's say we have a `Song` class that produces individual song objects. Each song belongs to the artist that wrote it. We can build that relationship by creating an `attr_accessor` in the `Song` class for `artist`:

```
class Song
    attr_accessor :artist, :name
 
    def initialize(name)
        @name = name
    end
end
```

If we also have an Artist class that looks like this:

```
class Artist
    attr_accessor :name
 
    def initialize(name)
        @name = name
    end
end
```

We can set an individual instance of `Song` equal to an instance of the `Artist` class like this:

```
ninetynine_problems = Song.new("99 Problems")
jay_z = Artist.new("Jay-Z")
 
ninetynine_problems.artist = jay_z
 
ninetynine_problems.artist.name
  # => "Jay-Z"
```

The benefit here is that in setting the `artist=()` method equal to a real instance of the `Artist` class, instead of equal to a simple string, we are associating our song to a robust object that has its own attributes and behaviors.

For example, in the code above, we are calling the `#name` method on the artist of `ninetynine_problems`. With method chaining like this, we can do even more with our code.

The inverse of the "belongs to" relationship is the "has many" relationship. If a song belongs to an artist, then an artist should be able to have many songs. This makes sense in the real-world––most musical artists have authored and performed many more than one song.

Let's take a closer look.

##The "has many" Relationship

How can we represent an object's "having many" of something? Well, having many of something means you own a collection of that thing. Ruby offers us a great way to store collections of data in list form: arrays.

We would like to be able to call:

```
jay_z.songs
```

And have returned to us a list, or array, of the songs that Jay-Z has written. A given artist should start, or be initialized, with a songs collection that is empty. Later, we will write a method that adds songs to that collection.

**INITIALIZING WITH AN EMPTY COLLECTION**

```
class Artist
 
    attr_accessor :name
 
    def initialize(name)
      @name = name
      @songs = []
    end
end
```

Here we set an instance variable, `@songs`, equal to an empty array. Recall that we use instance variables to store the attributes of a given instance of a class. This instance variable is set equal to an empty array because our artist doesn't have any songs yet.

Let's write the method that will allow us to add some.

**ADDING ITEMS TO THE COLLECTION**

Whose responsibility is it to add a new song to a given artist's collection? Well, at what point in time does an artist add another song to his or her repertoire? When that artist writes a new song. Consequently, it isn't the song's responsibility to add itself to the artist's collection of songs, it is the artist's responsibility to add a new song to their collection.

That's why we'll write the method that adds songs to an artist's collection in the `Artist` class:

```
class Artist
 
    attr_accessor :name
 
    def initialize(name)
      @name = name
      @songs = []
    end
 
    def add_song(song)
        @songs << song
    end
end
```

Now we can execute the following code:

```
jay_z = Artist.new("Jay-Z")
jay_z.add_song("99 Problems")
jay_z.add_song("Crazy in Love")
```

Now we need a method that will allow a given artist to show us all of the songs in their collection. Let's do it.

**EXPOSING THE COLLECTION**

Let's write an instance method, `#songs`, that we can call on an individual artist to return the list of songs that artist has.

```
class Artist
 
    attr_accessor :name
 
    def initialize(name)
      @name = name
      @songs = []
    end
 
    def add_song(song)
      @songs << song
    end
 
    def songs
      @songs 
    end
end
```

The `#songs` method simply return the `@songs` array, which contains the list of songs that the artist has many of.

Let's try it out:

```
jay_z.songs
  # => ["99 Problems", "Crazy in Love"]
```

**RELATING OBJECTS WITH "BELONGS TO" AND "HAS MANY"**

Wow, those look like interesting songs, you might be thinking. I wonder what kind of music Jay-Z makes, you might be wondering if you live on Mars. Well, let's ask `jay_z` to tell us the genres of the songs he has many of.

Oh no! We can't do that because `jay_z`'s songs are simply a list of strings. We can't ask a plain old string what genre it has, it will have no idea what we are talking about.

This is the limitation of one-sided relationships. Just like associating a given song to a string that contains an artist's name instead of to a real `Artist` instance had its drawbacks, so too does associating a given artist to a list of strings. With this set up, we are limited to references to a given artist's songs by their name alone. We cannot associate any further information to an artist's songs or enact any further behavior on an artist's songs.

Let's fix this now. Instead of calling the `#add_song` method with an argument of a string, let's call that method with an argument of a real song object:

```
99_problems = Song.new("99 Problems", "rap")
crazy_in_love = Song.new("Crazy in Love", "pop")
 
jay_z.add_song(99_problems)
jay_z.add_song(crazy_in_love)
 
jay_z.songs
  # =>[#<Song:0x007fa96a878348 @name="99 Problems", @genre="rap">, #<Song:0x007fa96a122580 @name="Crazy in Love", @genre="pop">]
```

Great, now our artist has many songs that are real, tangible `Song` instances, not just strings.

We can do a number of useful things with this collection of real song objects, such as iterate over them and collect their genres:

```
jay_z.songs.collect do |song|
    song.genre
end
  # => ["rap", "pop"]
```

**Object Reciprocity**

Now that we can ask our given artist for his songs, let's make sure that we can ask an individual song for its artist:

```
crazy_in_love.artist
  # => nil
```

Although we do have an `attr_accessor` for `artist` in our `Song` class, this particular song doesn't seem to know that it belongs to `Jay-Z`. That is because our `#add_song` method only accomplished associating the song object to the artist object. Our artist knows it has a collection of songs and knows how to add songs to that collection. But, we didn't tell the song that we added to the artist that it belonged to that artist.

Let's fix that now. Telling a song that it belongs to an artist should happen when that song is added to the artist's `@songs` collection. Consequently, we will write the code the accomplishes this inside our `#add_song` method:

```
class Artist
 
    attr_accessor :name
 
    def initialize(name)
      @name = name
      @songs = []
    end
 
    def add_song(song)
        @songs << song
        song.artist = self
    end
 
    def songs
        @songs 
    end
end
```

Let's take a closer look at the code in our `#add_song` method:

```
def add_song(song)
    @songs << song
    song.artist = self
end
```

Here, we use the `self` keyword to refer to the artist on which we are calling this method. We call the `#artist=()` method on the song that is being passed in as an argument and set it equal to `self`––the artist.

Let's try calling `#add_song` again:

```
jay_z.add_song(crazy_in_love)
```

Now, we should be able to ask crazy_in_love for its artist:

```
crazy_in_love.artist.name
  # => "Jay-Z"
```

We did it! Not only does an artist have many songs, but a song belongs to an artist and we built a method that enacts those associations at the appropriate time.

###Extending the Association and Cleaning up our Code

The code we have so far is pretty good. The best thing about it though, is that it accommodates future change. We've build solid associations between our `Artist` and `Song` class via our has many/belongs to code. With this foundation we can make our code even better in the following ways:

**THE `#ADD_SONG_BY_NAME` METHOD**

As it currently stands, we have to first create a song and then add it to a given artists collection of songs. We are lazy programmers, if we could combine these two steps, that would make us happy. Furthermore, if you think about our domain model, i.e. the program we are writing to model the real-world environment of an artist and their songs, the current need to create a song and then add it to an artist doesn't really make sense. A song doesn't exist before an artist creates it.

Instead, let's build a method `#add_song_by_name`, that takes in an argument of a name and genre and both creates the new song *and* adds that song to the artist's collection.

```
class Artist 
  ...
 
  def add_song_by_name(name, genre)
    song = Song.new(name, genre)
    @songs << song
    song.artist = self
  end
```

Here we use the logic of our original `#add_song` method, which adds a song to an artist's collection and tells that song that it belongs to that particular artist. But, we also create a new song using the name and genre from the arguments.

This is not only neater and more elegant––now we don't have to create a new song on a separate line every time we want to add one to an artist––but it makes more sense.

**THE `#ARTIST_NAME` METHOD**

Since we've already set up these great associations between instances of the Song and Artist class, we can use them to build other helpful methods.

Currently, to access the name of a given song's artist, we have to chain our methods like this:

```
crazy_in_love.artist.name
  # => "Jay-Z"
```

That's not very elegant. Wouldn't it be nice if we have one simple and descriptive method that could return the name of a given song's artist? Let's build one!

```
class Song
  ...
 
  def artist_name
    self.artist.name
  end
```

Now we can call:
```
crazy_in_love.artist_name
  # => "Jay-Z"
```

Much better. Notice that we used the `self` keyword inside the `#artist_name` method to refer to the instance of `Song` on which the method is being called. Then we call `#artist` on that song instance. This would return the `Artist` instance associated to the song. Chaining a call to `#name` after that is equivalent to saying: call `#name` on the return value of `self.artist`, i.e. call `#name` on the artist of this song.

##Collaborating Objects

###Introduction

Let's stick with our song/artist example. Our song class is responsible for handling songs. Our artist class is responsible for handling artists. However, these things clearly have some relation to one another. Remember, a song belongs to an artist, and an artist has many songs. These two classes will have to collaborate.

In fact, the classes do not even need to have any relationship ("`has many`" or "`belongs to`") to collaborate. Imagine we have an MP3 Importer that is responsible for taking in a bunch of MP3 files and making a song for each unique filename. It is not hard to imagine that to make a song, the MP3 Importer will have to have some sort of communication with the `Song` class.

Let's take a look at each of these collaborations in more detail.

###MP3 Importer collaborating with Songs

The purpose of this MP3 Importer is to take in a list of mp3s and send each mp3 filename to the Song class to make a Song. Let's just focus on the collaboration. Our MP3Importer class will receive a list of filenames that look like this "Drake - Hotline Bling". MP3Importer will then send each of those filenames to the Song class to be created.

```
class Song
  attr_accessor :title
 
  def self.new_by_filename(filename)
    song = self.new
    song.title = filename.split(" - ")[1]
    song
  end
 
end
```

```
class Mp3Importer
  attr_accessor :songs 
  def import(list_of_filenames)
    list_of_filenames.each{|file_name| songs <<
     Song.new_by_filename(file_name)}
  end
end
```

Notice how within the `Mp3Importer` class we are calling the `Song` class and a method within the `Song` class: `.new_by_filename`.

When we hit this line of code, it will send us to the `Song` class to do whatever behavior we have defined in the `.new_by_filename` class method. Then we will return to the `Mp3Importer` class to continue executing the code. This is at the heart of collaborating objects.

###Songs collaborating with Artists

Since our song belongs to an artist, we will want to collaborate with the `Artist` class at some point. Imagine we have the following code:

```
class Song
  attr_accessor :artist
 
  # other methods
 
  def artist_name=(name)
    if (self.artist.nil?)
      self.artist = Artist.new(name)
    else
      self.artist.name = name
    end
  end
end
```
```
class Artist
  attr_accessor :name
 
  def initialize(name)
    @name = name
  end
 
  # other methods 
 
end
```

The point of this code is that we want to be able to execute the following code given a song `hotline_bling = Song.new('Hotline Bling')` (Let's use Hotline Bling by Drake):

```
hotline_bling.artist_name = "Drake"
hotline_bling.artist
```

This should then return the new `Artist` object that was created by the `#artist_name` method. This is just another example of how objects can collaborate without being directly related.

##Has Many Through

###Introduction

We've seen how objects can be related to one another directly, when one object contains a reference to another.

An individual song belongs to an artist, for example. That relationship is implemented by giving a `Song` instance an `artist` `attr_accessor`. Then we set that `artist` attribute to an instance of the `Artist` class.

An individual artist, conversely, has many songs. We implement this relationship by giving artists a `song` property that is set equal to an array of `song` instances. We add `song` instances to that array via an instance method on the `Artist` class, `#add_song`.

Let's take a look:

```
class Song
  attr_accessor :name, :artist
end
```
```
class Artist
  attr_accessor :name
 
  def initialize(name)
    @name = name
    @songs = []
  end
 
  def add_song(song)
    @songs << song
    song.artist = self
  end
end
```

Notice that in the `#add_song` method, a song is passed in as an argument and added to an artist's `@songs` array. At the same time, that song's `artist` attribute is set equal to the instance of the artist that the `#add_song` is being called on, referenced by the `self` keyword.

This is the "has many"/"belong to" association. A song belongs to an artist and an artist has many songs. This relationship is direct. It is enacted with methods on our `Song` and `Artist` instances that directly reference and interact with objects of the other class.

However, in the real-world, different entities can be connected to one another *indirectly* as well as directly. For example:

+ A family tree in which you are directly related to your parents, and indirectly related to your grandparents. In this case, you are related to your grandparents through your parents.

+ A company that offers a network of doctors to their employees through the company's insurance program.

+ A user on a popular media sharing site can have many "likes", that occur through the pictures he or she posts.
These are just a few examples of the kind of indirect relationships that we may need to model in our programs.

In this lesson, we'll build out just such a relationship, using our music app domain model.

###Our Domain Model

In our example, we already have a has many/belongs to relationship between songs and artists. Let's add in another model to step up the complexity of our associations.

In the real world, individual songs belong to a genre. For example, you could classify Jay-Z's "99 Problems" as a rap song but his song "Crazy in Love", with Beyonce, is more of a pop song.

Let's give our `Song` instances the ability to belong to a genre:

```
class Song
  attr_accessor :name, :artist, :genre 
end
```

Let's build a `Genre` class, so that we can associate individual songs to complex genre objects that can contain other information pertinent to a given genre.

```
class Genre
  attr_accessor :name
 
  def initialize(name)
    @name = name
  end
end
```

Great! Now we can associate a `Song` to a `Genre`. At what point should this association be created? When a song is created, it can be categorized as a particular genre. So, let's create an `#initialize` method for our `Song` class that takes in an argument of a song name and a genre.

```
class Song
  attr_accessor :name, :artist, :genre
 
  def initialize(name, genre)
    @name = name
    @genre = genre
  end
end
```

With the above code, we can assign a `Song` instance a given genre:

```
rap = Genre.new("rap")
ninety_nine_problems = Song.new("99 Problems", rap)
 
ninety_nine_problems.genre
  # => rap
ninety_nine_problems.genre.name
  # => "rap"
```

Let's take a step back and think about the web of relationships that exists between our three classes, or models. We have an `Artist` class that produces individual artists that have many songs. We have a `Song` class that produces individual songs that belong to an artist *and* belong to a genre.

![relationship web](http://readme-pics.s3.amazonaws.com/Screen%20Shot%202015-11-03%20at%2012.23.17%20PM.png)

###The "has many through" Relationship

Here we can see that an artist has many songs and that each song belongs to one artist and one genre. This diagram makes it clear to us that an artist *does* have a connection to genres. That connection exists *through* the many songs that an artist owns. **This is the "has many through" relationship.** A given object has many of another type of object. That second object belongs to (or has many) of a third type of object. Therefore, the first object has many of the third object as well.

So, if artists do in fact have many genres, *through* songs, how can we ask a given artist to show us the genres that it is associated with? An artist has a genre through the songs that it has created, so in order to ask an artist about it's genre, we have to go through that artist's songs.

We'll need to collect all of the songs of a given artist and *then* collect the genre associated to each of those songs.

Let's build an instance method on the `Artist` class to accomplish that.

**BUILDING ON THE "HAS MANY THROUGH" RELATIONSHIP**

We'll call our instance method `#genres`. This method will collect all of the genres of all of the songs of a given artist.

```
class Artist
  attr_accessor :name
 
  def initialize(name)
    @name = name
    @songs = []
  end
 
  def add_song(song)
    @songs << song
    song.artist = self
  end
 
  def songs
    @songs
  end
 
  def genres
    self.songs.collect do |song|
      song.genre
    end
  end
end
```

The `#genres` method iterates over the `@songs` array, stored in the `#songs` instance method, and calls the `#genre` method on each song in order to collect the genre that is associated to that song. The return value of the `#genres` method should be an array of genre objects.

Let's see it in action:

```
jay_z = Artist.new("Jay-Z")
 
rap = Genre.new("rap")
pop = Genre.new("pop")
 
ninety_nine_problems = Song.new("99 Problems", rap)
crazy_in_love = Song.new("Crazy in Love", pop)
 
jay_z.add_song(ninety_nine_problems)
jay_z.add_song(crazy_in_love)
 
jay_z.genres
  # => [rap, pop]
```

By simply querying an artist's songs for their genre, we use existing relationships, in this case the relationship between an artist and a song and a song and a genre, to discover deeper relationships.

**COMPLETING THE RELATIONSHIP**

Right now, an artist can tell us about its songs and about its genres. But, a genre can't tell us about its songs and its artists. Let's fix that now.

The first thing we want to do is build the direct relationship between a song and a genre. A song already belongs to a genre. Let's build the inverse of that relationship, the "has many" side. A genre should have many songs:

```
class Genre
  attr_accessor :name
 
  def initialize(name)
    @name = name
    @songs = []
  end
end
```

Now, when we initialize a new genre, we do so with an empty collection of songs. Let's build a method, `#add_song`, that adds a new song to the given genre's collection:

```
class Genre
  attr_accessor :name
 
  def initialize(name)
    @name = name
    @songs = []
  end
 
  def add_song(song)
    @songs << song
  end
end
```

Now, we can refactor our `Song` class such that when a new song is instantiated it gets associated to a genre and the given genre adds that song to it's collection. This is similar to our `add_song` method from the `Artist` class:

```
class Song
  attr_accessor :name, :artist, :genre
 
  def initialize(name, genre)
    @name = name
    @genre = genre
    genre.add_song(self)
  end
end
```

Now let's see it in action:

```
rap = Genre.new("rap")
 
ninety_nine_problems = Song.new("99 Problems", rap)
lucifer = Song.new("Lucifer", rap)
 
rap.songs
  # => [ninety_nine_problems, lucifer]
```

Now a song knows about the genre it belongs to and a genre knows about the many songs that it has.

Let's put the finishing touches on our "has many through" relationship and tell our genres how to show us the artists they are associated to through the songs it has.

```
class Genre
  attr_accessor :name
 
  def initialize(name)
    @name = name
    @songs = []
  end
 
  def add_song(song)
    @songs << song
  end
 
  def artists
    @songs.collect do |song|
      song.artist
    end
  end
end
```

The `Genre` `#artists` method iterates over the genre's `@songs` collection, calls the `#artists` method on each song object and collects the resulting artists.

###Conclusion: The Advantages of the "has many through" relationship

Why associate artists to genre objects through songs? By associating songs to genres, we are not only reflecting the real world situation that our program is meant to model, we are creating clean and re-usable code. Without the song/genre association, you'll find that you have to add a given song to an artist's list of songs and then separately add a genre to that artist.

With our "through association", as long as we have properly associated a song to a given genre and that same song to a given artist, our connection between an artist and his or her genres will automatically follow.

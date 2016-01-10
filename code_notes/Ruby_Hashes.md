#Hashes
##Hash Structure and Usage

Hashes are structured with keys and values. Each key/value pair makes up one unit in the hash. The entire collection of key/value pairs, which are comma separated, is enclosed in curly braces `{ }`.

```
hash = {"key" => "value", "another_key" => "another
 value"}
```

You can think of a hash as a dictionary. The key is the reference point that is set equal to an associated value. A vocabulary dictionary actually makes a pretty good example of the hash structure:

```
dictionary = {

  "apple" => "a delicious fruit", 
  "this readme" => "wonderful and informative piece of
   reading", 
  "Moby Dick" => "a novel by my good friend, Herman
   Melville"

}
```

**//Flat-fact**: *In fact, "dictionary" is how the hash structure is known in some other programming languages such as Python and Objective-C.*

Keys in hashes can be any type of data: strings, integers or symbols (more on symbols later, don't worry about them for now). They are set equal to their associated values by using the `=>` symbol, known for this use as the "hash-rocket" (because it looks like a little rocket shooting off towards the right).

**EXAMPLE: NAMED DATA**

You can think of the data stored in a hash as being *associative*. In other words, keys point to data that is *related* to that key. Let's take a look at the following example:

```
the_rock = {"name" => "Dwayne, The Rock, Johnson", "weight" => "a lot"}
```

If we tried to represent this same information as an array, it would look something like this:

```
the_rock = ["name", "Dwayne, The Rock, Johnson", "weight", "a lot"]
```

Then, in order to access The Rock's name, we would have to use the following line of code:

```
the_rock[1]
```

That requires us to remember, every time we want to access The Rock's name, that it is stored at the first index of `the_rock` array. That is too hard to keep track of.

In our `the_rock` hash, we have two key/value pairs. We have a key of `"name"`, which points to an associated value of The Rock's name. We have have a key of `"weight"` which points to an associated value of The Rock's (real) weight. In this way, we are able to store data about The Rock in an organized and understandable manner.

###Hash Keys and Uniqueness

A key in a hash can be a string, a symbol, or an integer as long as you only use each key once. This is because, unlike arrays which store data in numbered indexes and access that data via the index numbers, hashes store data in keys and access that data by naming the key whose value we want to retrieve.

If you try to add a second key that duplicates an existing key, you will overwrite the value of the existing key, *instead of adding a new key/value pair*. Not only will duplicate hash keys simply not work as a way to store information, it just doesn't make sense.

Hashes are for storing named, or associative, data. Each key/value pair describes a unique attribute or unit of information. A person doesn't have two names, in reality. They might, though, have a name and a nickname. The data structure we are creating should mirror the real-world entity we are trying to describe. The uniqueness of our hash keys reflects that.

```
the_rock = {"name" => "Dwayne Johnson", "stage_name" =>
 "The Rock", "weight" => "a lot"}
```

**EXAMPLE: COUNTING DATA**

The year is 1866 and we work beside Herman Melville (author of Moby Dick) as a customs inspector in New York. (I know, when did our lives become so old-timey and exciting?) We're in charge of inspecting an incoming shipment and we need to track the items we're inspecting by listing each type of item (such as: whale bone corsets, porcelain vases, and oil paintings) along with the number of each kind of item in the shipment.

Let's say our shipment contains five (5) whale bone corsets, two (2) porcelain vases, and three (3) oil paintings. If we tried to store them in an array in list form, it would look like this:

```
old_fashioned_things = [
  "whale bone corset", 
  "whale bone corset", 
  "whale bone corset", 
  "whale bone corset", 
  "whale bone corset", 
  "porcelain vase", 
  "porcelain vase", 
  "oil painting", 
  "oil painting", 
  "oil painting"
]
```

As it stands, there is no way for us to include the count of each item in a way that associates the item to its count, so we have to store a separate occurrence of that item in order to track the shipment. Certainly for a small list this is manageable, but what happens when the shipment of Herman's first printing of Moby Dick arrives? We'd have to store 10,000 occurrences of the book instead of recording the count as value. That's no good!

So, how else can we help Herman keep track of how many of each item are included in the shipment? Here's where a hash can come in handy. With a hash, we can store a list of associated key/value pairs. In other words, we can store the pairs of item/count:

```
old_fashioned_things = {
  "whale bone corset" => 5, 
  "porcelain vase" => 2, 
  "oil painting" => 3
}

first_printing = {
  "Moby Dick" => 10000
}
```

We'll be done recording these shipments in no time!

##Creating Hashes

We can make a new hash via the literal constructor, just like arrays.

**THE LITERAL CONSTRUCTOR**

```
my_hash = {}
  => {}
```

Initializing a hash that already contains data is a lot like initializing an array with data. We use the literal constructor:

```
pets = {"cat" => "Maru", "dog" => "Pluto"}
  => {"cat" => "Maru", "dog" => "Pluto"}
```

**THE CLASS CONSTRUCTOR**

```
grades = Hash.new
grades["Dorothy Doe"] = 9
```

##Retrieving Data from Hashes

Retrieving data from a hash is similar to retrieving data from an array, but instead of giving an array the index number in brackets `[i]` we give a hash the name of the key `[key]`. If an array is a list in which we access index items by their number, a hash is a dictionary in which we access values by their key.

```
pets = {"cat" => "Maru", "dog" => "Pluto"}

pets["cat"]
  => "Maru"
```

Using `[]` is referred to as the **"bracket method"**. It is actually a method just like any other––just like the methods you've been defining and like the methods available on objects such as Strings.

##Adding Objects to Hashes

Adding items to hashes is easy. Instead of `<<` (the shovel method) that we use to add items to arrays, hashes use the "bracket-equals" method to add data. The full syntax for this takes the form of:

```
hash_name[new_key_name]= new_value
```

Let's think about the distinction between the `<<` method for arrays and the `[]=`, or bracket-equals, method of adding data to hashes.

Hashes are *not numbered lists*. The data stored in them is not indexed by number, but instead associated to a unique key. Consequently a hash has no concept of the "end of the list", like an array. So the `<<` method, which tells the array to find the end of the list and add the new item to the next index, can't work on a hash.

While we were busy plugging away at our shipping manifest, Herman discovered an attempt to smuggle ten (10) jars of molasses past the inspection. Since molasses isn't illegal Herman decided not to confiscate it, but we do need to add it to the manifest so the merchant gets taxed on it. Accomplishing this with our hash would look like this:

```
shipping_manifest = {
  "whale bone corset" => 5, 
  "porcelain vase" => 2, 
  "oil painting" => 3
}

shipping_manifest["jar of molasses"] = 10

puts shipping_manifest
#  └── shipping_manifest = {
  "whale bone corset" => 5, 
  "porcelain vase" => 2, 
  "oil painting" => 3, 
  "jar of molasses" => 10
}
```

**ANOTHER EXAMPLE OF ADDING TO HASHES**

When adding to nested hashes, you can the `.merge` or `.merge!` method (changes the original hash to include whatever is merged, therefore, changing the return value).

```
def adding_matz
# add the following information to the top level of the
 programmer hash
# :yukihiro_matsumoto => {
#   :known_for => "Ruby",
#    :languages => ["LISP, C"]
# }

	programmer_hash = 
 		{
        :grace_hopper => {
        	:known_for => "COBOL", 
        	:languages => ["COBOL", "FORTRAN"]},
        :alan_kay => {
          :known_for => "Object Orientation",
          :languages => ["Smalltalk", "LISP"]
        },
        :dennis_ritchie => {
          :known_for => "Unix",
          :languages => ["C"]
        }
     }

#below is an example of #merge

    programmer_hash.merge!(yukihiro_matsumoto:
     {known_for: "Ruby", languages: ["LISP, C"]})
end
```

You can also do the following:

```
programmer_hash[:yukihiro_matsumoto] = {:known_for =>
 "Ruby", :languages => ["LISP", "C"]}
     programmer_hash
```
**NOTE**: The value of a key can be overriden. The last assigned value will become the current value of the key.

**ADDING TO ARRAYS NESTED INSIDE OF A HASH**

To add a value to an array inside of a hash (without *replacing* the original value), you can use the shovel method (`<<`):

```
def adding_to_dennis
  programmer_hash = 
    {
        :grace_hopper => {
          :known_for => "COBOL",
          :languages => ["COBOL", "FORTRAN"]
        },
        :alan_kay => {
          :known_for => "Object Orientation",
          :languages => ["Smalltalk", "LISP"]
        },
        :dennis_ritchie => {
          :known_for => "Unix",
          :languages => ["C"]
        }
     }

#Here is an example of the shovel method

     programmer_hash[:dennis_ritchie][:languages] <<
      "Assembly"
     programmer_hash
end
```
**Option 2:**

If the above option isn't clear, we can break it down step by step.

+ First, grab the value (which itself is another hash) of the "Jon Snow" key, and store it in a variable called `jon_snow: jon_snow = contacts["Jon Snow"]`

+ Second, grab the value of Jon Snow's favorite ice cream, and store that array in a variable: `jons_fav_ice_cream = jon_snow[:favorite_icecream_flavors]`

+ Third, add the new ice cream to `jons_fav_ice_cream` using the shovel (`<<`) operator: `jons_fav_ice_cream << "mint chip"`

```
jon_snow = contacts["Jon Snow"]
jons_fav_ice_cream = jon_snow[:favorite_icecream_flavors]
jons_fav_ice_cream << "mint chip"
```
```
puts contacts 
#  > {
  "Jon Snow" => {
    name: "Jon",
    email: "jon_snow@thewall.we", 
    favorite_icecream_flavors: ["chocolate", "vanilla",
     "mint chip"]
  },
  "Freddy Mercury" => {
    name: "Freddy",
    email: "freddy@mercury.com",
    favorite_icecream_flavors: ["strawberry", "cookie
     dough", "mint chip"]
  }
}
```

##The `#each` Method and Hashes

The `#each` iterator that we encountered in previous units can also be used to iterate over hashes. When we iterate over arrays, we iterate over one element at a time––each index in an array contains just one object. In a hash however, data is stored in key/value pairs so we will be iterating over those pairs. Let's take a look:

```
hash = {key1: "value1", key2: "value2"}

hash.each do |key, value|
  puts "#{key}: #{value}"
end
```

When we iterate over a hash, the `#each` method (as well as any other iteration method you use) yields the key/value pair together into the block. Inside that block, you have access to the key and the value, and can manipulate either one or both.

Drop into IRB and enter in the above code. You should see this output:

```
key1: value1
key2: value2
 => {:key1=>"value1", :key2=>"value2"} 
```

Inside the iteration we have access to, we can puts out the key and value of a single pair. The return value, however, is always the original hash. Remember that `#each` returns the original collection on which you are calling the method.

##Using `#collect`

We use `#collect` to iterate over a collection of data, such as an array or a hash, and return a collection of the data therein.

We have seen it used with arrays to iterate over an array, operate on the data it contains, and return a collection with this altered data.

When working with hashes, we'll most often see `#collect` used to collect the values of the hash's keys and/or collect data that we've operated on over the course of an iteration.

Let's take a look at an example.

**#COLLECTING HASH VALUES**

Let's use `#collect` to return all of the values of the keys in a given hash.

In this example, we are the managers at Chucky Cheese. Chucky Cheese is a great place to have a birthday party, and there are several birthdays going on here today.

We will be operating on the following hash that tracks birthday kids and their associated ages:

```
birthday_kids = {
    "Timmy" => 9, 
    "Sarah" => 6, 
    "Amanda" => 27
}
```

Our managers have asked us to give them the list of ages of the birthday kids so they know how many candles to buy for the birthday cakes. Let's iterate over the `birthday_kids` hash and collect the ages.

```
birthday_kids = {
    "Timmy" => 9, 
    "Sarah" => 6, 
    "Amanda" => 27
}

birthday_kids.collect do |kids_name, age|
    age
end

# => [9, 6, 27]
```

Note that the return value is an *array* of the values we collected.

###Advanced Example

You might have noticed that our previous example didn't operate on the data we were collecting. Let's step it up a level. In this example, we'll iterate over the `birthday_kids` hash using `#collect` and return the age of each child, in dog years:

```
birthday_kids.collect do |name, age|
    age * 7
end
```

In this case, we are multiplying the value of each `age` by `7` and collecting the return values of that multiplication into a new array. The above method call would return:

```
[63, 42, 189]
```

##Nested Hashes

So far, we've seen hashes that store values in associated keys. In the hashes we've built up until now, each key points to a single value. Hashes are so useful, however, because they can be multi-dimensional, or nested. A key in a hash can point to a value that is a *collection of objects*, i.e. an array or even another hash.

As programmers, we strive to write code that models the real-world. The programs we write serve a purpose––whether you're creating a simple command line game or an app to help hospitals manage patient data, our code is designed to do a real job, like run a game or communicate critical information.

**WHERE WILL YOU FIND NESTED HASHES?**

Nested hashes are a very common way to store and operate on complex associated data in a program. You are likely to encounter them any time you find yourself working with a large collection of information. In particular, you will encounter these data structures when working with data you will pull from APIs.

API stands for "Application Programming Interface" and here refers to the way in which organizations, companies and governments will expose their data to the public for use.

New York City, for example, has a robust API called NYC Open Data. Developers can connect to this API to find information about city programs, public housing, parks, schools, construction, health information––like this collection of NYC doctors who participate in project REACH––you name it.

**Top-Tip**: To view the nested hash data from the NYC Open Data API, linked to above, in an organized and legible way in your browser, use the Chrome JSON Viewer extension.

When you send a request for data to such an API, the data you get back from them will be in the form of a nested hash that can contain information about thousands of records.

We'll learn a lot more about APIs later on in this course. For now, just understand that nested hashes are a very common occurrence in programming. They are used to store complex collections of data and you will encounter them when working with APIs, among other places.

**NESTED HASHES MODEL REAL-WORLD DATA**

We can imagine so many real-world situations and environments in which we are dealing with complicated collections of data.

Let's take, for example, a list of instructors at the Flatiron School. They can be stored in an array like this:

```
instructors = ["Avi", "Jeff", "Rose"]
```

What happens when we expand our data collection to include the members of the development team here at the Flatiron School? We could create yet another array:

```
dev_team = ["Jonas", "Logan", "Amanda", "Seiji", "Kate", "Spencer"]
```

With this set-up, though, there is no way to associate our two arrays and tell them that they both belong to the Flatiron School.

This is where nested hashes come in.

**Our First Nested Hash**

With a nested hash, we can store complex collections of data. In other words, we can store data that is associated to other data via categories and subcategories.

Both the `dev_team` array and the `instructors` array should be associated to the Flatiron School. So, we can create a hash, `flatiron_school`, that contains keys to denote the `instructors` and `dev_team` categories. We can point these keys to the arrays that contain our instructors and dev team members respectively.

Let's take a look:

```
flatiron_school = {
  instructors: ["Avi", "Jeff", "Rose"], 
  dev_team: ["Jonas", "Logan", "Amanda", "Seiji", "Kate", "Spencer"]
}
```

In this example, each key in our hash points to a value that is an array.

Nested hashes allow us to further group, or associate, the data we are working with. They help us to deal with situations in which a category or piece of data is associated not just to one discrete value, but to a collection of values. In such a situation, we can create a hash key that points to a value of another hash or an array.

**A NOTE ON MIXING OUR COLLECTION TYPES**

You may have noticed in the above example that we have a hash in which the value of a key is an array.

Understand that arrays and hashes can store *any type of data*. In other words, the individual index items of an array can be strings, integers, or even other arrays and hashes. The same is true of hashes. The values that hash keys point to may be strings, integers, even arrays and hashes.

In fact, one of the most common nested data structures you'll see when working with APIs, as discussed above, is an array of hashes.

**Nested Hash Example**

We'll be building up our own nested hash and operating on such data structures in a number of ways over the course of the next few lessons. For now, just read through the next example and get comfortable looking at a nested hash.

In this example, we have a hash, `flatiron_school`, which stores some data about us. This data is broken down into the categories of `:instructors`, `:dev_team` and `:students`, thanks to our nested hash.

```
flatiron_school = {
  instructors: ["Avi", "Jeff", "Rose"], 
  dev_team: ["Jonas", "Logan", "Amanda", "Seiji", "Kate", "Spencer"],
  students: ["Sarah", "you", "John", "David"]
}
```

In the above example, each key points to an array of names. We may not know how to work with nested hashes just yet, but we do know how to work with one-dimensional hashes and arrays.

The `flatiron_school` hash has a key of `:instructors`. The value of that key is an array of instructors. In order to access that array, we can use the `[]` method we've been using all along to grab the values of a particular hash key.

```
instructors = flatiron_school[:instructors]
 # => ["Avi", "Jeff", "Rose"]
```

Here, we set a variable, instructors, equal to the return value of calling `flatiron_school[:instructors]`, which is simply the array of instructors.

Now, to operate on that collection of instructors, we can simply operate on our instructors array.

What if I wanted to grab just the first name in the instructor's array? We use the same methods for accessing array index items that we've been using all along:

```
instructors[0]
#  => "Avi"
```

Nested hashes can get pretty complicated. Read through the example in this lesson again before moving on. It's okay if you don't understand everything; just try to get comfortable reading through the above nested hash.

##Nested Hash Iteration

What happens when we want to iterate over a multidimensional hash like the one below? Let's iterate over our nested hash one level at a time; iterating over the first level of our hash would look like this:

```
contacts = {
  "Jon Snow" => {
    name: "Jon",
    email: "jon_snow@thewall.we", 
    favorite_icecream_flavors: ["chocolate", "vanilla", "mint chip"]
  },
  "Freddy Mercury" => {
    name: "Freddy",
    email: "freddy@mercury.com",
    favorite_icecream_flavors: ["strawberry", "cookie dough", "mint chip"]
  }
}

contacts.each do |person, data|
  puts "#{person}: #{data}"
end
```

This should return:

```
Jon Snow:      
{ :name=>"Jon", 
  :email=>"jon_snow@thewall.we", 
  :favorite_icecream_flavors=>["chocolate", "vanilla", "mint chip"],
  :knows=>nil
}

Freddy Mercury: 
{ :name=>"Freddy", 
:email=>"freddy@mercury.com", 
:favorite_icecream_flavors=>["strawberry", "cookie dough", "mint chip"]
}
```

On the first level, the keys are our contacts' names, "Jon Snow" and "Freddy", and our values are the hashes that contain a series of key/value pairs describing them.

Let's iterate over the second level of our `contacts` hash. In order to access the key/value pairs of the second tier (i.e. the name, email, and other data about each contact), we need to iterate down into that level. So, we pick up where we left off with the previous iteration and we keep going:

```
contacts.each do |person, data|
  #at this level, "person" is Jon Snow or Freddy and
   "data" is a hash of key/value pairs
  #to iterate over the "data" hash, we can use the
   following line: 

  data.each do |attribute, value|
    puts "#{attribute}: #{value}"
  end
end
```

That should output the following:

```
name: Jon
email: jon_snow@thewall.we
favorite_icecream_flavors: ["chocolate", "vanilla", "mint chip"]
knows: nil

name: Freddy
email: freddy@mercury.com
favorite_icecream_flavors: ["strawberry", "cookie dough", "mint chip"]
```

Let's take is one step further and print out just the favorite ice cream flavors. Once again, we'll need to iterate down into that level of the hash, then we can access the favorite ice cream array and print out the flavors:

```
contacts.each do |person, data|
  #at this level, "person" is Jon Snow or Freddy and
   "data" is a hash of key/value pairs
  #to iterate over the "data" hash, we can use the 
  following line: 

  data.each do |attribute, value|
    #at this level, "attribute" is describes the key
     of :name, :email, :favorite_icecream_flavors, or 
     :knows
    #we need to first check and see if the key is 
    :favorite_icecream_flavors,
    #if it is, that means the VALUE is an array that 
    we can iterate over to print out each element

    if attribute == :favorite_icecream_flavors
      value.each do |flavor|
        # here, each index element in an ice cream
         flavor string
        puts "#{flavor}"
      end
    end
  end
end
```

This should output:

```
chocolate
vanilla
mint chip
strawberry
cookie dough
mint chip
```

##Helpful hash methods

###`.VALUES`

You can collect all of the values in a hash with the `.values` method:

```
groceries = {fruit: "Banana", vegetable: "Broccoli",
 dessert: "Cookie"}

groceries.values
#  => ["Banana", "Broccoli", "Cookie"]
```

We can see that the `.values` method returns an array of the values of the keys in the hash.

###`.KEYS`

This method, not surprisingly, returns an array containing all of the keys in the hash that `.keys` has been called on:

```
groceries = {fruit: "Banana", vegetable: "Broccoli",
 dessert: "Cookie"}

groceries.keys
#  => [:fruit, :vegetable, :dessert]
```

###`.MIN`

You can use the `.min` method on a hash to return the key/value *pair* that contains that **lowest** value. What return type can return two different things? Arrays!:

```
food_items = {apples: 45, pears: 12}

food_items.min
#  => [:apples, 45]
```

These are only a few of the many helpful methods out there. Be sure to check out the Ruby Docs on Hashes to learn more.


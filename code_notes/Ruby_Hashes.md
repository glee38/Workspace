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

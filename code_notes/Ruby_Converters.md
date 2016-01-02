#Converting Types

In Ruby, there are a few methods available to us for converting data types. For example, it is possible to convert a string to an array, an array into a string, or a range into an array, among other conversions. You can learn more about conversion [here] (http://www.dotnetperls.com/convert-ruby), but for now we'll just take a look at a few.

##String to Array

The `.split` method will convert a string into an array. The `.split` method takes an argument of the character in the string on which you want to split it. For example, if we have a string that contains a comma-separated list of items:

```
"hippo,giraffe,monkey,horse"
```

We can call `.split` on it, with an argument of a `,` ("comma") to get an array of animal names:

```
"hippo,giraffe,monkey,horse".split(",")
  => ["hippo", "giraffe", "monkey", "horse"]
```

##Range to Array

The `.to_a` method, when called on a range, can convert a range of numbers to an array:

```
(1..10).to_a
  => [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

##Array to String

The `.join` method, when called on a array, will convert it into a string. This method takes in an optional string argument containing either a character or a set of characters that will be inserted between each array element, as they are assembled into a string.

For example, calling `.join` with no argument will create a string out of the array's elements by placing each one directly adjacent to the next one:

```
["a", "b", "c"].join
  => "abc"
```

But calling `.join` on the same array and giving it an argument of a " `:-)` " ("smiley face") will place an occurrence of that string, (i.e. the smiley face) between each letter:

```
["a", "b", "c"].join(" :-) ")
  => "a :-) b :-) c"
```
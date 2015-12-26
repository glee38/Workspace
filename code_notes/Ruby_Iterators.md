#Ruby Iterators

##Using `#each`

The `#each` method is a prime example of an iterator. Here's a boilerplate example of it's usage:

```
primary_colors = ["Red", "Yellow", "Blue"]
primary_colors.each do |color|
  puts "Primary Color #{color} is #{color.length}
   letters long."
end
```

`#each` is called on the collection `primary_colors`, which is an array containing 3 individual strings.

A block is passed to `#each`, opened by the code that starts with `do` and closed by the preceding `end`. Every `do` needs a closing `end`.

```
primary_colors = ["Red", "Yellow", "Blue"]
primary_colors.each do |color| # do begins a block
  # the lines between the do/end are the block's body
  puts "Primary Color #{color} is #{color.length}
   letters long."
end # end terminates the block
```

The output from this code is:

```
Primary Color Red is 3 letters long.
Primary Color Yellow is 6 letters long.
Primary Color Blue is 4 letters long.
```

We can see that the block passed to each is executed once for each element in the original collection. If there were 5 colors in `primary_colors`, the block would have run 5 times. We call each run, each execution, of the block passed to the iterator (`#each` in this case), an iteration. It's a word used to refer to each 'step', or each 'execution', of a block. An iteration is the singular execution of a sequence of code (that we call a block) within a loop.

When we iterate over a collection of elements using `#each` (and also in other iterators and enumerables we'll soon learn about), the iterator `#each` yields each element one at a time to every iteration via a variable declared with the opening of the block.

After the opening do of our code above, we see `|color|`. `|` is called a pipe. After `do`, we declare a local variable color by enclosing it in `| |` pipes. This variable's value is automatically assigned the element from the array for the current iteration. So on the first iteration of the each above, the variable color would be equal to `"Red"`. But on the next iteration of the block, color will be reassigned the value of the next element in the `primary_colors` array, `"Yellow"`.

Let's take a closer look at some of these concepts.

**WHAT IS A BLOCK?**

A block is a chunk of code between braces, `{ }` or between `do`/`end` keywords that you can pass to a method almost exactly like you can pass an argument to a method. There are some methods, like iterator methods, that can be called with a block, i.e. accompanied by a block denoted with `{ }` or `do`/`end`. Such a method would run and pass, or yield, data to the code in the block for that code to operate on or do something with.

Blocks are part of what make the Ruby language special, powerful, and loved.

**WHAT ARE THE `| |`?**

Those are called "pipes". When invoking an iterator like `#each`, the variable name inside the pipes acts as an argument that is being passed into the block. The iterator will pass, or yield, each element of the collection on which it is called to the block. Each element, as it gets passed into the block, will be equal to the variable name inside the pipes. Think of it like this:

+ Call, or run, the code in the block once for each element of the collection.

+ Pass a single element of the collection into the block every time the code in the block is called, or run. Start with the first element in the collection, and then move on to the second element, then the third, etc.

+ Every time you call the code in the block and pass in an element from the collection, set the variable name from between the pipes equal to that element.

This is exactly what happens when you define a method to accept an argument and then call that method with a real argument:

```
def hi_there(name)
  puts "Hi, #{name}"
end

hi_there("Sophie") # > "Hi, Sophie"
# => nil 
```

Think of the variable between the pipes like the name variable we are using to define our argument.

The variable name inside the pipes is more or less arbitrary. For example:

```
brothers = ["Tim", "Tom", "Jim"]
brothers.each do |brother|
  puts "Stop hitting yourself #{brother}!"
end
```

Will output the same thing as:

```
brothers = ["Tim", "Tom", "Jim"]
brothers.each do |hippo|
  puts "Stop hitting yourself #{hippo}!"
end
```

Which is:

```
Stop hitting yourself Tim!
Stop hitting yourself Tom!
Stop hitting yourself Jim!
```

We should, however, be reasonable and sensical when we name our variables. If your collection is called `brothers`, name the variable between the pipes `brother`. If you collection is called `apples`, name your variable `apple`.

**A CLOSER LOOK**

Let's revisit our example from above and break it down, step by step:

```
brothers = ["Tim", "Tom", "Jim"]
brothers.each do |brother|
  puts "Stop hitting yourself #{brother}!"
end
```

Here, the `#each` method takes each element of the brothers array, one at a time, and passes, or **yields**, it into the block of code between the `do`/`end` keywords. It makes each element of the array available to the block by assigning it to the variable `brother`. It does so by placing that variable name in between the pipes `| |`.

In summary, `#each` yields each item of the collection on which it is called to the block with which it is called. It keeps track of which element of the collection it is manipulating as it moves through the collection. During the first step of the iteration, `#each` will yield the first array element to the block. At that point in time, inside the block, `brother` will equal `"Tim"`. During the second step of the iteration, `brother` will equal `"Tom"` and so on.

Iterators like `#each` are smart – they don't need a separate counter variable and manual incrementation of that variable to know how many times to do something. They use the number of items in the collection on which they are called to determine how many times they will do something.

Let's set a counter variable and manually increment it in order to see the `#each` method in action:

```
brothers = ["Tim", "Tom", "Jim"]
counter = 1
brothers.each do |brother|
  puts "This is loop number #{counter}"
  puts "Stop hitting yourself #{brother}!"
  counter += 1
end
```

Copy and paste the above code into IRB. You should see this output:

```
This is loop number 1
Stop hitting yourself Tim!
This is loop number 2
Stop hitting yourself Tom!
This is loop number 3
Stop hitting yourself Jim!
#=> ["Tim", "Tom", "Jim"]
```

See that, during loop number 1, the string `"Tim"` was yielded to the block and the variable name `brother`, when interpolated into the string we `#putsed` out, was set equal to `"Tim"`. During loop number 2, the same thing happened with `"Tom"`, and during loop number 3, the same thing happened with `"Jim"`. There was no loop number four because the `#each` iterator operated on each member of the array on which it was called and then stopped.

**A NOTE ON RETURN VALUES**

Different iterators have different return values. Notice that the return value of the call to `#each` above returned `["Tim", "Tom", "Jim"]` – the original array. The `#each` method will always return the original collection on which it was called.

**THE `{ }` SYNTAX**

Another way of establishing a code block that you may encounter is to use curly brackets, `{ }`, instead of the `do`/`end` keywords. Let's take a look:

```
brothers = ["Tim", "Tom", "Jim"]
brothers.each{|brother| puts "Stop hitting yourself #{brother}!"}
```

It is appropriate to use the `{ }` syntax when the code in the block is short and can fit on one line.

**Conclusion**

Both loops and iterators are powerful tools in Ruby, but they're not right for every job. Loops are useful when you need to tell your program to do something a certain number of times or to do something based on a certain condition. Iterators are useful for operating on a collection of objects, and even performing complex operations on the members of that collection. Because iterators are called with blocks, it's easy to carry out complex logic or tasks using each individual member of a collection of objects.

##RETURN VALUES

###Each

The most important thing to remember about each is that it does not change the return value. It implicitly returns the original array.

```
toppings = ["pickles", "mushrooms", "bacon"]

def hamburger(toppings)
  toppings.each do |topping|
    puts "I love #{topping} on my burgers!"
  end
end
```

This method will print:

```
I love pickles on my burgers!
I love mushrooms on my burgers!
I love bacon on my burgers!
```

But the return value is still:

```
["pickles", "mushrooms", "bacon"]
```

If we want a different return value, we have to explicitly tell it to do so.

In this version of our burger method we set an empty array called `my_statements`, which we will then explicitly return after we finish our loop.
Inside our `each` statement loop, we manipulate each topping by interpolating it inside a string. We then push that string into our `my_statements` array.
After we iterate over each topping in our array, we return the new `my_statements` array.

You'll notice that since the `each` doesn't return the thing we want, we have to add an extra line at the end that returns the `my_statements` array.

```
def hamburger(toppings)
  my_statements = []
  toppings.each do |topping|
    my_statements << "I love #{topping} on my burgers!"
  end
  my_statements
end
```

OUR NEW RETURN VALUE:

```
[
  "I love pickles on my burgers!"
  "I love mushrooms on my burgers!"  
  "I love bacon on my burgers!"
]
```

However, if we do want a different return value, there is a handy method called `map`, also known as `collect`. These methods are abstractions of our each method. **An abstraction is the process of taking away or removing characteristics from something in order to reduce it to a set of essential characteristics.** Let's take a look at a few examples.

###Map & Collect

```
toppings = ["pickles", "mushrooms", "bacon"]

def hamburger(toppings)
  toppings.map do |topping|
    puts "I love #{topping} on my burgers!"
  end
end
```

Since `map` and `collect` are the same thing, this can be expressed exactly the same way with `collect`, like the following.

```
def hamburger(toppings)
  toppings.collect do |topping|
    puts "I love #{topping} on my burgers!"
  end
end
```

This method will print:

```
I love pickles on my burgers!
I love mushrooms on my burgers!
I love bacon on my burgers!
```

This method will return:

```
[nil, nil, nil, nil]
```

Why does it return `nil`?

If you look inside our `map` loop, you will see that we are using `puts`, which always has a `nil` return value. What this is telling us is that our return value is indeed being changed by `map`. Let's look at another example.

Here we are no longer using `puts`, but instead implicitly returning what is inside our block. Again showing that `map` will give us a new return value based on the logic inside our block.

```
def burger(toppings)
  toppings.collect do |topping|
    "I love #{topping} on my burgers"
  end
end
```

Our new return value:

```
[
  "I love pickles on my burgers!"
  "I love mushrooms on my burgers!"  
  "I love bacon on my burgers!"
]
```

**TAKEAWAY:**

+ If you want want the transformations to be reflected in the return value use `map` or `collect`.
+ If you want to return the original return value use `each`.

##YIELD

###The `yield` Keyword

The `yield` keyword, when used inside the body of a method, will allow you to call that method with a block and pass, or "yield", to that block. Think of the `yield` keyword as saying "stop executing the code in this method and instead execute the code in the block. Then, return to the code in the method."

Let's look at the following example:

```
def yielding
  puts "the program is executing the code inside the
   method"
  yield
  puts "now we are back in the method"
end
```

To call this method with a block, we use the following syntax:

```
yielding {puts "the method has yielded to the block!"}
```

or:

```
yielding do 
  puts "the method has yielded to the block!"
end
```

When we call `yielding` with the above block, we will output:

```
"the program is executing the code inside the method"
"the method has yielded to the block!"
"now we are back in the method"
```

###Yielding With Parameters

The `yield` keyword can take parameters. In other words, if you use `yield` and give it an argument, it will pass that argument to the block and that data will become available to the code in the block.

For example:

```
def yielding_with_arguments(num)
  puts "the program is executing the code inside the method"
  yield(num)
  puts "now we are back in the method"
end
```

To call this method, we invoke our method and give it an argument and we accompany that method call with a block that contains a placeholder for the yielded value, plus whatever code we want to enact with that yielded value:

```
yielding_with_arguments(2) {|i| puts i * 2}
```

We call our method with an argument:

```
yielding_with_arguments(2)
```

and a block:

```
{ |i| puts i * 2 }
```

The `|i|` (placeholder variable in between pipes) is our placeholder for the yielded value. The `i * 2` is the code we actually want to enact with our yielded value.

So, the above method call will output:

```
"the program is executing the code inside the method"
4
"now we are back in the method"
```

The syntax inside the block might look familiar—it is how we identify index items in a block when we call `#each` on an array and pass a block to that method call.

##Building Our Own Method `#each` method with `Yield`

Let's revisit our earlier example of a call to the `#each` method that only puts out a greeting if the word we pass into the block starts with the letter `"T"`.

```
["Tim", "Tom", "Jim"].each do |name|
    if name.starts_with?("T")
        puts "Hi, #{name}"
    end
end
```

In this example, we'll be building our own method, `#hello_t`.

####Step 1: Defining our method to accept an argument

Our method needs to operate on an array so let's define it to take in an argument:

```
# lib/hello.rb

def hello_t(array)
    # code here
end
```

####Step 2: Enacting an iteration

We know that we want to `yield` each element of the array successively to a block that we will call this method with. Let's use a `while` loop to create our iteration:

```
def hello_t(array)
  i = 0
  while i < array.length
    i = i + 1
  end
end
```

####while Loop Review

In the code above, we set a counter variable, `i`, equal to `0`. We start our `while` loop and tell it to execute the code in between the `while` and `end` keywords as long as `i` is less than or equal to the length of the array. Inside our `while` loop, we increment the value of our `i` variable.

Now, we need to tell our method to actually yield each member of the array, as we loop over it, to the block we will call our `#hello_t` method with.

####Step 3: Yielding to the Block

The first time through our `while` loop, `i` is set equal to zero. The second time through the loop, `i` is set equal to 1, and so on. This will go until until `i` is equal to the last index number of our array.

So, inside each step of the `while` loop, `i` equals a given index number of our array. We can use this information to yield each successive value stored in our array, to our block.

```
def hello_t(array)
  i = 0
  while i < array.length
    yield array[i]
        i = i + 1
  end
end
```

Here, we use the `[]`, bracket, method to grab the value of each successive index element as we proceed through our while loop, and yield it to a block. Now we're ready to call our method:

####Step 4: Calling Our Method

Let's call our method with an argument of the following array:

```
["Tim", "Tom", "Jim"]
hello_t(["Tim", "Tom", "Jim"]) do |name|
    if name.start_with?("T")
        puts "Hi, #{name}"
    end
end
```

You should see the following:

```
Hi, Tim
Hi, Tom
```

We call our method with the argument of our array and accompany that method call with a block that contains the name parameter and the desired code to puts out a greeting if the person's name begins with the letter `"T"`. Good job!

####Step 5: Passing Our Test

Go ahead and run the test suite by typing learn into your terminal in the directory of this lesson. You'll see that our test is expecting us to return the original array but our method is currently returning nil.

How can we fix this? We can tell our `#hello_t` method to return the original array:

```
def hello_t(array)
  i = 0
  while i < array.length
    yield(array[i])
    i = i + 1
  end
  array
end
```

Here, we tell our method to return the original array simply by having that array be the last line of the method. Whatever is evaluated last in a method will be it's return value. If you run the test again, you should be passing.

####ADVANCED: DEFINING A METHOD TO OPTIONALLY TAKE A BLOCK

In the examples above, our methods will break if they are called without an accompanying block. We like our code to be flexible and accommodating. In other words, we don't want our code to break so easily.

Let's refactor our `#hello` method so that it can be called either with or without a block:

```
def hello_t(array)
  if block_given?
    i = 0
    while i < array.length
      yield(array[i])
      i = i + 1
    end
    array
  else
    puts "Hey! No block was given!"
  end
end
```

The `block_given?` method returns `true` if the method that contains `block_given?` is called with a block and `false` if it is not.

So, our `#hello_t` method will yield each element of the array to the block if a block is present, otherwise it will puts out a helpful phrase.

####Enumerators Under the Hood

You've already worked with enumerator methods like `#each`, `#collect` and others. These methods are called on collections, like arrays. They take blocks as their arguments and yield each element of the collection to the block, allowing the code in the block to be applied to each element of the collection.

In the code-along above, we built our own implementation of the `#each` method. We used `while` to loop, or iterate, over each element of the array and passed, or yielded, each of those elements to an accompanying block. That block used parameters, `|i|`, to set a variable, `i`, equal to whatever value is yielded into the block at each successive step of the iteration. That block also contained code to execute with each yield element in turn.

##Building a mannual `#collect`

Now that we understand that calling `yield(some_argument)` will give us the return value of the execution of the block with that argument, we can capture those return values.

```
def hello(array)
  i = 0
  collection = []
  while i < array.length
    collection << yield(array[i])
    i += 1
  end
end
```

Here we are setting a variable, `collection`, equal to an empty array. Then, inside our `while` loop, we push the return value of using `yield(array[i]) into that `collection` array.

Lastly, we need to return that new collection at the end of our method:

```
def hello(array)
  i = 0
  collection = []
  while i < array.length
    collection << yield(array[i])
    i += 1
  end
  collection
end
```

And that's it! We've successfully built our own iteration using a `while` loop, yielded each individual member of an array to a block and captured the return values of yielding those items to the block. This is exactly how the `#collect` method works, and we've just built it out, all by ourselves.
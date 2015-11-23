#RSPEC

##WHAT'S A TEST?

Tests verify that the code you write behaves and produces the desired result. Many of the labs you will complete on Learn use tests. It can feel like an abstract concept at first, but it's worth starting to understand. Doing so will help you be more productive on Learn. Beyond that, understanding testing is important because test-driven development, or TDD, is considered the most reliable methodology for delivering quality code.

##WHAT IS TEST DRIVEN DEVELOPMENT?

Test Driven Development, or TDD, is a method for approaching a problem not through the implementation of the solution, but rather, through the expectations of a working solution. What that means is, instead of trying to write the code that solves the problem, you first define what the working code will do when it works, and then you write the implementation to make it work.

###TEST DRIVING FIZZBUZZ
A classic programming problem is FizzBuzz. It is considered the Stairway to Heaven of programming because there are so many different ways to play it and everyone plays it.

The goal of fizzbuzz is to build a program that can take a number and if the number is evenly divisible by 3, it should return "Fizz", if it's divisible by 5, it should return "Buzz", and if it's divisible by both 3 and 5, it should return "FizzBuzz".

###DEFINING OUR EXPECTATIONS

Let's approach solving this problem from a TDD approach. That means what we don't care about is **"how"**, but rather, we care about **"what"**. **What will the program do if it works correctly, not how will it do it.** We are going to write our expectations first, then our implementation.

Before we look at the RSpec syntax, let's just express our tests in plain English (also sometimes referred to as "pseudo-code").

+ We expect fizzbuzz(3) to return "Fizz"
+ We expect fizzbuzz(5) to return "Buzz"
+ We expect fizzbuzz(15) to return "FizzBuzz"

Furthermore, we could also provide a negative case.

+ We expect fizzbuzz(4) to return nil or nothing or ""

We don't care how that fizzbuzz method works, or even that it's defined, we are just *stating our expectations*. And we're doing that first. When coding, it's important to have a target to shoot for; by writing your test first and stating your expectations of your code, you know your goal. You get to use your entire brain to think about just your goals, not how you'll get there, which adds clarity and focus to the problem.

**The competent programmer is fully aware of the strictly limited size of his own skull; therefore he approaches the programming task in full humility. — Edsger W. Dijkstra**

Test-Driven Development allows us to fully utilize our mental capacity to clearly state the problem we're solving and then to 100% focus on implementing a solution. When you are just coding, you're actually focusing on stating the problem and the solution at the same time. You are thereby splitting your thinking in two, making you less effective at both.

**It's the exact same reason you make a todo list. Also testing is not for present you, it's for future you. — Joe M Burgess**

##INTRODUCTION TO RSPEC
RSpec is what's known as a DSL, or Domain Specific Language (also called a "Task Oriented Language" - a language designed for a specific kind of problem). It's a set of vocabulary (but actually just methods) that is useful for completing a specific kind of task. In this example, that task is describing our expectations and tests.

Here is an example of an RSpec test for one of the expectations above:

```
describe "fizzbuzz" do
  it 'returns "Fizz" when the number is divisible by 3' do
    fizz_3 = fizzbuzz(3)

    expect(fizz_3).to eq("Fizz")
  end
end
```

Sure, that code looks crazy and it's actually quite advanced, but we don't need to think about all of it, we just need to focus on 5 words: `describe`, `it`, `expect`, `to`, and `eq`. 

Let's break down that example.

###DESCRIBE

The first thing RSpec allows you to do with its DSL is to define what it is you are describing. As in, are we writing a test that describes how our Billing System works or are we writing a test that describes how fizzbuzz works?

RSpec gives us the `describe` method. The argument we pass to the `describe` method is arbitrary; it doesn't really do anything besides document what exactly it is we're testing. Since we're writing a specification (a test) for `fizzbuzz`, it makes sense to pass describe the string `"fizzbuzz"`. `describe` also accepts a block, which will be covered shortly, but for now, just know that a block in ruby starts with the `do` keyword. Every block that is opened must be closed. We use the `end` keyword to close the describe block.

The `describe` block can be thought of as a template for a spec (a test).

```
describe "The Thing We're Testing" do
  # Specifications, that is, specific test examples, 
  go here.
end
```

###WTF IS A BLOCK?
A block is a simple unit of code that starts with the `d`o keyword and ends with the `end` keyword. Blocks are a complex and wonderful part of the Ruby language and we don't need to focus on exactly what they are right now. It's best to think of a block just as a word used to describe a certain chunk, or unit, of code (almost like a paragraph). You can generally tell that something is a block because it is indented and grouped together. The aesthetics, the very form of the code, like in a poem, suggest a structure. That's a block.

**Resources on Blocks:**

+ Why's Guide to Blocks
+ Understanding Ruby Blocks, Procs, and Lamdbas
+ Codecademy on Blocks
+ RubyMonk on Blocks

###IT

Now that we've created a structure to group our tests together using the `describe` method, we can move onto actually describing the desired functionality. Every specification in RSpec begins with the `it` method.

The `it` method, like the `describe` method, accepts an arbitrary string as an argument, namely, some text that says what we're currently interested in testing. Like all things in code, every it should be specifying one, only one, and exactly one, desired outcome or behavior.

Above we said that one functionality of `fizzbuzz` is that it returns "Fizz" if the number is divisible by 3. So we can simply write that. It's totally up to you, the programmer, to decide the phrasing for your `it` specification, but a good starting point is a verb or action. `it returns` or `it prints` or `it emails` or `it validates` etc.

Also like the `describe` method, the `it` method accepts a block, beginning with the `do` keyword and ending with the `end` keyword, that will encapsulate, or group, the code that will test our expectation.

The `it` block template might look something like:

```
it 'returns "Fizz" when the number is divisible by 3'
 do
  # Code that actually does what we're testing and 
  confirms the desired result
end
```

So far, if we think of our RSpec test as a form of documentation, we can imagine it might read something like:

```
fizzbuzz
  returns "Fizz" when the number is divisible by 3
```

We're simply restating the problem and our expectations in the RSpec DSL. Now let's actually write the test for the specific example.

###EXPECT, TO AND EQ

Okay, let's refocus. Our goal is to test that when we call the `fizzbuzz` method, which we haven't even built yet, but in theory, when we call it, assuming it works correctly, when we call it and pass it the number 3, we expect it to equal "Fizz".

Outside of the context of our test suite, we could write an expression like:

```
fizzbuzz(3) == "Fizz"
```

That is to say, when we evoke `fizzbuzz(3)`, it should equal `"Fizz"`. Taking advantage of the RSpec DSL, we can express that assertion as follows:

```
# First, get the return value from fizzbuzz(3), store
 it in a variable, fizz_3
fizz_3 = fizzbuzz(3)

# Next, our expectation that the value of fizz_3 
should be "Fizz"
expect(fizz_3).to eq("Fizz")
```

So three new RSpec methods, `expect`, `to`, and `eq`.

`expect()` is a method that accepts our unknown value or variable, the thing we're testing. So for instance, in a simple math equation, imagine the following:

```
x = 1 + 1
expect(x)
```

Since `x` is the unknown variable, we'd be testing the expectation of the value of `x`, so we pass that value to the `expect` method. I can imagine it's weird to think of the variable `x` as an unknown value worth confirming. You're thinking, "it's obviously 2!", but the truth is, you're making the assumption that Ruby has a correct notion of arithmetic. As our programs become more complex and we use more variables, it's very important to constantly validate our assumptions with expectations and testing. Let's finish the example.

In addition to the `expect(x)` call, we need to communicate what we expect `x` to be equal to. We do this in a weird way, we chain a `to()` method to the `expect()`, so it simply looks like:

```
x = 1 + 1
expect(x).to
```

Then finally we use what is known as a matcher, `eq`, to specify our expectation: that we expect the result of our expectation, passed to the `expect` method, to equal, to `eq`, `2`.

```
x = 1 + 1
expect(x).to eq(2)
```

##READING RSPEC OUTPUT

Let's look at the output RSpec gave us and dig into it a bit.

###TEST RUN SUMMARY

rspec

```
Run options: include {:focus=>true}

All examples were filtered out; ignoring {:focus=>true}
FFFF
```

The first two lines of output are descriptive, RSpec is simply telling us what we did.

The line with `FFFF` is important. `F` stands for a failure. As the test suite runs, RSpec will spit out an `F` for every failed expectation (every `it` block). RSpec will spit out a `.` for every passing test. Right now, the entire test suite is failing, which makes sense. We haven't written any code! That's an important point, and I think Steve Klabnik said it best:

**Programming is a movement from a broken state to a working state. That means you spend the majority of your time with things being broken. Hell, if it worked, you'd be done programming.**

If you're new to programming, you're probably used to things working. So when they are broken, it's scary. In programming, you have to feel the exact opposite way. It is totally normal for everything to be broken. As you code, you fix things, one by one, and then when it all works, you're done for the day.

###READING A SPECIFIC EXAMPLE FAILURE
So, sure, we know philosophically that the reason why our tests are failing is because we did nothing to make them pass. But what, specifically, is the reason they are failing?

Our rspec output continues with a summary of each individual failure:

```
Failures:

  1) fizzbuzz returns "Fizz" when the number is divisible by 3
     Failure/Error: fizz_3 = fizzbuzz(3)
     NoMethodError:
       undefined method `fizzbuzz' for #<RSpec::Core::ExampleGroup::Nested_1:0x007fbdd2230428>
     # ./spec/fizzbuzz_spec.rb:5:in `block (2 levels) in <top (required)>'
```

+ In the failure output, the first line:

```
fizzbuzz returns "Fizz" when the number is 
divisible by 3
``` 

tells us the description of the test. 

+ The second line:

```
Failure/Error: fizz_3 = fizzbuzz(3) 
```
tells us the line that caused the failure or error. In this case, it is an error, and we see the Ruby error.

```
NoMethodError:
       undefined method `fizzbuzz' for #<RSpec::Core::ExampleGroup::Nested_1:0x007fbdd2230428>
     # ./spec/fizzbuzz_spec.rb:5:in `block (2 levels) in <top (required)>'
```

We have a `NoMethodError`. There is no method `fizzbuzz`, and the line that is important is line 5 of `/spec/fizzbuzz_spec.rb`. The rest of it is just RSpec noise (the thing about `RSpec::Core::ExampleGroup::Nested_1` is pretty meta and not worth going into here).

So, why did our test fail? Because we did not define a `fizzbuzz` method.

###SOLVING FIZZBUZZ

It is finally time to actually program a solution to fizzbuzz. The first question to answer is where do we put our solution, the actual code we care about, the program we're writing. Let's look at our current directory structure.

```
rspec-fizzbuz/
    spec/
        fizzbuzz_spec.rb
        spec_helper.rb
    .learn
    .rspec
    fizzbuzz.rb
    README.md  
```

`fizzbuzz.rb` is where we're going to code the core of our program––a simple method, `fizzbuzz` that when evoked in the tests above, will behave as we specified. For now though, let's just fix our first error.

The error we were dealing with is that there is no method known as `fizzbuzz`. Let's simply define that method.

File: fizzbuzz.rb

```
def fizzbuzz
end
```

Now, when we run our spec suite, we could expect to see a new sort of error. After all, we finally started solving `fizzbuzz` by at least defining a method to solve `fizzbuzz`.

###A BIT ABOUT YOUR TEST VS YOUR PROGRAM

Run: rspec

```
  1) fizzbuzz returns "Fizz" when the number is divisible by 3
     Failure/Error: fizz_3 = fizzbuzz(3)
     NoMethodError:
       undefined method `fizzbuzz' for #<RSpec::Core::ExampleGroup::Nested_1:0x007fd2f21083b8>
     # ./spec/fizzbuzz_spec.rb:5:in `block (2 levels) in <top (required)>'
```

Don't panic! It's the same exact error, our old friend, `NoMethodError`, as though we never defined `fizzbuzz`. But look, after all this, we finally did some work and created the `fizzbuzz.rb` with a clearly defined `fizzbuzz` method and our tests are still complaining that we didn't define that method. Why?

Let's try this, let's load a Ruby environment from within our project directory and just play with the `fizzbuzz` method ourselves for a second.

From within `rspec-fizzbuzz` fire up `irb`, the Interactive Ruby Shell, a REPL, or real time ruby prompt for executing arbitrary ruby. For instance (remember `$` means that command, irb was entered into your bash prompt):

```
$ irb
2.0.0-p247 :001 > 1+1
 => 2
2.0.0-p247 :002 >
```

From within your new IRB shell, try calling the fizzbuzz method that's defined in `fizzbuzz.rb`:

```
2.0.0-p247 :001 > fizzbuzz
NameError: undefined local variable or method `fizzbuzz' for main:Object
  from (irb):1
  from /Users/avi/.rvm/rubies/ruby-2.0.0-p247/bin/irb:16:in `<main>'
```

There's our error. We tried calling `fizzbuzz` and IRB complains that it doesn't know what we're talking about. Why? Well, when did we tell IRB to actually load the contents of our `fizzbuzz.rb`? We didn't. And no where in our test runtime did we tell our tests to actually load the contents, to read, to require, `fizzbuzz.rb`. 

Try this in IRB:

```
2.0.0-p247 :001 > fizzbuzz
NameError: undefined local variable or method `fizzbuzz' for main:Object
  from (irb):1
  from /Users/avi/.rvm/rubies/ruby-2.0.0-p247/bin/irb:16:in `<main>'

2.0.0-p247 :001 > require './fizzbuzz.rb'
=> true
2.0.0-p247 :001 > fizzbuzz
=> nil
```

As you can see, by **requiring** the `fizzbuzz.rb` file, we were able to call the `fizzbuzz` method.

Let's add that requirement, the loading of our actual program, to our test, so that when our tests run, they can rely on our program.

Edit: `spec/fizzbuzz_spec.rb`

```
require_relative './spec_helper.rb'
require_relative '../fizzbuzz.rb'
```

Now we're telling our `fizzbuzz_spec` to load both `spec_helper.rb` and `fizzbuzz.rb`.


Run the test suite one more time with rspec, you should see failures in line with:

```
  1) fizzbuzz returns nil when the number is not
   divisible by 3 or 5
     Failure/Error: fizz_4 = fizzbuzz(4)
     ArgumentError:
       wrong number of arguments (1 for 0)
     # ./fizzbuzz.rb:1:in `fizzbuzz'
     # ./spec/fizzbuzz_spec.rb:21:in `block (2 levels) in <top (required)>'
```

Read that error message; it's brand new! Getting a new error message is a sign of progress. It's one more hint the computer is giving you, one more clue to drive your investigation forward. Now it's complaining that our tests are calling the `fizzbuzz` method with an argument; however, our definition of `fizzbuzz` accepts no arguments. You'll fix that, but it's important to realize our test suite is now all wired up and we can continue building out our fizzbuzz method and running our specs against that code until we have it working.

###CONTINUING TO SOLVE FIZZBUZZ

That's the end of the tutorial part of this lab. You have enough about RSpec and Ruby to continue solving this in a test driven manner. As you define your `fizzbuzz` method, remember, you can always play with it in IRB. For instance, load an IRB terminal and try (line by line):

```
def fizzbuzz(int)
  if int % 3 == 0 # if the number int is divisible by 3
    "Fizz" # Go fizz
  end
end

fizzbuzz(3) # => You should see a return of "Fizz"
fizzbuzz(5) # => You should see a return of nil
fizzbuzz()  # => You should get an argument error
```

**Helpful Tip: You can try code in IRB and then copy it to your program files as you get it working. You can also use Pry by placing the line require 'pry' at the top of your fizzbuzz.rb and placing binding.pry inside the method before running RSpec.**


##UNDERSTANDING THE TEST IN THE SPEC DIRECTORY

Let's take a peek inside:

`spec/current_age_for_birth_year_spec.rb`

When we run our test program, `spec/current_age_for_birth_year_spec.rb`, that code will load the code in `current_age_for_birth_year.rb` and try to execute `current_age_for_birth_year(1984)` with the expectation that it returns `31`. If so, the test will pass. Anything else will make it fail.

File: `spec/current_age_for_birth_year_spec.rb`

```
require_relative '../current_age_for_birth_year.rb'

describe "current_age_for_birth_year method" do
  it "returns the age of a person based on the year of birth" do
    age_of_person = current_age_for_birth_year(1984)

    expect(age_of_person).to eq(31)
  end
end
```

Let's break this code down.

###How your test loads your program:

+ **The first line of the test**:

`require_relative '../current_age_for_birth_year.rb`

loads the code from our actual program file so that we can use all the code in that file in our test. That line connects our test to our actual program.

###The `describe` method in RSpec:

+ **The next line**:

`describe "current_age_for_birth_year method" do`

is the RSpec language and can basically be ignored for now beyond the actual semantics and meaning. We are simply saying, via valid Ruby, that this test describes the `current_age_for_birth_year` method.

The only things that are required in this line of code are the `describe` RSpec method and the Ruby `do` keyword, the rest of this line is entirely arbitrary and of our own design. After all, `current_age_for_birth_year` method is a String of data and could not possibly matter to Ruby because it is not interpreted, it's just data.

When we write tests we use the `describe` RSpec method and strings to describe what we are testing. This code is entirely for you, the programmer, and has very little meaning to RSpec or Ruby.

###The `it` method in Rspec:

After describing the subject of our test, the method current_age_for_birth_year, we use the RSpec method it to state an expectation or behavior of that method.

+ `it "returns the age of a person based on their year of birth" do` is very similar to the `describe` line.

+ `it` is an RSpec method

+ `do` is a Ruby keyword

`"returns the age of a person based on the year of birth"` is a Ruby string that has no meaning to the code and is only there to provide you, the programmer, with a description of what behavior we're currently testing.

###Testing our method:

The next three lines are our actual test code and the most important part of the `spec/current_age_for_birth_year_spec.rb` file. It is within this block of code––between `it` and `end`––that we test the functionality of our method.

In order to actually test our code, we need to use the method that this test relies on, that this test is designed to exercise. So the first real line of code in our test is:

	age_of_person = current_age_for_birth_year(1984)

What we're doing here is calling a method, `current_age_for_birth_year(1984)`, the very method we're supposed to define and implement, passing it a known argument, `1984`, and assigning the return value of the method to a variable called `age_of_person`.

What do you think the value of `age_of_person` should be if the method `current_age_for_birth_year` is called with `1984` as the argument?

The next line of code poses that exact question with an expected outcome. Using lots of RSpec methods and syntax, we say, quite colloquially:

`expect(age_of_person).to eq(31)`

What this line of code means is that we `expect` the value of the variable `age_of_person` to equal `31`.

That is to say, given that `age_of_person` is the return value of the method `current_age_for_birth_year(1984)`, we can expect that the variable equals `31`, the age of the person born in `1984`. That's a test.

Our test loads our code, uses our code in the manner desired, and compares the result of our code with a known outcome so that we know our code behaves as we expected.

We could imagine another specification of the `current_age_for_birth_year` method as another `it` block within the opening `describe` block:

```
it "should return the current year for a person born in year 0" do
  twenty_fifteen = current_age_for_birth_year(0)

  expect(twenty_fifteen).to eq(2015)
end
```

**A test is always going to be about setting up a state with a known result and comparing that known result or expectation to the behavior of your program, thus ensuring that your program behaves as you expected.**

##UNDERSTANDING TEST OUTPUT

When you run the tests with the learn command you're going to see the results of the test in your console. RSpec will report on what is working and what is broken and why.

When you run this lab's test suite with learn, before writing any solution code in `current_age_for_birth_year.rb`, you'll see output similar to:

```
current_age_for_birth_year method
  returns the age of a person based on the year of 
  birth (FAILED - 1)

Failures:

  1) current_age_for_birth_year method returns the age
   of a person based on the year of birth
     Failure/Error: age_of_person = current_age_for_birth_year(1984)
     NoMethodError:
       undefined method `current_age_for_birth_year' for #<RSpec::ExampleGroups::CurrentAgeForBirthYearMethod:0x007fbb8b0607b8>
     # ./spec/current_age_for_birth_year_spec.rb:5:in `block (2 levels) in <top (required)>'

Finished in 0.00063 seconds (files took 0.1535 seconds
 to load)
1 example, 1 failure

Failed examples:

rspec ./spec/current_age_for_birth_year_spec.rb:4 
# current_age_for_birth_year method returns the age of
 a person based on the year of birth
```

Let's break this down.

```
current_age_for_birth_year method
  returns the age of a person based on the year of
   birth (FAILED - 1)
```
  
Those lines are summaries of what we are testing and what failed. They correspond directly to the strings provided to `describe` and `it`, and they are simply there to provide context.

```
  1) current_age_for_birth_year method returns the age
   of a person based on the year of birth
     Failure/Error: age_of_person = current_age_for_birth_year(1984)
     NoMethodError:
       undefined method `current_age_for_birth_year'
        for #<RSpec::ExampleGroups::CurrentAgeForBirthYearMethod:0x007fbb8b0607b8>
     # ./spec/current_age_for_birth_year_spec.rb:5:in
      `block (2 levels) in <top (required)>'
```

This actually describes why our test failed.

```
  1) current_age_for_birth_year method returns the age of a person based on the year of birth
```

That line just joins the strings passed to describe and it to create a description of what broke.

```
     Failure/Error: age_of_person = current_age_for_birth_year(1984)
```

###NOMETHODERROR:

The above line raises the line of code in our test suite that created the failure and error. The rest of the output describes the error:

```
     NoMethodError:
       undefined method `current_age_for_birth_year'
        for #<RSpec::ExampleGroups::CurrentAgeForBirthYearMethod:0x007fbb8b0607b8>
     # ./spec/current_age_for_birth_year_spec.rb:5:in `block (2 levels) in <top (required)>'
```

Before writing any code, our test suite is failing because of a line of code within it:

	age_of_person = current_age_for_birth_year(1984).

This line tried calling a method, `current_age_for_birth_year`, which your test expected to have been defined. You have yet to define it, however, resulting in a `NoMethodError`.

**Tip: We can run our test suite as many times as we want, it's totally free. In fact, we suggest that every time you make a change to your code and think it might solve something in the test, run the test suite again. Run the test suite a lot; get instant feedback; read the errors, they are clues.**


##READING ERRORS AND MAKING OUR TESTS PASS
So, we conceptually understand what we're trying to build: a method called `current_age_for_birth_year`, that when given an argument of a year of birth, `current_age_for_birth_year(1984)`, returns the age of a person, `31`. Our test suite actually tries to execute this code and compares the result of it to the desired outcome, failing until the expectation and the outcome are equal.

The first error thrown by the test suite is that our code, defined in `current_age_for_birth_year.rb`, should have defined a method called `current_age_for_birth_year`, but did not, resulting in a `NoMethodError`.

Let's fix this error by defining a method in `current_age_for_birth_year.rb` called `current_age_for_birth_year`.

Add the following to the file, `current_age_for_birth_year.rb`:

```
def current_age_for_birth_year
end
```

Save the file and go back to your terminal and run the learn command. You'll see output including:

```
  1) current_age_for_birth_year method returns the age
   of a person based on the year of birth
     Failure/Error: age_of_person = current_age_for_
     birth_year(1984)
     ArgumentError:
       wrong number of arguments (1 for 0)
     # ./current_age_for_birth_year.rb:1:in 
     `current_age_for_birth_year'
     # ./spec/current_age_for_birth_year_spec.rb:5:in 
     `block (2 levels) in <top (required)>'
```

###ARGUMENTERROR:

Our tests are still failing, but for a new reason. Previously we lacked the method definition. Now we have the method defined; however, our tests are complaining that the line of code, `age_of_person = current_age_for_birth_year(1984)`, invoked the method `current_age_for_birth_year` incorrectly because it called that method with an argument but the method we defined does not accept an argument.

This results in an `ArgumentError`.

Let's fix that.

Update the code in `current_age_for_birth_year.rb` to entirely read:

```
def current_age_for_birth_year(birth_year)
end
```

There we define the method `current_age_for_birth_year` to accept an argument and name that argument `birth_year`. The method does nothing else.

Run learn again and your failures should resemble:

```
  1) current_age_for_birth_year method returns the age of a person based on the year of birth
     Failure/Error: expect(age_of_person).to eq(31)

       expected: 31
            got: nil

       (compared using ==)
     # ./spec/current_age_for_birth_year_spec.rb:6:in `block (2 levels) in <top (required)>'
```

This failure isn't a syntax error related to undefined methods or arguments. Instead, this error is telling us that we expected the return value of the method `current_age_for_birth_year(1984)`, stored in the variable `age_of_person` to equal `31`, but in actuality, the method returned the value `nil`.

That's perfect. Our test is showing a **mismatched expectation**. We need to add actual logic to that method to solve the problem.

How do we calculate the difference between the year currently and the year provided to the method as an argument birth_year? You might simply subtract the current year from the birth year.

```
def current_age_for_birth_year(birth_year)
  2015 - birth_year
end
```

Run learn again and you should see the test suite passing. Great job!

At this point you should stage your solution with git add . and commit it with git commit -am "Done" and push it with git push. Then open a pull request to pass this lab.


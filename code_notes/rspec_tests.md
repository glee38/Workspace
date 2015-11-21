#RSPEC

##WHAT'S A TEST?

Tests verify that the code you write behaves and produces the desired result. Many of the labs you will complete on Learn use tests. It can feel like an abstract concept at first, but it's worth starting to understand. Doing so will help you be more productive on Learn. Beyond that, understanding testing is important because test-driven development, or TDD, is considered the most reliable methodology for delivering quality code.

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

The next three lines are our actual test code and the most important part of the spec/current_age_for_birth_year_spec.rb file. It is within this block of code––between it and end––that we test the functionality of our method.

In order to actually test our code, we need to use the method that this test relies on, that this test is designed to exercise. So the first real line of code in our test is:

age_of_person = current_age_for_birth_year(1984)

What we're doing here is calling a method, current_age_for_birth_year(1984), the very method we're supposed to define and implement, passing it a known argument, 1984, and assigning the return value of the method to a variable called age_of_person.

What do you think the value of age_of_person should be if the method current_age_for_birth_year is called with 1984 as the argument?

The next line of code poses that exact question with an expected outcome. Using lots of RSpec methods and syntax, we say, quite colloquially:

expect(age_of_person).to eq(31)

What this line of code means is that we expect the value of the variable age_of_person to equal 31.

That is to say, given that age_of_person is the return value of the method current_age_for_birth_year(1984), we can expect that the variable equals 31, the age of the person born in 1984. That's a test.

Our test loads our code, uses our code in the manner desired, and compares the result of our code with a known outcome so that we know our code behaves as we expected.

We could imagine another specification of the current_age_for_birth_year method as another it block within the opening describe block:

it "should return the current year for a person born in year 0" do
  twenty_fifteen = current_age_for_birth_year(0)

  expect(twenty_fifteen).to eq(2015)
end
A test is always going to be about setting up a state with a known result and comparing that known result or expectation to the behavior of your program, thus ensuring that your program behaves as you expected.

There are many kinds of tests, and test-driven development and RSpec are very complex topics. Just focus on the semantics and meaning of the *_spec.rb files for now. It's a tremendously valuable skill to be introduced to this early.
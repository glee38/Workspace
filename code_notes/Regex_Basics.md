#Regex Basics

##Introduction

Regular expressions are an extremely powerful way to search through strings and blocks of text for specific patterns. They can be used for data validation, searching, mass file renaming, and finding records in a database. Use them carefully - they are like a surgeon's scalpel: Able to do a lot of harm or good, depending on how well it is wielded.

A great place to head for Regex testing and practice is [Rubular](http://rubular.com/) - it allows you to build and test regular expressions against text that you define.

##Writing Regular Expressions

In Ruby, regular expressions are always written between forward slashes:
`/your regex/`. In Rubular, you can see that these slashes have already been written for you.

**SIMPLE TEXT MATCHING**

Let's start with the simplest text matching. Add the following regex in rubular:

`/twenty/`
![twenty regex](https://curriculum-content.s3.amazonaws.com/web-development/Regex/twenty.png)

Notice that the pattern matches the two instances of "twenty" in the passage. Writing a series of letters or numbers in your regular expression will result in a search for exact matches of this pattern anywhere in the string.

**METACHARACTERS**

The real beauty of Regular Expressions is revealed in its use of metacharacters. Metacharacters allow you to use a pre-defined shorthand to match specific characters. For example, `\d` will match any digit in your text, and `\w` will match any word character (letters, numbers, and underscores). The 'Regex Quick Reference' at the bottom of Rubular shows metacharacters and patterns that you can use. Play around with these a little. Use `\W` (notice uppercasing) to match the non-word characters in your text.

**ONLY SPECIFIC CHARACTERS**

If I want to match all instances of vowels in a string, the regex `/aeiou/` won't work (feel free to try it), as it will only match the entire string "aeiou" - which clearly isn't in our text. Instead let's use square brackets: `/[aeiou]/` - this is looking for only **one single** character in our text which matches any of the characters inside the square brackets. If you add this regext to our rubular, you'll see every vowel highlighted in your match result.

**RANGES**

Based on what we've just learned, we can write a regular expression looking for single characters in the first 10 letters of the alphabet like so:`/[abcdefghij]/`

We can actually shorten this in ruby using a Regex range:`/[a-j]/`

`[0123456789]` becomes `[0-9]`

**EXAMPLE: DOUBLE VOWELS**

There are many other metacharacters and ways of building patterns in regex, many of which you can refer in the Rubular quick reference guide. However, the best way to actually learn to use Regular Expressions is to practice building your own patterns. Let's look for instances in our text of two consecutive vowels (for example, 'ae', 'ie', 'oo', etc). The longest way to do this is to hand code the different combinations of two vowels:`/aa|oo|ee|ii|uu|ae|ea|ou|ie|ei|eo|oe/`. It's pretty tedious to hand code each of these combinations (I didn't finish). An improvement is to use two sets of square brackets with vowels, each one representing a single character: `/[aeiou][aeiou]/`. Our most efficient, however, is to use repetitions: `/[aeiou]{2}/` The curly braces surrounding mean that the pattern or character directly preceding it must repeat that number of times. As such, we're looking for a repeat of a vowel two times. As you can see, there are many ways to write a Regular Expression that does the same thing.

###Resources

+ [Rubular](http://rubular.com/)
+ [Regex One](http://regexone.com/)
+ [Regex Golf](https://regex.alf.nu/)

##Ruby Methods in Regex

**SCAN**

The `scan` method returns an array of **all** items in your string that match a given Regular Expression. For example:

```
"The rain in Spain lies mainly in the plain".scan(/\w+ain/)
=> ["rain", "Spain", "main", "plain"]
```

**MATCH**

The `match` method returns the first item in your string that match a given Regular Expression (as a `MatchData` object. For example:

```
"The rain in Spain lies mainly in the plain".match(/\w+ain/)
=> #<MatchData "rain"> 
 
"The rain in Spain lies mainly in the plain".match(/France/)
=> nil
```

More often than not, we use the result of the match method as a boolean, indicating the existence of the pattern in the given string.

**GREP**

`Grep` is an enumerable method for pattern searching in arrays and hashes. Similar to scan, grep will return an array of matching items from an array.

```
names = ["Jeri Faria", "Althea Voth", "Audry Donoho",
 "Scotty Chaves", "Lance Barrio", "Zachary Newhall",
  "Stefany Janey", "Tressie Kinsel", "Raven Grimsley",
   "Marketta Gaylor", "Leota Crowe", "Mazie Norman",
    "Damien Loffredo"]
 
#Get items from array where first name has five
 letters:
names.grep(/^\w{5}\s/)
 
=> ["Audry Donoho", "Lance Barrio", "Raven Grimsley",
 "Leota Crowe", "Mazie Norman"]
```

 
**CAPTURE GROUPS**

Using parentheses in our regex allows us to create 'groups' that we can refer to in our scan/match/grep methods as indexes in an array. In the example below we create three capture groups for the three sets of digits in a phone number. Now, when we scan a list of numbers, each phone number is broken down into subgroups based on the capture groups we built in our regular expressions:

```
numbers = "202-555-0192 202-555-0147 202-555-0131
 202-555-0116 202-555-0192 202-555-0197"
 
number_breakdown = numbers.scan(/(\d+)-(\d+)-(\d+)/)
=> [["202", "555", "0192"], ["202", "555", "0147"], 
["202", "555", "0131"], ["202", "555", "0116"], 
["202", "555", "0192"], ["202", "555", "0197"]] 
 
number_breakdown[0]
=> ["202", "555", "0192"]
 
number_breakdown[0][1]
=> "555"
```
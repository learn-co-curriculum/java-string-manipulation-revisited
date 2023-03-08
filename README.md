# String Manipulation Revisited

## Learning Goals

- Learn about the `Matcher` class in the `java.util.regex` package.
- Apply regular expressions to string manipulation in Java.

## Introduction

It's time to get back to Java! But we aren't ready to leave regular expressions
just yet either. In this lesson, we will be looking at a the `Matcher` class
from the `java.util.regex` package and how regex may be helpful when it comes to
manipulating `String` objects.

## The Matcher Class

The Java `Matcher` class is another class part of the `java.util.regex` package.
A `Matcher` object associates patterns with `String` objects to find and match
regular expression patterns. We can get a `Matcher` object by calling the
`Pattern` class' `matcher()` method. The `matcher()` method will take in a
character sequence of input to match against the pattern:

```java
import java.util.regex.Pattern;
import java.util.regex.Matcher;

public class RegexExample {
    public static void main(String[] args) {
        Pattern regexPattern = Pattern.compile("[Hh]m?");
        Matcher matcher = regexPattern.matcher("Hm");
    }
}
```

Once we have created a `Matcher` object, we can use it to see if a regex pattern
exists in the input character sequence like we did before!

```java
import java.util.regex.Pattern;
import java.util.regex.Matcher;

public class RegexExample {
    public static void main(String[] args) {
        Pattern regexPattern = Pattern.compile("[Hh]m?");
        Matcher matcher = regexPattern.matcher("Hm");
        System.out.println(matcher.matches());
    }
}
```

The output of the above code is:

```text
true
```

The `matches()` method we see above will behave in the exact same way as the
`Pattern.matches()` static method! If that is the case, we might be wondering
why even introduce the `Matcher` class?

Well the `Matcher` class can do so much more! Consider some of the more popular
methods of the `Matcher` class below:

| Method                             | Return Type | Description                                                                                                           |
|------------------------------------|-------------|-----------------------------------------------------------------------------------------------------------------------|
| `end()`                            | int         | Returns the offset after the last character matched                                                                   |
| `find()`                           | boolean     | Attempts to find the next subsequence of the input sequence that matches the pattern                                  |
| `find(int startingIndex)`          | boolean     | Attempts to find the next subsequence of the input sequence that matches the pattern, starting at the specified index |
| `group()`                          | String      | Returns the input subsequence matched by the previous match                                                           |
| `replaceAll(String replacement)`   | String      | Replaces every subsequence of the input sequence that matches the pattern with the given replacement string           |
| `replaceFirst(String replacement)` | String      | Replaces the first subsequence of the input sequence that matches the pattern with the given replacement string       |
| `reset()`                          | Matcher     | Resets this matcher                                                                                                   |
| `start()`                          | int         | Returns the start index of the previous match                                                                         |

Let's take a look at how to use some of these methods!

### Finding the Pattern

The `Matcher` class' `find()` methods are extremely useful in finding the exact
location of a matching pattern in a sequence of characters. Before we show some
code, let's go back to our friendly tool, Regex 101.

Let's enter in this test string into Regex 101:

```text
But I would walk 500 miles and I would walk 500 more
```

Assume we are interested in finding the three-digit numbers in this test string.
Go ahead and enter in the "Regular Expression" text box the pattern `"\d\d\d"`.

![regex-500-miles](https://curriculum-content.s3.amazonaws.com/java-mod-3/string-manipulation-revisited/regex-500.png)

Notice there are two matches. The first match has a starting index of 17 and an
ending index of 20. The second match has a starting index of 44 and an ending
index of 47. (You can see the starting and ending indexes in Regex 101 if you
look at the "Match Information" box.)

Now let's see if Java can find the same matches!

```java
import java.util.regex.Pattern;
import java.util.regex.Matcher;

public class RegexExample {
    public static void main(String[] args) {
        Pattern regexPattern = Pattern.compile("\\d\\d\\d");
        Matcher matcher = regexPattern.matcher("But I would walk 500 miles and I would walk 500 more");
        while (matcher.find()) {
            System.out.printf("I found the text %s starting at index %d and ending at index %d%n",
                            matcher.group(), matcher.start(), matcher.end());
        }
    }
}
```

If we were to run the code above, the result would look like this:

```text
I found the text 500 starting at index 17 and ending at index 20
I found the text 500 starting at index 44 and ending at index 47
```

It matches exactly with what we found using the Regex 101 tool!

Time to break down what is happening now:

- Create and compile a regex pattern called `regexPattern`.
- Create a `Matcher` object by calling the `Patrern` class' `matcher()` method,
  and provide it the test string we put into Regex 101.
- Create a `while` loop with the conditional being `matcher.find()`.
  - The `find()` method attempts to find the next subsequence in the input
    sequence that matches the pattern. This means, if it finds the pattern in
    the input test string, it will return true. If it cannot find the pattern,
    then it will return false.
    - Note, if we only call the `find()` method once, it will only find the first
      time it matches the pattern. If we want to find all the patterns in the
      `String` object, we can call it again, and it will pick up where it left off
      in the `String` to find another matching pattern. This is why we have it as
      the conditional in the `while` loop.
- If it finds a matching pattern, we will print out the subsequence that
  matched, the starting index, and the ending index.
  - We can get the subsequence that matched the pattern by calling the `group()`
    method from the `Matcher` class. This will return a `String` instance of the
    substring matched.
    - So in both the first and second iterations, this will return `500`.
    - We can get the starting index of the match by calling the `start()` method
      from the `Matcher` class. This will return an `int` with that starting
      index of the matched substring.
      - So in the first iteration, this method will return `17` since that is the
        first time `500` appears.
      - In the second iteration, this method will return `44` since that is the
        second time `500` appears.
  - We can get the ending index, or close to it, by calling the `end()` method
    from the `Matcher` cass. This will return an `int` with an offset after the
    last character matched. In other words, the ending index is **not**
    inclusive like the starting index.
      - So in the first iteration, this wil return `20` and in the second
        iteration it will return `47`.

Now what if we care only about the last part of our test string?

```text
and I would walk 500 more
```

We _could_ modify the test input to be just that, or we could let Java do the
work for us. Let's say we know the part of the string we are interested in has
a starting index of 27, and we only want to find that last three-digit number.
We could rewrite our code to look like this:

```java
import java.util.regex.Pattern;
import java.util.regex.Matcher;

public class RegexExample {
    public static void main(String[] args) {
        Pattern regexPattern = Pattern.compile("\\d\\d\\d");
        Matcher matcher = regexPattern.matcher("But I would walk 500 miles and I would walk 500 more");
        if (matcher.find(27)) {
            System.out.printf("I found the text %s starting at index %d and ending at index %d%n",
                            matcher.group(), matcher.start(), matcher.end());
        }

    }
}
```

When provide the `find()` method with an `int` parameter, we are actually
specifying the starting index of the string. By default, with no parameters, the
`find()` method will start at 0 and search the entire string for the pattern.
By saying `find(27)` we are saying "ignore the first 26 characters of the input
when trying to find a matching pattern."

The output of the above code is:

```text
I found the text 500 starting at index 44 and ending at index 47
```

### Replacing the Pattern

Now that we know how to find matching patterns using the `Matcher` class, let's
see how we could possibly replace a pattern!

500 miles is quite ambitious. Maybe we are only willing to walk 200 miles at a
time. Let's replace the pattern 500 with 200!

```java
import java.util.regex.Pattern;
import java.util.regex.Matcher;

public class RegexExample {
    public static void main(String[] args) {
        Pattern regexPattern = Pattern.compile("\\d\\d\\d");
        Matcher matcher = regexPattern.matcher("But I would walk 500 miles and I would walk 500 more");
        String replaced500 = matcher.replaceAll("200");
        System.out.println(replaced500);
    }
}
```

The output of the executed code above would be:

```text
But I would walk 200 miles and I would walk 200 more
```

The `replaceAll()` method takes in a `String` parameter of what we would want to
replace the pattern we found with. In this case, we want to replace `500` with
`200`. It will search the entire input `String` instance we provided when we
created the `Matcher` object and replace all occurrences that match the pattern
`"\\d\\d\\d"` with `200`.

Maybe we will walk the 500 more... but initially, we are only going to walk 200
miles. Instead of using the `replaceAll()` method, we could use the
`replaceFirst()` method!

```java
String replaced500 = matcher.replaceFirst("200");
```

Now if we were to run the code above with that line instead, we would get this
output:

```text
But I would walk 200 miles and I would walk 500 more
```

The `replaceFirst()` method takes in a `String` parameter of what we would want
to replace the pattern we found with, but will only replace the first occurrence
of the pattern.

In the code above, we need to create a `Pattern` and a `Matcher` instance before
we can even replace all or just the first occurrence of the pattern. What if
we said we could do this in less lines of code?

```java
public class RegexExample {
    public static void main(String[] args) {
        String proclaimers = "But I would walk 500 miles and I would walk 500 more";
        String replaced500 = proclaimers.replaceAll("\\d\\d\\d", "200");
        System.out.println(replaced500);
    }
}
```

Ta-da! The `String` class actually has its own `replaceAll()` and
`replaceFirst()` method! This method will yield the same result as the
expression:

```java
Pattern.compile(regex).matcher(inputString).replaceAll(replacement);
```

### Resetting the Matcher

As we saw with the `find()` method, the `Matcher` object can retain the state of
the input string to see what pattern we last found. For example, if we were to
run the following code:

```java
import java.util.regex.Pattern;
import java.util.regex.Matcher;

public class RegexExample {
    public static void main(String[] args) {
        Pattern regexPattern = Pattern.compile("\\d\\d\\d");
        Matcher matcher = regexPattern.matcher("But I would walk 500 miles and I would walk 500 more");

        if (matcher.find()) {
            System.out.printf("We found the text %s at index %d%n", matcher.group(), matcher.start());
        }

        if (matcher.find()) {
            System.out.printf("We found the text %s at index %d%n", matcher.group(), matcher.start());
        }
    }
}
```

The output would be similar to what we saw before:

```text
We found the text 500 at index 17
We found the text 500 at index 44
```

We could reset the `Matcher` object though after the first call to the `find()`
method though, and it would completely reset where it was searching.

```java
import java.util.regex.Pattern;
import java.util.regex.Matcher;

public class RegexExample {
    public static void main(String[] args) {
        Pattern regexPattern = Pattern.compile("\\d\\d\\d");
        Matcher matcher = regexPattern.matcher("But I would walk 500 miles and I would walk 500 more");

        if (matcher.find()) {
            System.out.printf("We found the text %s at index %d%n", matcher.group(), matcher.start());
        }

        // Reset the Matcher instance
        matcher.reset();

        if (matcher.find()) {
            System.out.printf("We found the text %s at index %d%n", matcher.group(), matcher.start());
        }
    }
}
```

Just adding the `reset()` method would change the output to be this instead:

```text
We found the text 500 at index 17
We found the text 500 at index 17
```

As we can see, resetting the `Matcher` instance will discard the current state
of where it was searching and set it back to zero, or the start of the input
test string.

## String Split

We now know how to check if a regular expression pattern matches a specific
character sequence in Java, how to search for matches in a `String`, and even
how to replace the patterns in Java using the `replaceAll()` and
`replaceFirst()` method calls.

The last method we want to discuss is actually a method in the `String` class,
and it's called the `split()` method. The `split()` method will take in a
regular expression as a `String` and splits the string around matches of the
given regular expression, returning an array of `String` objects.

That seems a little confusing, so let's look at an example.

In Regex 101, enter the following as the "Test String":

```text
Menu Item,Cost
```

Now in the "Regular Expression" field, enter `","`.

Notice there is one match, and it is highlighting the only comma in the test
string.

Now consider the following code:

```java
public class RegexExample {
    public static void main(String[] args) {
        String menu = "Menu Item,Cost";
        String[] splitResult = menu.split(",");

        for (String result : splitResult) {
            System.out.println(result);
        }
    }
}
```

The result of the above code is:

```text
Menu Item
Cost
```

Notice how the `split()` method broke the `String menu` value into an
array by using the pattern as a delimiter. A delimiter is a character that
marks the beginning or end of a unit of data. Wherever it found the `,` in
`menu`, it would split the `String` value. Since there was only one `,`, the
result is two `String` objects with the values "Menu Item" and "Cost".

## Resources

- [Regex 101](https://regex101.com/)
- [Java 17 Matcher Class](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/regex/Matcher.html)
- [Java 17 String Class](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/String.html)

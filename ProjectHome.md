A library for writing readable, composable regular expression matchers that integrates cleanly with [Hamcrest](http://hamcrest.googlecode.com).

## Features ##

  * Easier to read (although more long-winded) than regular expressions
  * Named capture groups: captured text is identified by name, not by the index of the group.
  * Composable: patterns can be easily combined into more complex patterns without worrying about breaking regex syntax or changing group identifiers.
  * Refactoring friendly: patterns can be refactored with your favourite IDE.

## Parsing ##

Here's a simple example of using Hamcrest Text Patterns to parse date ranges, such as "12 Apr 1994 - 16 Mar 2005".

We can define components of the final text pattern using the factory methods defined in the Patterns class:

```
PatternComponent month = either(
    "jan", "feb", "mar", "apr", "may", "jun", 
    "jul", "aug", "sep", "oct", "nov", "dec");
        
PatternComponent digit = anyCharacterInCategory("Digit");
    
PatternComponent date = separatedBy(whitespace(),
    capture("day", exactly(2, digit)), 
    capture("month", caseInsensitive(month)), 
    capture("year", exactly(4, digit)));
```

We can combine these into a PatternMatcher for matching date ranges:

```
PatternMatcher dateRange = new PatternMatcher(separatedBy(whitespace(),
    capture("from", date), "-", capture("to", date)));
```

We can now parse date ranges from input text and extract the dates using the names of the capture groups we defined in the dateRange pattern: "from" and "to".

```
String input = "31 Dec 2003 - 16 aug 2008";
        
Parse parse = dateRange.parse(input);
        
assertThat(parse.get("from"), equalTo("31 Dec 2003"));
assertThat(parse.get("to"), equalTo("16 aug 2008"));
```

And, because we defined capture groups in our date pattern, we can extract the parts of the "from" and "to" dates:

```
assertThat(parse.get("from.day"), equalTo("31"));
assertThat(parse.get("from.month"), equalTo("Dec"));
assertThat(parse.get("from.year"), equalTo("2003"));
        
assertThat(parse.get("to.day"), equalTo("16"));
assertThat(parse.get("to.month"), equalTo("aug"));
assertThat(parse.get("to.year"), equalTo("2008"));
```

## Matching ##

A PatternMatcher is a Hamcrest matcher, and so text patterns can be used with libraries that build upon Hamcrest, such as [JUnit](http://www.junit.org) or [jMock](http://www.jmock.org).  For example:

```
String input = "31 Dec 2003 - 16 aug 2008";
assertThat(input, dateRange);
        
String badInput = "31 12 2003 - 16 08 2008";
assertThat(badInput, not(dateRange));
```

## Next Steps ##

The [How-To Documentation](http://code.google.com/p/hamcrest-text-patterns/w/list?can=1&q=label%3AHow-To), the [examples](http://code.google.com/p/hamcrest-text-patterns/source/browse/trunk/tests/org/hamcrest/text/pattern/examples) and the [tests](http://code.google.com/p/hamcrest-text-patterns/source/browse/trunk/tests/org/hamcrest/text/pattern/PatternMatcherTests.java) have more examples of how to use the library.
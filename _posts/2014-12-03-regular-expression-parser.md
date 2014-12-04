---
layout: post
title: Simple Regular Expression Parser
---

This is a simple [regular expression][regex] parser in Python.  It takes two
parameters: a simple regular expression (which I'll define later) and an input
string to match against the regex.

In this simple regex, this is the alphabet being recognized:

* **star (`*`)**: denotes any number of occurences (including zero) of the
  previous literal;
* **period (`.`)**: denotes any literal character;
* **literals**: any character not mentioned above;
* It is also assumed that the regex is **anchored**.  That is, it is
  implicitly surrounded by beginning and end of line characters.

For simplicity, no input validation is being done, so it is assumed that all
regexes are valid (i.e. stars must be preceeded by a literal).  The following
code is actually pretty short, so I'll explain the idea behind the code as we
go through.


{% highlight python %}
def is_match(regex, string):
    # Base case: empty regex should match only empty strings
    if regex == '':
        if string == '':
            return True
        return False

    token = regex[0]
    possible_chars = ['.']
    if string:
      possible_chars += string[0]

    # This is the tricky part: peak ahead to handle the star
    # separately. Whenever a star is found, then we need to branch
    # out and try two different cases:
    if (len(regex) > 1 and regex[1] == '*'):
        # First, ignore the token as if there was no such occurence
        if is_match(regex[2:], string):
            return True

        # Otherwise, try to consume one character and keep going to
        # possibly find more occurences.
        if string and token in possible_chars:
            return is_match(regex, string[1:])

    # No special cases, so consume one token and one char
    if (string and token in possible_chars):
        return is_match(regex[1:], string[1:])

    # Nothing matched
    return False
{% endhighlight %}


[regex]: https://en.wikipedia.org/wiki/Regular_expression
  "More on regexes on Wikipedia"

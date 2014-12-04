---
layout: post
title: Simple Regular Expression Parser
---

This is a simple [regular expression][regex] parser in Python.  It takes two
parameters: a simple regular expression (which I'll define later) and an input
string to match against the regex.

In this simple regex, this is the alphabet being recognized:

* **star (`*`)**: denotes any number of characters, including zero characters;
* **period (`.`)**: denotes any literal character;
* **literals**: any character not mentioned above;
* It is also assumed that the regex is **anchored**.  That is, it is
  implicitly surrounded by beginning and end of line characters.

The following code is actually pretty short, so I'll explain the idea
behind the code as we go through.


{% highlight python %}
def is_match(expr, str):
    # Base case: empty regex should match only empty strings
    if expr == '':
        if str == '':
            return True
        return False

    # str[0:1] is just a hack to handle the next character, so as to
    # prevent str[0] from blowing up when the str is empty.
    possible_chars = (str[0:1], '.')
    token = expr[0]

    # This is the tricky part: peak ahead to handle the star
    # separately. Whenever a star is found, then we need to branch
    # out and try two different cases:
    if (len(expr) > 1 and expr[1] == '*'):
        # First, ignore the token as if there was no such occurence
        if is_match(expr[2:], str):
            return True

        # Otherwise, try to consume one character and keep going to
        # possibly find more occurences.
        if str and token in possible_chars:
            return is_match(expr, str[1:])

    # No special cases, so consume one token and one char
    if (str and token in possible_chars):
        return is_match(expr[1:], str[1:])

    # Nothing matched
    return False
{% endhighlight %}


[regex]: https://en.wikipedia.org/wiki/Regular_expression
  "More on regexes on Wikipedia"

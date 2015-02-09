---
layout: post
title: Help Your Future Self in Your Commit Messages
---

Every now and then I join a new project and soon enough learn that devs
are using some [VCS][] but being incredibly wasteful at doing so.
They'll make some changes, hopefully run tests, build, check, and, if
things are good enough, commit.  So far, so good, if it weren't for the
fact that they're also doing a great disservice for their future selves
by being careless about what they add to that commit message.

There are lots of good articles on [writing][] [good][] [commit
messages][] and so I won't just repeat them.  In the good spirit of
[free software][], I'll stand on the should of others and try build on
top of that by providing real examples of commit messages I've seen on
previous projects and the reasons why they hurt developers instead of
helping us.


{% highlight python %}
"build fix"
# what was the problem?
# was it completely fixed?
# if the same problem happens again, how do I know this commit has something to do with it?

"Update pom"
# was it broken? or was it a clean up?
# one seemingly unrelated section was changed, was it by design or by accident?

"Fix for DE38274"
# the symptom was fixed. what was the cause?
# what if the bug tracker is offline or if we migrate bug trackers?

"Non-zipped version of my last changelist"
# is this - or the zipped - file duplication?
# how do I know files weren't added by accident?

"removing properties files"
# some property files are still there. were they forgotten or was it by design?

"Updated Smoke tags as per"
# was this an accidental commit?

"Workflow changes & Property files updates"
# what were the workflow changes?
# if a bug traces back to this commit, can I easily know if it's due to the workflow or the property files?

"Adding log statements before system calls and refactoring reading versioning csv"
# can I easily apply the CSV refactoring to the latest stable release?

"(Checkin 1 of 2) Verify provided inputs are actually valid/in the dropdown"
"(Checkin 2 of 2) Verify provided inputs are actually valid/in the dropdown"
# why was this change broken into two commits?
# do they work on their own or do you need both commits to work properly?

"As Lisa Hammond, I want to be able to export an attached document from a Model Validation so that I can read the contents of the document offline (backend code)"
# it's pretty annoying to have to scroll horizontally to read this
{% endhighlight %}


There are lots of things that might seem obvious to you at the time you're
changing a particular piece of code, but they will mostly likely not be that
obvious - or even make sense at all - a few days, weeks, or months in the
future.  Make sure you're kind to your fellow developers and to your future
self and give them all the context you currently have on this change.  Code
quality is measured in [WTFs/m][wtfsm] and good code not only will get fewer
WTFs from you but will also explain to you the reason behind the question in
its commit messages!

_Those who cannot remember the past are condemned
to repeat it._ (George Santayana)


[VCS]: https://en.wikipedia.org/wiki/Revision_control
  "Revision Control - Wikipedia"
[writing]: http://robots.thoughtbot.com/5-useful-tips-for-a-better-commit-message
  "5 Useful Tips For A Better Commit Message - ThoughtBot"
[good]: https://github.com/erlang/otp/wiki/writing-good-commit-messages
  "Writing good commit messages - Erlang/OTP"
[commit messages]: http://who-t.blogspot.com/2009/12/on-commit-messages.html
  "On Commit Messages - Who-T"
[free software]: http://www.amazon.com/gp/product/0983159203/ref=as_li_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=0983159203&linkCode=as2&tag=andersonvom-20&linkId=OMF7PZKZ7S6RORDC
  "Free Software Free Society - Richard Stallman"
[wtfsm]: http://www.osnews.com/story/19266
  "WTFs/m - OSnews"

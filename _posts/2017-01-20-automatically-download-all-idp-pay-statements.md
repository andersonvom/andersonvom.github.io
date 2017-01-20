---
layout: post
title: Automatically Download All Your ADP Pay Statements
---

Recently I had to go find some old pay statements to look at tax
information and [ADP][] does not provide a way to search for information
on old pay statements. You can't even download statements older than a
certain date (my understanding is that you can only access see/download
information for the last 100 pay cycles).

Downloading each statement manually to search for what I wanted proved
to be a boring and time consuming task: and I thought I could invest
this time better by writing something that will download them
automatically for me, and keep them updated as more pay statements came
along.

Enters [adp-downloader][].

Just install and run the gem:

{% highlight bash %}
gem install adp-downloader  # you might have to use sudo
mkdir paystatements
cd paystatements
adp-downloader
{% endhighlight %}

This will ask for your username and password and download all available
statements from [MyADP][] automatically. It also supports unattended
downloading by creating an entry in your `$HOME/.netrc` file:

{% highlight .netrc %}
machine adp-downloader login ___username___ password ___password___
{% endhighlight %}

Running `adp-downloader` this way will use these credentials to
authenticate and you can then add it to a cronjob to keep your
statements up to date.


### Contributing

You can find information on the gem on [adp-downloader rubygems
page][gem] and the source code on [github][adp-downloader].


[adp-downloader]: https://github.com/andersonvom/adp-downloader
  "Automatically download ADP pay statements and tax statements"
[ADP]: https://www.adp.com/
  "ADP website"
[MyADP]: https://my.adp.com/static/redbox
  "MyADP Redbox portal"
[gem]: https://rubygems.org/gems/adp-downloader
  "adp-downloader rubygem page"

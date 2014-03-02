---
layout: post
title: Contributing to an Open Source Project&#58; OpenStack
---

I've been working on [OpenStack][] for a little over three months now
and it has been a great experience: not only I'm being paid to write
code that is used by millions of people, this code is also freely
available for people to read and improve as they see fit.  Having used
opensource software for a long time now, working in an opensource
project had been in my checklist of things to do as a developer and it's
been a great pleasure to be able to give it back to the community as my
full-time job and fulfill my desire.

### The Project

OpenStack is an infrastructure as a service cloud computing platform.
It aims to provide all resources needed to run a cloud service:
authentication system, compute instances, storage devices, network
management, and, more recently, an orchestration system as well.  It
started in 2010 in a partnership between [Rackspace][] and [NASA][],
with the initial code being from both NASA's Nebula platform and
Rackspace's Cloud Files.

The project I'm currently working on is [Heat][].  It is the service
responsible for the orchestration features of OpenStack and it aims to
make it easy to create and manage the entire cloud application using
templates.  That means that you can spin up webservers, put them behind
a load balancer, hook everything up to your database cluster, and deploy
your application, without having to worry about the nitty-gritty of
doing all that manually.  When you want to scale your deployment and add
more webservers, just edit the template to reflect the new count and
update your application!


### Members

There are over 200 companies involved in OpenStack (Red Hat, IBM, HP,
and Rackspace to name a few), 17 of which have contributed to Heat, plus
independent contributors.  A total of 69 different developers have
contributed code to Heat (~30 of which are active and constantly
submitting new code), and they are scattered around several different
countries and timezones.


### Day-to-Day

Most of our interaction takes place on IRC and you can always find folks
hanging out on Freenode's [#heat][heat-irc]. Come by and drop us a line!
Everybody is really friendly and always willing to help out.  IRC is
used to chat pretty much about everything related to the project:
general questions about the code, the direction the project is taking,
current code reviews, bug, or blueprints (more on that later), or
something else you can come up with.

Outside of IRC, a lot of discussion also happens on our mailing list.
It is usually used for discussions where the input of a larger crowd is
needed - you won't be able to find all core members on the IRC channel
at once, or the subject is too complex to be solved in quick (and
sometimes even really long) chats.


### Meetings

It's not because it's an opensource project and most interaction happens
on the internet that we do not have meetings.  You can't scape them!
We hold weekly meetings that - now in an alternating schedule  - happen
at two different times to accomodate members that happen to live in very
different timezones.  It's not fair that all meetings happen at 2am your
time.

The weekly meetings are one hour long and promptly start at 2000 UTC on
Wednesdays every other week, and at 0000 UTC on Thursdays the remaining
weeks.  They are facilitated by the project team lead and he's
responsible for guiding everybody through the weekly agenda and for
making sure everybody stays on topic.  There is also a special guest
that is present in every meeting: an eavesdrop IRC bot called
[Meetbot][] and it plays a crucial role in making it easy to hold these
meetings on IRC.


### Code

The code is all written in Python.  We use [Launchpad][] to track bugs
and blueprints (specification and implementation of new features).  This
is primarily so that we can track progress of milestones and issue
triage.

Once you get a bug or a blueprint and actually do some coding, then we
use [Gerrit][] to do code review.  The review process is very
straight-forward:

  1. Submit a patch for review
  2. [Jenkins][] runs automated unit and functional tests and votes
  3. Members vote on your patch, possibly with reasons/questions
  4. If approved, Jenkins merges your patch with the repo

Both Jentkins and members vote on your patch.  Once Jenkins finishes
running all the tests, it'll give either a -1 if tests fail or a  +1 if
they pass.  After that, members will cast their votes ranging between
-2 and +2.  Once you get two core members to give you a +2, your code
gets merged.  The score meaning is as follows:

  * -2: this patch will never be accepted (core members only)
  * -1: I would prefer you didn't merge this
  *  0: Used mostly for questions/comments
  * +1: Looks good to me, but someone else must approve
  * +2: Approved (core members only)

The scrutiny around each patch is very healthy for the most part and you
get many developers examining your code for potential bugs, design
flaws, or other pertinent details.  That is a very good way of learning
and constantly improve everybody's code, since folks with more
experience or that are more familiar with the codebase can spot things
that would be harder for a newcomer to see at first.  Though, good for
the most part, the delay in certain reviews due to different timezones,
too much work on core members, or just plain disagreements can be
frustating at times.


### Overall

All in all, working in an opensource project has been a great
professional experience and it feels amazing to be able to spend my full
time giving back to the community a little bit of my contribution.  Have
you ever contributed to an open project?


[openstack]: http://www.openstack.org/
  "OpenStack Cloud Platform"
[rackspace]: http://www.rackspace.com/
  "Rackspace Hosting"
[nasa]: http://www.nasa.gov/
  "National Space Agency"
[heat]: https://wiki.openstack.org/wiki/Heat
  "OpenStack Orchestration - Heat"
[heat-irc]: irc://irc.freenode.net
  "OpenStack Cloud Platform"
[meetbot]: https://wiki.debian.org/MeetBot
  "IRC bot for running meetings"
[launchpad]: https://launchpad.net/
  "A suite of tools to help software projects"
[gerrit]: https://code.google.com/p/gerrit/
  "Gerrit code review and project management"
[jenkins]: http://jenkins-ci.org/
  "Open Source Continuous Integration server"

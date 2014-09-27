---
layout: post
title: Think Parallel with Celery
---

Lately I've been working a lot with [Celery][celery-python] (not to be confused
with [this celery][celery-vegetable]) in a somewhat legacy app that was created
to gather information on the company's resources being used by their customers
and provide visual feedback on how they're being used and how much money
they're generating.  Gathering such information is quite expensive, as you have
to make lots of HTTP calls to different APIs to get all the necessary
information the app needs, and this is a great use case for celery - a
distributed task queue.

Recently the app started performing poorly and some investigation revealed that
there was a surge in the amount of resources customers are using.  Since the
app makes several API calls for each resource, this led to the backlogging of
the celery tasks responsible for them.  As the app periodically polls for the
current status of the resources, and it took lots of calls for each resource,
there wasn't enough time to finish all the current calls before the service
started yet another poll, which generated even more calls... Before you know
it, your queue, which used to be empty most of the time, now has over 200k
tasks and increasing.

So I dive into the code to look for what's causing this.  After understanding
how all the pieces fit together, I notice a similar pattern happening time and
again.  This is the structure of some of the calls I found in the code:


{% highlight python %}
@app.task
def start():
    for obj_list in get_paginated_obj_list():
        if obj_list:
            store_results(obj_list)


@app.task
def store_results(obj_list):
    for obj_id in obj_list:
        obj = get_obj(obj_id)
        obj_details = get_obj_details(obj_id)
        store_obj_info(obj, obj_details)


@app.task
def store_obj_info(obj, obj_details):
    crunch_and_save(obj, obj_details)
{% endhighlight %}


If you're just starting with celery, this may look fine to you: iterate over
all the pages of objects until you have no more pages left, then, for every
object in a page, get all the necessary information, crunch some data, and save
it to the DB.  For the sake of completion, let me also show how the functions
used be these tasks might look like:


{% highlight python %}
def get_paginated_obj_list():
    for page in range(20):
        # http call that can take a few seconds
        time.sleep(0.5)
        obj_list = [(page * 10) + obj for obj in range(100)]
        yield obj_list


def get_obj(obj_id):
    # http call that can take a few seconds
    time.sleep(0.25)
    return obj_id


def get_obj_details(obj_id):
    # http call that can take a few seconds
    time.sleep(0.75)
    return obj_id


def crunch_and_save(obj, obj_details):
    # expensive operation
    time.sleep(2)
    LOGGER.info('saving object: %s' % obj)
    return (obj, obj_details)
{% endhighlight %}


Now, allow me to show how bad this really is.  First off, some benchmarking to
get a baseline of how we're doing.  This is the output when running the
`start()` task:


    2014-09-26 17:03:42,272 INFO Received task: collector.celery.tasks.stats_tasks.start
    [2014-09-26 17:03:45,783: INFO/Worker-5] collector.celery.tasks.stats_tasks.store_obj_info: saving object: 0
    [2014-09-26 17:03:48,790: INFO/Worker-5] collector.celery.tasks.stats_tasks.store_obj_info: saving object: 1
    [2014-09-26 17:03:51,795: INFO/Worker-5] collector.celery.tasks.stats_tasks.store_obj_info: saving object: 2
    [2014-09-26 17:03:54,809: INFO/Worker-5] collector.celery.tasks.stats_tasks.store_obj_info: saving object: 3
    [2014-09-26 17:03:57,814: INFO/Worker-5] collector.celery.tasks.stats_tasks.store_obj_info: saving object: 4
    ...


I didn't have enough patience to let the task run all the way to the end as
it's taking about 3.5s per task.  For 20 pages of 100 objects per page, this
would take almost 2 hours!  That's just crazy!  This is because all these tasks
are being executed one after the other, in a single task, and by the same
worker.  Let's try to do better.

Since storing the results of each page has nothing to do with getting the next
page, we can start by having that be done by a different task in the correct
way.  Notice that right now, we're just calling `store_results()` directly.
Even though the function is decorated with `@app.task`, this is no different
from calling a regular python function.  To make it a task that will be run by
some other worker, we need to call it like this:
`store_results.delay(obj_list)`.  Let's take a look at our logs to see what
changes:

    2014-09-26 17:31:07,548 INFO Received task: collector.celery.tasks.stats_tasks.start
    2014-09-26 17:31:08,069 INFO Received task: collector.celery.tasks.stats_tasks.store_results
    2014-09-26 17:31:08,573 INFO Received task: collector.celery.tasks.stats_tasks.store_results
    2014-09-26 17:31:09,075 INFO Received task: collector.celery.tasks.stats_tasks.store_results
    ...
    2014-09-26 17:31:16,109 INFO Received task: collector.celery.tasks.stats_tasks.store_results
    2014-09-26 17:31:16,610 INFO Received task: collector.celery.tasks.stats_tasks.store_results
    2014-09-26 17:31:17,120 INFO Received task: collector.celery.tasks.stats_tasks.store_results
    2014-09-26 17:31:17,620 INFO Task collector.celery.tasks.stats_tasks.start succeeded in 10.069038403s: None
    2014-09-26 17:31:17,621 INFO Received task: collector.celery.tasks.stats_tasks.store_results
    2014-09-26 17:36:08,791 INFO Task collector.celery.tasks.stats_tasks.store_results succeeded in 300.717724441s: None
    2014-09-26 17:36:09,298 INFO Task collector.celery.tasks.stats_tasks.store_results succeeded in 300.720794872s: None
    ...
    2014-09-26 17:46:10,526 INFO Task collector.celery.tasks.stats_tasks.store_results succeeded in 300.757876165s: None
    2014-09-26 17:46:11,445 INFO Task collector.celery.tasks.stats_tasks.store_results succeeded in 300.732260493s: None
    2014-09-26 17:46:15,943 INFO Task collector.celery.tasks.stats_tasks.store_results succeeded in 300.793925441s: None

Wow!  Did you notice we went from 2 hours all the way down to 15 minutes!?
That's quite an improvement!  Notice that the `start()` task actually finished
somewhat in the middle of all the 21 tasks.  That happened because we're now
telling celery that while we download the specific object details, it can go
ahead and continue to get more pages of objects, since these two are not
dependent on each other.  Another interesting thing to notice is that the total
time was still higher than running all tasks in parallel.  This has to do with
the way celery handles the scheduling of the tasks and there are way to
mitigate this behavior, but that's beyond the scope of this post.

Can we do a little better?  Take a look at the `store_results()` task:


{% highlight python %}
@app.task
def store_results(obj_list):
    for obj_id in obj_list:
        obj = get_obj(obj_id)
        obj_details = get_obj_details(obj_id)
        store_obj_info(obj, obj_details)
{% endhighlight %}


It's, once again, doing all that work in serie, but getting a specific object
and it's details has nothing to do with other objects, so why wait for the
first one to finish before going to the next one?  Celery provides quite a few
ways to [run your code in parallel][primitives] if you organize your tasks in
the right way.  We can use a [group][] to start all the object retrievals in
parallel by refactoring our code a little bit:


{% highlight python %}
@app.task
def store_results(obj_list):
    group(
        store_obj_info.s(obj_id)
        for obj_id in obj_list
    )()


@app.task
def store_obj_info(obj_id):
    obj = get_obj(obj_id)
    obj_details = get_obj_details(obj_id)
    crunch_and_save(obj, obj_details)
{% endhighlight %}


And let's take a look at our logs one more time:


    2014-09-26 21:04:32,354 INFO Received task: collector.celery.tasks.stats_tasks.start
    2014-09-26 21:04:32,877 INFO Received task: collector.celery.tasks.stats_tasks.store_results
    2014-09-26 21:04:32,901 INFO Received task: collector.celery.tasks.stats_tasks.store_obj_info
    2014-09-26 21:04:32,903 INFO Received task: collector.celery.tasks.stats_tasks.store_obj_info
    2014-09-26 21:04:32,905 INFO Received task: collector.celery.tasks.stats_tasks.store_obj_info
    ...
    2014-09-26 21:11:03,886 INFO Task collector.celery.tasks.stats_tasks.store_obj_info succeeded in 3.009974083s: None
    2014-09-26 21:11:03,954 INFO Task collector.celery.tasks.stats_tasks.store_obj_info succeeded in 3.013722462s: None
    2014-09-26 21:11:04,087 INFO Task collector.celery.tasks.stats_tasks.store_obj_info succeeded in 3.013033944s: None


Yay!  We managed to bring the total time down to **7 minutes** since we're
taking advantage of running a lot of different things in parallel (to be
precise, 2021 tasks were run in this last iteration).  This is **1700% faster
than the original code**.  :D

From this point on, your mileage may vary.  There are still a few tasks that
could, technically, be optimized: you could try parallelizing `get_obj()` and
`get_obj_info()` in a [chord][], if your pagination doesn't depend on the
current page to get to the next page, you could parallelize
`get_paginated_obj_list` and [chain][] the results into `store_results()`, and
possibly others.

In my local machine, none of the above was worthwhile anymore, but I suspect
that's due to the specific time constants I'm using in this example and my own
VM not having enough computing power.  Getting more eficiency out of that will
greatly depend on your specific scenario and variables.  Remember you want to
have tasks that are small enough to end quickly and take advantage of your
processors/cores, but not too small that there would be too much task overhead.


[celery-python]: http://www.celeryproject.org/
  "Celery - Distributed Task Queue"
[celery-vegetable]: https://twitter.com/andersonvom/status/514529769795227648
  "It's sometimes frustating to search for celery related tips and tricks, because this is what you find..."
[primitives]: http://docs.celeryproject.org/en/latest/userguide/canvas.html#the-primitives
  "Celery primitives: group, chord, chain, map, starmap, and chunks"
[group]: http://docs.celeryproject.org/en/latest/userguide/canvas.html#groups
  "A group can be used to execute several tasks in parallel."
[chord]: http://docs.celeryproject.org/en/latest/userguide/canvas.html#chords
  "A chord is a task that only executes after all of the tasks in a group have finished executing."
[chain]: http://docs.celeryproject.org/en/latest/userguide/canvas.html#chains
  "Tasks can be linked/chained together, which in practice means adding a callback task."


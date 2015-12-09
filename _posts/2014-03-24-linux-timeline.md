---
layout: post
title: Linux Timeline
---

This is a simple comparison between the full linux timeline versus a
timeline of only surviving distros.  Several forks happen every so
often, but a lot of them don't survive the crushing force of time.  The
original image was obtained from [this URL][original] and you can see
the code used to remove the extinct lines from the SVG file and the
results below.  Click on the image to turn off the lines for distros
that do not currently exist anymore.

May all 133 of them rest in peace. =(


{% gist 9743531 %}


<img title="A Linux timeline with existing and extinct distros"
     alt="A Linux timeline with existing and extinct distros"
     src="/images/posts/full_timeline.svg"
     class="img-responsive"
     id="timeline" />

<script type="text/javascript">
var switchTimelines = function() {
  var isFull = $('#timeline').attr('src').indexOf('full');
  var next = isFull >= 0 ? 'existing' : 'full';
  $('#timeline').attr('src', '/images/posts/' + next + '_timeline.svg');
};

var timeline = document.querySelector("#timeline");
timeline.addEventListener("click", switchTimelines);
timeline.addEventListener(this.eventTouchend, switchTimelines);
</script>

[original]: http://futurist.se/gldt/wp-content/uploads/12.10/gldt1210.svg

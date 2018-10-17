---
title: Keuka Lake Laser-Cut Tray
permalink: /docs/laketray/
---

This was a pretty awesome project that turned out pretty well!

As is tradition, here is the finished product first:

![finished]({{ site.baseurl }}/img/lake_tray/finished.jpg)

### This project can be broken into a few steps:
1. Acquire data
2. Parse data using [QGIS](http://www.qgis.org/)
3. Generate vector graphics from the data
4. Laser cut
5. Assemble

I learned a ton from this project, and I'll include where I would do things differently where relevant. However, the main take-aways are:
* Check your total layer math
* Have the right tools for the job (mostly, own a router or CNC machine)

### 1. Acquire Data

Where the data is hosted varies from state to state, since I'm from New York, my data was hosted at [gis.ny.gov](https://gis.ny.gov/).  Because Keuka is on the corner of two counties, it was very annoying to download all of the relevant data.  However, eventually I was able to download the necessary .dbf files.

### 2. Load Data into QGIS

Once I had these meshes, I loaded them into QGIS, drew contours at the desired heights, added the roads, and made vector graphics from those images.  The naive way to implement this is to have a sheet of plywood for every layer in your map.  However, there's a more efficient way to do it - if you capture each alternate contour on two separate pieces of plywood, it's possible to accomplish the task with just 2 pieces.  Here's an illustration:

![example]({{ site.baseurl }}/img/lake_tray/example.png)

Essentially, because every totally isolated piece is supported on all sides by the piece below it, it can handle spanning a small gap.  This was the idea - however, I ended up making some miscalculations based on the expected number of layers.  My plywood was 1/4" thick, and I wanted a tray that was ~1.5" deep, which meant a maximum number of 6 layers.  So, I planned on having 5 layers for each board, a simple arithmetic error that means I couldn't do what I was originally planning. So, if you want 6 total layers, each of the 2 laser-cut pieces needs to only have **3 layers**.

### 3. Generate the images!

Okay, now that we're a bit wiser than I was, it's time to actually do it. In QGIS, this process is fairly straightforward - starting from the height we want, and going up by some increment, we draw lines on the map that are of equal height.  This is documented pretty well online, so I won't go into it here. But, at the end of the day, we end up with 2 (slightly) different images:

<!-- #### Tray 1 ('even' layers)
![lake1.png]({{ site.baseurl }}/img/lake_tray/lake1.png)!

#### Tray 2 ('odd' layers)
[lake2.png]({{ site.baseurl }}/img/lake_tray/lake2.png) -->

These two trays were printed out and overlaid on top of each other:
![stacked]({{ site.baseurl }}/img/lake_tray/stacked.jpg)

Hopefully now it makes a bit more sense how they're supposed to fit together.

### 4. Laser cut the lake!

My laser cutter was only capable of raster cutting/etching, which meant each of these took 16 hours. Woof. More powerful / newer cutters are capable of vector cutting, which is significantly faster.

![raw_lake]({{ site.baseurl }}/img/lake_tray/raw_lake.jpg)

### 5. Assemble!

Assembly was very annoying because we essentially had to 'replicate' having 2 cut pieces while only having 1. So, we basically stood all of the cuts off relative to each other and glued them back in place.  Here's a tab-album for display efficiency:

<ul class="nav nav-tabs">
  <li class="active"><a href="#not_cut" data-toggle="tab">Not Cut</a></li>
  <li><a href="#reassembly" data-toggle="tab">Reassembly</a></li>
  <li><a href="#progress" data-toggle="tab">Progress</a></li>
  <li><a href="#almost" data-toggle="tab">Almost Finished</a></li>
  <li><a href="#complete" data-toggle="tab">Assembly Done</a></li>
  <li><a href="#handle" data-toggle="tab">Starting Handle</a></li>
  <li><a href="#finished" data-toggle="tab">Finished</a></li>
</ul>
<div id="myTabContent" class="tab-content">
  <div class="tab-pane fade active in" id="not_cut">
    <p>The weak, weak laser cutter didn't make it all the way through the wood, especially through knots, so e had to mill away some wood from the back side with a dremel.</p>
    <img src="/img/lake_tray/not_cut.jpg" alt="Seattle">
  </div>
  <div class="tab-pane fade" id="reassembly">
    <p>It was slow work sanding off the backsides, and required careful reassembly.</p>
  	<img src="/img/lake_tray/reassembly.jpg" alt="Mountain View">
  </div>
  <div class="tab-pane fade" id="progress">
    <p>Despite the frustration, we manage to make progress!</p>
	<img src="/img/lake_tray/progress.jpg" alt="Mountain View">
  </div>
  <div class="tab-pane fade" id="almost">
    <p>Slowly, we manage to make progress.  In this image, the lake was colored blue by dying the underlying piece of wood, which can only be seen at the lowest point, the lake.</p>
  	<img src="/img/lake_tray/almost_done.jpg" alt="Mountain View">
  </div>
  <div class="tab-pane fade" id="complete">
    <p>Finally, after hours of annoying gluing, we finish the topographical bit. Now, on to the handles!</p>
  	<img src="/img/lake_tray/completed.jpg" alt="Mountain View">
  </div>
  <div class="tab-pane fade" id="handle">
    <p>Now is when I really wish I'd had a router.  Instead, I had to cut these handles out AND cut a PERFECTLY STRAIGHT LINE AT THE PERFECT HEIGHT on each of the border pieces.  This is basically impossible, and this part was definitely the most discouraging part of the entire project.</p>
  	<img src="/img/lake_tray/handle.jpg" alt="Mountain View">
  </div>
  <div class="tab-pane fade" id="finished">
    <p>Eventually, I managed to cut out the handles and make the glass recesses straight enough to fit the piece in.  Boy, that was a lot of work!</p>
  	<img src="/img/lake_tray/finished.jpg" alt="Mountain View">
  </div>
</div>


This was a really fun project, and even though it could have gone better, that's generally how first attempts at a new project are.  

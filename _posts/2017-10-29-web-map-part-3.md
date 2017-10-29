---
layout: post
title: Building a Web Map From Scratch, Part 3
---

Now that we understand some basic web mapping concepts, we can use these to create interesting features in our web maps. For instance, it's possible to create a custom point-clustering algorithm in a web map.

Suppose we have a map with a large number of sites we want to display on our web map and we are doing this by depicted them as circles with a specific radius. We want the user to be able to click on a point and then view in a popup window some information about the site. The problem is that when the map is zoomed out, many of the points overlap, and it's difficult for the user to see how points are there. See image below:

 ![Screenshot 1](/img/ScreenShot1.png)

What we want to do is develop an algorithm for identifying when the circles of two sites overlap and then have these circles merge to create a larger circle representing a cluster or sites. We then want to continue this process until we have a map with no circles that overlap. We also want to let the user click on a cluster and zoom in on the area and then depict the points again. The web map concepts that we covered in the previous blog posts can help us do this.

The point locations in the map above were originally described in terms of latitude and longitude. These were then converted to the Web Mercator world coordinates using the mathematical formula from the first blog post. The units of these coordinates are in meters. Given two site locations, we can determine the distance between the two sites in meters. If we know the scale level, then we know the number of pixels in the map but, more importantly for our purposes, the size a pixel on a screen. In Scale level 2, the width of a pixel represents slightly more than 39 kilometers on the ground. Using this information, we can then determine how many pixel widths separate the two sites.  In the web map, the sites are represented by circles with a radius of 8 pixels. This means that two circles will technically overlap when the distance between the two is less than 16 pixels. Thus, if we know this overlap threshold distance and if we can calculate the distance in world coordinates between two points, it is possible to identify when points overlap.

The process of merging sites is an iterative process. If we have an array of sites, we start by selecting the first site in the array. We then cycle through the remainder of the array and determine the distance between the first site and each of the other sites. If the distance is less than the threshold distance, then a new cluster is created and placed at the end of the array. The center of the new cluster is calculated as the mid point of the overlapping sites. The site found to be overlapping with the first site is then removed from the array. If the first site does not overlap with any of the others in the array, then it is rendered in the map. The process of merging or rendering sites continues until the array of sites is empty. This process produces a series of circles that do not overlap and is much easier for the user to interpret. In the image below, numbers have been placed in the center of the circles corresponding to the number of sites they represent.

![Screenshot 2](/img/ScreenShot2.png)

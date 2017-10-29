---
layout: post
title: Building a Web Map From Scratch, Part 1
---

The first step in building a web map from scratch is to understand the Web Mercator coordinate system. The Web Mercator coordinate system is a projected coordinate system, which means that locations on the spherical surface of the earth are transformed onto a flat two-dimensional plane. This contrasts with geographical coordinate systems, which describe the positions on the Earth surface in terms of positions on a sphere (or approximate sphere, which is a more accurate model of the Earth's surface). The familiar latitude / longitude system is an example of a geographic coordinate system.

The Web Mercator coordinate system is the de facto standard for online web maps. [According to Wikipedia](https://en.wikipedia.org/wiki/Web_Mercator), it was adopted by Google Maps in 2005, and since then, it been adopted by most other mapping systems, including Bing Maps, ESRI, OpenStreetMap.

As mentioned above, the Web Mercator coordinate system describes positions on Earth's surface in terms of a flat, two-dimensional plane. In the east-west direction, the extant ranges from -20,037,507.0671618 meters to 20,037,507.0671618 meters. The total width, -40,075,014.1343236 meters, is approximately equal to the circumference of the Earth at the equator. The Web Mercator coordinate system, like all projected coordinate systems, does not perfectly represent the surface of the Earth.  Projecting the surface of a three-sphere onto a two-dimensional surface always introduces distortions in the objects represented. These issues need not concern us here; however, we should note that the distortions increase at extreme latitudes. For this reason, [according to Wikipedia](https://en.wikipedia.org/wiki/Web_Mercator), Google Maps does not depict locations beyond 85.051129° latitude north and south.

If we want to plot various locations on a web map, we first need their Web Mercator coordinates. Despite being a popular web mapping coordinate system, locations are rarely described in this coordinate system. They are more often described with a latitude and longitude. Fortunately for our purposes, it is possible to mathematically convert lat / long coordinates to Web Mercator coordinates.  The following JavaScript function does this:
```
var calculateWMCoords = function(geoCoords){
  var x = geoCoords.lon * 20037508.34 / 180;
  var y = -Math.log(Math.tan((90 + geoCoords.lat) * Math.PI / 360)) / (Math.PI) * 20037508.34;
  return {x:x, y:y};
}
  ```

In most coordinate systems, the equator is represented as having a y value of 0, with locations in the northern hemisphere (in the top half of the map) as having positive y-values and locations in the southern hemisphere (in the bottom half of the map) as having negative y-values.  Thus, as you progress from the top of the map to bottom, y-values go from larger to smaller.  In browsers, however, as you progress from top to bottom, y-values go from smaller to larger. To make certain programming functions easier, the y-values of the Web Mercator coordinate systems can be flipped so that locations in the northern hemisphere are represented by negative values and locations in the southern hemisphere are represented by positive values. This has been done in the equation above by adding a negative symbol at the beginning of the expression calculating the y coordinate.

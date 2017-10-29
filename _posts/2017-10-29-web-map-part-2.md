---
layout: post
title: Building a Web Map From Scratch, Part 2
---

The second step in building a web map from scratch is to understand how coordinate systems differ from maps. Coordinate systems are ways of representing the locations of objects on the Earth's surface. They are not maps. A map is a tangible representation of all or a portion of the Earth's surface.  All maps have a scale, which is a mathematical explanation of how sizes of objects on the map correspond to sizes of objects on the Earth's surface. For example, a map with a scale of 1:12,000 means that one inch on the map represents 12,000 inches on the Earth. Web maps also have scales. When creating web maps, we want to know the size of objects in pixels. To convert distance values to pixel values, we need to know the number of dots (pixels) per inch.  Web maps typically assume 96 pixels per inch.

Part of what makes web maps potentially more interesting than paper maps is that the user can zoom in and out to see more or less detail. We can think of what's happening here is that the user is actually switching between maps with different scale. Zooming in increases the scale, which in effect switches the map to one with more pixels. Having more pixels allows for more detail.

It is common in web mapping applications to have many predefined scale levels.  Here is a JavaScript object with the properties of maps with different value levels. Each property represents a different scale value.
```
var mapProperties = {
  "0" : {scale:5.91657527591555E8, pixelSize:156543.03392800014, mapSizePx:256},
  "1" : {scale:2.95828763795777E8, pixelSize:78271.51696399994, mapSizePx:512},
  "2" : {scale:1.47914381897889E8, pixelSize:39135.75848200009, mapSizePx:1024},
  "3" : {scale:7.3957190948944E7, pixelSize:19567.87924099992, mapSizePx:2048},
  "4" : {scale:3.6978595474472E7, pixelSize:9783.93962049996, mapSizePx:4096},
  "5" : {scale:1.8489297737236E7, pixelSize:4891.96981024998, mapSizePx:8192},
  "6" : {scale:9244648.868618, pixelSize:2445.98490512499, mapSizePx:16384},
  "7" : {scale:4622324.434309, pixelSize:1222.992452562495, mapSizePx:32768},
  "8" : {scale:2311162.217155, pixelSize:611.4962262813797, mapSizePx:655536},
};
```

On first glance, the scale values seem pretty arbitrary. Why would we want a map with a scale of precisely 1:577790.554289? The answer is that the scale levels were chosen precisely so that they produce maps of specific sizes. For instance, the map corresponding to scale level 2 has a scale of 1: 1.47914381897889E8 and a map size in pixels of 1024. For scale level 3, the map size is 2048 pixels; for scale level 4, the map is 4096 pixels, etc. Having maps of these sizes is useful for overcoming certain logistical problems of depicting basemaps in a web map. A basemap is an image of all or a portion of the Earth's surface is that depicted in a web map. It is usually the lowest layer on a map with other features, such as circles representing specific locations, on top.  In higher scale levels, the map sizes can be quite large. For instance, in scale level 8, the map is 65,536 pixels in height and 65,536 pixels. This image is several gigabytes in size. This is way too large for a typical web user to be able to download for a web map. Fortunately, since a typical user's screen is much smaller than 65,536 pixels in size, there is no need to transfer all this information. Instead, basemap images are divided into 256 pixel tiles. In a web map application, only the tiles viewable in the map viewport are transmitted to the user. The scale values corresponding to the scale levels were chosen so that the whole basemap image can be divided into a whole number of tiles. For instance, in scale level 2, the total map is 1024 pixels in width and height, which corresponds to four basemap tiles, both horizontally and vertically.

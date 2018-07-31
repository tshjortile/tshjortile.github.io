---
layout: post
title: Georeferencing Maps with QGIS
---

Even though there is a QGIS 3.0 now, I stick to version 2.18, because I know and love it and have made projects with it before.
To georeference a map with QGIS (either 2.8 or 3.0 or 3.2) is quite easy and can be done in a few steps:

1. One has to set a base map for QGIS if one wants to georeference by matching geographical points (cities, certain geographical shapes, etc.)
2. Go to Raster and chose 'Georeferencing'. A new window opens.
3. In this window, select "add image/map", and then georeference by clicking on "add point"
4. Here, one can either insert preexistent geographical data (latitude and longitude), or match points on the basemap.
5. After one is finished, just click the play button in the new window (twice in the older version).

The layer should appear now in QGIS.

![georef](/assets/img/georef.png)
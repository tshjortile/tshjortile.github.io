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
Unfortunately, due to limited working memory capacities I only got the points and not the map displayed, even though the referencing worked out as it should have.

![georef](/assets/img/georef_VA.png)

Fortunately, there's also the possibility to get documentation for the points that one georeferences, which can be found here:
![georef_doc](/assets/img/georefdoc.png)

And here's a table with points I took up with "Koordinatenaufnahme", their latitude and longitude:

|Place|Lat|Long|
|---|---|---|
|Alexandria|-77.05842|38.81157|
|Eastville|-75.93692|37.35491|
|Hampton|-76.34548|37.02815|
|Franklin|-76.91844|36.68562|
|Norfolk|-76.27874|36.84861|
|Charleston|-81.63805|38.35924|
split-large-polygons
====================

Note: Forked from https://github.com/rory/split-large-polygons, modified to calculate area always in square meters (not in crs units).

A script to break large polygons into many more managable smaller polygons. It will look for large polygons and split them in half until none are larger than the specified area. It can optionally include an overlap to prevent rendering artifacts.


Usage
=====

Example usage:

    split-large-polygons -d gis -t land_polygons -c the_geom -i gid -a 0.1

This will split the rows in ``land_polygons`` (in the ``gis`` database) into iteratively smaller polygons until none of them are larger than 0.1 in area (in square meters). The area is calculated casting to a PostGIS `geography` primitive.

The table must have a primary key, which is specified in the ``-i``/``--id`` argument. It will delete the older, larger rows. The values of other columns will be copied to the new, split, polygons.

You need PostGIS 2.0 or later, since this program needs the [ST_Split](http://postgis.refractions.net/documentation/manual-2.0/ST_Split.html) method.

Buffer
------

By default, it will cut the polygons in half with no overlap between the resultant polygons. If you use the ``-b``/``--buffer`` option, an overlap of that many units will be left between each polygon split. This can help with rendering artifacts.

Example:

    split-large-polygons -d gis -t land_polygons -c the_geom -i gid -a 0.1 -b 0.01

Here a 0.01 degree overlap will be left between all polygons.

Motivation
==========

I had a table of large polygons of land from http://openstreetmapdata.com/. Large polygons mean that bounding box and indexes are less useful. Lots of small geometries are more effecient and allow better index usage. You can already download pre-split polygons from that site. But if you want even smaller polygons, then you can use this programme.

Some projections do not cover the whole world. Mapnik is able to reproject a 4326 or 3857 dataset from openstreetmapdata to whatever you want. But if one part of the polygon goes outside the valid area of that projection, then mapnik can drop the whole polygon. This can be a problem when it drops (say) the Eurasia polygon. This programme splits the polygons into many smaller, individual shapes so that mapnik will only drop the smaller polygons that are invalid and keep the ones that are valid.


Licence
=======

This code is copyrighted and released under the GNU General Public Licence version 3 (or at your option) a later version. See the LICENCE file for more information.

The author is Rory McCann <rory@technomancy.org>.

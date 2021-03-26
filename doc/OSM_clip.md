#### Clip a `.pbf` file
Extract from an OpenStreetMap `.pbf` file just the area you need.

You have to create a bounding box (a rectangle can be nice, although any polygon would do) in [Osmosis/Polygon Filter File Format](https://wiki.openstreetmap.org/wiki/Osmosis/Polygon_Filter_File_Format).s

Then, just set up an osmosis "pipeline":
```
osmosis --read-pbf file=<input pbf file> --log-progress --bounding-polygon file=<name of your PFFF file> --write-pbf file=<output pbf file>
```s

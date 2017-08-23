Defining path information for your indoor map
===================

eeGeo’s routing service gives you complete control over navigation in your indoor map!  This tutorial shows you how to define the pathways which serve as inputs to the routing service. 

Pathway information is submitted at the same time as the indoor map itself.  The routing service will only return routes which follow the pathways you specify.  See the routing service documentation for further details.

To support routing, you will need to define enough paths to permit navigation to all relevant parts of your building.  You may decide that some parts of your building, such as non-public service or storage areas, should not be available through the routing service. You can simply omit such areas from your path definitions.

You can add as many paths as you like to your building.  Typically, you will define a single path along each hallway or corridor, with additional paths leading from that main path to individual rooms.  Paths will intersect if they share a common point; this enables the routing service to define a route which uses multiple paths.

### Prerequisites

This tutorial builds on the the [previous tutorial](TUTORIAL.md) for creating your indoor map. In particular, it assumes you have installed QGIS and that the .geojson files for each floor level in your building are available.  

We will use the [example data](examples/westport-house) for this tutorial. We will be creating our own path information, so start by copying the `westport-house` folder to a new location and deleting the existing path files from it, i.e. 
```
main-paths.json
westport-house-floor-1-paths.geojson
westport-house-floor-2-paths.geojson
westport-house-floor-gf-paths.geojson
```
should be deleted from the copied folder.

### Defining paths on a single level 

We’ll start by defining paths on floor 2 of Westport House, where the eeGeo offices are.

Open QGIS and start a new project.  Add the `westport-house-floor-2.geojson` file to the project, either by selecting Layer > Add Layer > Add Vector Layer… or by dragging and dropping the file into the main QGIS window area.

In order to draw our paths, we *must* set the Coordinate Reference System to WGS84 (EPSG:4326). In the bottom left corner of the QGIS window you will see the currently active CRS. If this is not already set to EPSG:4326, open the CRS window by clicking that button. To switch your active CRS, you will need to enable "On the fly" CRS transformation. Do this by ticking the box at the top of the CRS window. Then, select WGS84 EPSG:4326 from the window.


Create a new layer to hold the path information by selecting Layer > Create Layer > Create New Shapefile Layer.  Select the Line type, and add Text data fields called name and type.
Save the shapefile layer with a name like `westport-house-floor-2-paths.shp`.  

![New shapefile layer](/images/pathtutorial/NewShapefileLayer.png)


You should now see the new layer in the layers panel.  

There are a few settings which will make editing easier.   Check that the color selected for the lines in the layer contrasts with the color selected for the floor plan -- these can be changed by right clicking the layer in the layers panel and selecting Properties. 

Turn on snapping for line drawing by selecting Settings > Snapping Options… from the main menu bar.  Select "All visible layers", "To vertex" and set a snapping tolerance of your choice (you can always adjust it later).  Check "Enable snapping on intersection" and click OK. 


It can be helpful to highlight the doors you identified when tracing the floor plan., so you can ensure that paths don’t go through walls. Right-click the floor plan layer and select "Open Attribute Table".  Click the "Filter" button and enter "door" in the type field, then click "Select Features".

![Select doors through filter](/images/pathtutorial/SelectDoorsThroughFilter.png)

Now the doors have been highlighted:

![Doors highlighted](/images/pathtutorial/Floor2DoorsHighlighted.png)

Now we are ready to draw our first path. Select the paths layer and turn on editing by clicking the toolbar button or selecting "Toggle Editing". Select the line feature tool by clicking the Add Feature toolbar button, or select Edit > Add Feature.

We’ll begin by drawing a path along the main corridor for the floor. Click to start a path, then click along a sequence of points on the path.  It will help later if points are placed where you expect building users may need to turn, for example, in front of doorways or other hallways.

Draw each path by clicking on a sequence of points.  Paths can be terminated by a right-click, which will bring up a dialog box to set feature properties:

![Feature properties](/images/pathtutorial/NewMainPathway.png)

As when we were adding features on the indoor map, the id may be left blank for now and filled in later.  As this is a basic path, enter "pathway" for the type. The name is optional, but will help you identify the paths you have defined in the final GeoJSON file.

We now have our first path.

![First path](/images/pathtutorial/FirstPath.png)

From this point, we can add more paths connecting to the main path. The snapping editor setting will help us to ensure that paths join up by sharing a point -- the cursor will change color when it is over an existing vertex. You can add a vertex to a path by switching to the node tool and double-clicking where you want the new vertex. Note that paths won't intersect unless they share a vertex, so take care to join paths at a vertex. After adding more paths, we’ll have a map like this: 

![Many paths](/images/pathtutorial/ManyPaths.png)

With the level pathways complete, we can look at adding paths for the elevator and stairs. Paths which connect levels are defined in a separate file, but will need to connect to the network of paths on each level to complete the route between levels.  To help us make this connection, we can add specific named paths which lead to elevators, escalators, and stairs.

Let’s start with the elevator.  Clicking first on the existing path, and then on the center of the elevator, add a path with name "path into elevator". The type in this case is still pathway. Through the name, we can then identify that the second point in the LineString is the elevator point.

Now we’ll draw a similar pathway into the stairwell.  In this case, we’ll add another path leading down the stairs, and set its type to stairs.  This path is highlighted here:

![Path with stairs](/images/pathtutorial/PathWithStairs.png)

Escalators can be treated in a similar way.

We can check the information for the paths we’ve defined in the attribute table (Layer > Open Attribute Table, or the toolbar button). Every path must have a type, which must be one of `"pathway"`, `entrance`, `"elevator"`, `"escalator"`, or `"stairs"`. (Entrances are used to identify paths leading into the building from outside -- see [later in this tutorial](#defining-entrance-paths) for details.)  At this point, we can generate ids for the features, following the instructions in the [other tutorial](TUTORIAL.md#user-content-generate-feature-ids).  No two paths in the building can have the same feature id, but ids used for paths can duplicate ids in building geojson files as they are processed separately.

![Path attribute table](/images/pathtutorial/PathAttributeTable.png)

When you’re finished editing, ensure your edits to the path layer are saved (Layer > Save Layer Edits). Then save the paths as GeoJSON (Layer > Save As…). Browse to the `westport-house` folder where the other .geojson files are, and enter a filename. Ensure the selected format is GeoJSON and the Geometry type is set to LineString. The original path layer can be removed from the project as this new GeoJSON file will replace it.

If you open `westport-house-floor-2-paths.geojson` in a text editor, you should see something like:
```
{
"type": "FeatureCollection",
"crs": { "type": "name", "properties": { "name": "urn:ogc:def:crs:OGC:1.3:CRS84" } },
"features": [
{ "type": "Feature", 
  "properties": { "id": 21, "name": "main corridor", "type": "pathway" }, 
  "geometry": { "type": "LineString", "coordinates": [ [ -2.978619835540779, 56.460249902599863 ], [ -2.978609665758094, 56.46021704637888 ], [ -2.978575244955159, 56.460222522415712 ], [ -2.978551776225885, 56.460149769354963 ], [ -2.978518137713925, 56.460057459019815 ], [ -2.978328041006806, 56.460076234003239 ], [ -2.978139508881639, 56.460100485023489 ], [ -2.978121516189196, 56.460047289237131 ], [ -2.978103523496753, 56.46000348094249 ] ] } },
…
```

There is one further change which must be made to the new GeoJSON file directly.  To identify which level this path file applies to, we must add an attribute to the top level FeatureCollection object.  You can do this in any text editor by adding `"z_order": 2,` after `"type": "FeatureCollection"`, so that the start of the file looks like:
```
{
"type": "FeatureCollection",
"z_order": 2,
"crs": { "type": "name", "properties": { "name": "urn:ogc:def:crs:OGC:1.3:CRS84" } },
"features": [
{ "type": "Feature", "properties": { "id": 21, "name": "main corridor", "type": "pathway" }, 
...
```

The `z_order` value should match that defined for the level in the building’s `main.json` file.

### <a name="defining-entrance-paths"/>Defining entrance paths

If you wish to support routing between buildings, you will need to identify the entrance points for each building. You can do this by drawing a two-point path through each entrance and giving it the type "entrance":

![Entrance path](/images/pathtutorial/EntrancePath.png)

The entrance path type should only be used for the portion of the path which directly leads outside the building; it should not be used for the continuation of the path inside the building.  See the [ground floor paths for Westport House](/examples/westport-house/westport-house-floor-gf-paths.geojson) as an example.

### Defining paths which connect levels

Paths which connect different levels, such as elevators and stairs, require a bit more work in QGIS.  Let’s look at Westport House again.  

![Multiple floor paths](/images/pathtutorial/MultipleFloorPaths.png)

This picture shows paths for level 1 and level 2, and a new layer called main-paths for the multilevel paths.  (Note that this must be saved in a filename called `main-paths.json`.) By setting the floor plan to be semitransparent, we can see the paths in the stairwell need to be joined up. The vertex snapping settings will allow us to add a new path in main-paths which connects these two points.  This new path should have type `"stairs"`:

![Path down stairs](/images/pathtutorial/PathDownStairs.png)

We recommend that it have a name which explicitly describes the levels and the order in which the points were placed, as this will make the next step easier.  However, the name is otherwise unused.

By toggling display of pairs of levels, you should be able to add paths between the levels.  This may be tricky for elevators, but if you have used the same point on all levels within the elevator, you can create the elevator path in a text editor following the pattern in [main-paths.json](examples/westport-house/main-paths.json).  Or, if you have identified the paths into the elevator on each floor using their names, you can extract the point from each level by examining the saved GeoJSON. 

For every multilevel path, an extra attribute must be added to resolve the levels of the points in the linestring.  This can be done by opening `main-paths.json` in a text editor and adding the `"levels"` attribute to each feature object:

```
   {
      "type": "Feature",
      "properties": {
        "id": 1002,
        "name": "stairs down from 2 to 1",
        "type": "stairs"        
      },
      "levels": [2, 1],
      "geometry": {
        "type": "LineString",
        "coordinates": [
          [ -2.978677883006827, 56.460274537451838 ],
          [ -2.978678817233738, 56.460272668998016 ]
        ]
      }
    },
```

The value of this attribute is an array with one integer per coordinate in the `"geometry"` attribute for the feature.  This will be easier if you have included this information in the path name. The level values should match those specified for the `"z_order"` in each level path file.

Finally, the `main-paths.json` file must be extended with an attribute which includes the names of the individual level path files. 
``` 
{
  "type": "FeatureCollection",
  "crs": {
    "type": "name",
    "properties": { "name": "urn:ogc:def:crs:OGC:1.3:CRS84" }
  },
  "features": [
    ...
  ],
  "level_filenames" : [
    "westport-house-floor-gf-paths.geojson",
    "westport-house-floor-1-paths.geojson",
    "westport-house-floor-2-paths.geojson"
  ]
}
```

A level may be omitted if there are no relevant paths on it.  If your building has only one level, you will only need a main.json containing an empty FeatureCollection and a level_filenames attribute.

When you have completed all levels plus `main.json`, add them to the folder containing the indoor map trace, zip the folder, and submit it as normal through the indoor maps API.   Once your indoor map has been built, your routing information can then be promoted so that it becomes available through routing.wrld3d.com.

Questions or problems?  [Log an issue](https://github.com/wrld3d/wrld-indoor-maps-api/issues) and we’ll get back to you as soon as we can.  

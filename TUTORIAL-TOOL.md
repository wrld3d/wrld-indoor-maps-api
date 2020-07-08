Uploading your indoor map to WRLD
===================
If you’re interested in seeing your building’s indoor space in our immersive 3D maps, read on!

These instructions cover creating an indoor map by georeferencing floor plan imagery after you've claimed a building and downloaded the building outline from the [WRLD Indoor Map tool](https://mapdesigner.wrld3d.com/portal/latest/indoor-maps/). After we've processed your indoor map, you'll receive an email containing details of how to view it in WRLD.

WRLD will not share your map data; this means that any submitted indoor maps will remain private to your organisation, unless you choose to publish them.

You can follow along on Mac OS X, Linux or Windows.

| WRLD Indoor Map Format | WRLD 3D Indoor Map |
|:-----------:|:------------:|
|![Indoor map source data](/images/tutorial/overview_coloured.png)|![Indoor map in app](/images/tutorial/overview.png)|

WRLD's office building, Westport House, will be used as the example for this tutorial. The owners have kindly given us permission. Please note that if you do not have the building owner’s approval to submit a map to the service, your submission will not be eligible for inclusion in the public map.

For detailed information on the WRLD Indoor Map format, please refer to the [format documentation](FORMAT.md).

A rough outline of the process:

1. [Install the required software](#install-software)
1. [Georeference the floor plan image](#georeference-floor-plan)
1. [Create a indoor map level](#create-indoor-map-level)
1. [Export the level to GeoJSON](#export-level-to-geojson)
1. [Create the main JSON file](#create-main-json-file)
1. [Package your map for upload](#create-map-package)

---

#### <a name="install-software"/>Install the required software
Pre-requisites:
- A floor plan image (e.g. pdf, jpg, tif, png or bmp) for each of the floors you wish to submit.
- A building outline geojson file, downloaded from the WRLD [Indoor Map Tool](https://mapdesigner.wrld3d.com/indoormap/latest/).  If your building does not exist in WRLD or the outline is inaccurate, you can create your own following [this tutorial](TUTORIAL.md).
- [QGIS](https://www.qgis.org/en/site/forusers/download.html) - Instructions for version 3.10 "A Coruña" is shown here, but any recent version is fine. **Note**: Other GIS software such as [ArcMap](http://desktop.arcgis.com/en/arcmap/) is also available.

We're using QGIS in this example because it's free and easily available.  If you have access to other GIS software, anything that supports georeferencing, polygon creation and exports to GeoJSON should work, although of course you'll need to adapt the instructions below as appropriate.

Install and run QGIS, then ensure you have access to the [Georeferencer](https://docs.qgis.org/3.10/en/docs/user_manual/plugins/core_plugins/plugins_georeferencer.html) plugin (under Raster > Georeferencer...). On recent versions of QGIS the plugin comes preinstalled but you may need to install and/or enable it (Plugins > Manage and Install Plugins > Search for “Georeferencer” > Choose “Georeferencer GDAL” > click “Install Plugin” and ensure that the checkbox next to the plugin is ticked).

![Enable Georeferencer Plugin](/images/tutorial/enable_georeferencer.png)

If using a version of QGIS older than 2.18 we’d also recommend you install the [OpenLayers](http://docs.qgis.org/2.18/en/docs/training_manual/qgis_plugins/plugin_examples.html?highlight=openlayers#basic-fa-the-openlayers-plugin) plugin to allow you to pull map & satellite imagery into your QGIS scene. This makes certain steps of the process much easier, as you can line up your indoor map features with the satellite imagery.

**Note**: For advanced users accustomed to GIS packages, if you have access to useful data (such as a shapefile for the building that contains your indoor map), feel free to use these, as they’ll likely be more accurate than freely available data.

---

#### <a name="georeference-floor-plan"/>Georeference the floor plan image
If you have an image of your building’s floor plan, you can use [georeferencing](https://en.wikipedia.org/wiki/Georeference) to help turn it into a map. Unfortunately, images do not contain geographic location data so while they may be detailed and legible, there’s nothing to associate the contents of the image with spatial locations in the world, or orient it correctly.  

[Georeferencing](https://en.wikipedia.org/wiki/Georeference) basically means saying ‘point **P** on the image is at geographic coordinate **Q**’. This will allow you to view your floor plan image in QGIS with the correct location and orientation.

- Open QGIS and create a New Project.
- Add a map layer to help locate your building (experiment with each to see which one best suits your location of interest):
  - To add an OpenStreetMap layer simply select XYZ Tiles > OpenStreetMap from the Browser panel and drag it to the Layers panel.
  - To add a Google Maps Satellite layer, right-click on XZY Tiles in the Browser panel, select "New Connection..." and set the Name to "Google Satellite" and URL to ``http://mt0.google.com/vt/lyrs=s&hl=en&x={x}&y={y}&z={z}``.  Click on the newly-created XYZ Tiles > Google Satellite and drag it to the Layers panel.
  - Older versions of QGIS will need to use the OpenLayers plugin. Choose Web > OpenLayers plugin > and then choose a layer.
- Browse to your building’s location via left-click panning & zooming with the mouse wheel.
- Open the Georeferencer (Raster > Georeferencer...).

![Georeferencer toolbar](/images/tutorial/georeferencing_toolbar.png)
- Click “Add Raster”.
- Add your floor plan image. If you’re using OpenLayers, set the CRS (coordinate reference system) as WGS84 / Pseudo Mercator ([EPSG: 3857](http://spatialreference.org/ref/sr-org/6864/)).
- The image should be displayed in the Georeferencer window.

[<img src="/images/tutorial/georeference_plan_thumb.png">](/images/tutorial/georeference_plan.png)
- The next step involves selecting a point on the indoor map image and then assigning it a location.
- In the Georeferencer window, ensure the “Add Point” tool selected and left click the first point to be referenced (a corner is a good choice).
- Click the “From map canvas” button.
- Left click the location on the map matching the point you’ve just selected.
- Click the "OK" button.

![Point in Georeferencer](images/tutorial/georeference_side_by_side.png)
- Repeat this process for a handful of points on the building perimeter; try to pick the points that are clearly visible on the map (e.g. building corners). Inaccurately georeferenced points cause distortion so if in doubt, don't add it (in this example, only 4 points were georeferenced).
- Open the Transformation Settings dialog and choose a filename for “Output raster”. If you're using OpenLayers, the Target SRS should be set to [EPSG:3857](https://en.wikipedia.org/wiki/Web_Mercator_projection#EPSG:3857). Otherwise, set it the QGIS project's CRS; to check this, choose Project > Properties > CRS. The rest of the options should match the following:

![Transform settings](/images/tutorial/transform_settings.png)
- Click the “OK” button.
- Click the “Start Georeferencing” button.
- The transformed image should open in the QGIS main scene view.

![Georeferenced image](images/tutorial/georeferenced_image.jpg)
- If nothing appeared, you likely forgot to check the “Load in QGIS when done” box. You can drag the georeferenced image into QGIS, or simply re-export with the box checked.
- In the Layers panel, locate the layer that’s just been added. Double click it, select "Transparency" and adjust the "Global Opacity" slider so you can see the underlying map imagery.
- If the result is sub-par and heavily distorted, try the process again and choose different points to georeference. Again, fewer is often better.

---

#### <a name="create-indoor-map-level"/>Create an indoor map level

We’ve now got a georeferenced floor plan image, and we’re ready to begin tracing the indoor map features for a single building level. For the purposes of this tutorial, we're tracing the Westport House floor that contains the WRLD Dundee office -- this is the second level.

- Set the transparency on your floor plan image layer fully opaque once more.
- Add your building outline as a new QGIS layer, either by dragging it into your QGIS window, or:
  - Go to the Layer menu > Add Layer > Add Vector Layer...
  - Select your file as the Source > Vector Dataset(s).
  - Click "Add".
- Ensure that your building outline layer is selected by clicking on it in the Layers panel.
- Make the layer transparent (Layer > Properties > Symbology) and move the opacity slider to 50%.
- Create a new QGIS layer for your indoor map level via Layer > Create Layer > New Shapefile Layer...
- Give your new layer a suitable name. Something like "my-indoor-map-name-level-x" is good (where level-x corresponds to whatever floor of the building you're about to create):
  - Click on the File Name browser (...) to select the location and name for your file.
- Select "Polygon" from the Geometry Type drop-down, and select the appropriate CRS (again, this is typically [EPSG:3857](https://en.wikipedia.org/wiki/Web_Mercator_projection#EPSG:3857) unless your QGIS project is using something else).
- Finally, add “type” and “name” to the fields list (under “New Field”, fill in the field name and click the “Add to Fields List” button). The default data types are fine (String, 80 width).
- Click “OK”.

![New level layer](/images/tutorial/new_level_layer.png)

- Ensure that your new layer is selected by clicking on it in the Layers panel.
- In the layers panel, left click & drag the new feature layer to the top of the panel (we need our new layer to be the top-most layer, or it will be hidden by the others).
- Make the layer transparent (Layer > Properties > Symbology) and move the opacity slider to 50%.

  This allows you to see through to the building outline and floor plan image layer, which makes tracing easier. Furthermore, it's expected that polygons will overlap; setting the transparency helps us make sense of overlaps (overlapping polygons have a darker shade).

- The first feature we’re going to create is an outline of this particular indoor map level. A building outline will be used to create geometry for the level's floor plane.
- Click the “Toggle Editing” button.  ![Toggle editing button](/images/tutorial/toggle_editing.png)
- If you're happy with the building outline you downloaded from the WRLD Indoor map tool, you can add it to your map layer.  Note that the level outline doesn't have to exactly match the building outline - different floors can have different outlines!
  - Click the "Select Features" button.  ![Select features button](/images/tutorial/select_features.png)
  - Select your building outline layer in the Layers panel by clicking on it.
  - Click on the building outline polygon - it should highlight in yellow.
  - Select Edit menu > Copy Features.
  - Select your level feature layer in the Layers panel by clicking on it.
  - Select Edit menu > Paste Features.
- If you want to trace out the level outline:
  - Click the “Add Features” button.  ![Add features button](/images/tutorial/add_features.png)
  - Trace the boundary of the floor plan image by drawing a polygon, one point at a time (don't worry about geometry warnings).
  - When you’re happy with the polygon, right click to accept it.
  - In the confirmation dialog, select the ‘type’ field and change its value to `building_outline`.

  If you have a descriptive name to use, enter it. Otherwise, leave the name as `NULL`.

  Leave the id as `NULL`. While it is possible to manually enter ids after creating each feature, it's easier to leave them as `NULL` and set them all later. I would recommend this, as it's less error-prone.

![Building outline confirmation](/images/tutorial/building_outline_confirmation.png)
- You should now have a level outline polygon in your QGIS layer:

![Traced floor plan outline](/images/tutorial/level_outline.png)
- We've now got our level outline; the next thing to do is add the contents of the floor.

- WRLD’s map format displays the polygons you create differently, depending on the feature type you give them. The `building_outline` will appear as the floor of your indoor map. On top of it, we’ll add features such as rooms, walls and windows. There are additional feature types which can be used to give your map more detail; Refer to the [format documentation](FORMAT.md) for a full list.

- A good first step is to create the walls and windows around the outside of the floor, using the feature types `wall` and `window`. Drawing perfectly straight lines can be tricky in QGIS; fortunately, there are some tools that make it possible to trace polygons with precision.

- First, enable snapping. The snapping settings can be found under Project > Snapping Options. Ensure that snapping mode is set to "All layers", and that you are snapping "To vertex and segment". Setting the Tolerance to around 12 px should be suitable. If not, experiment with raising or lowering it.

![Snapping Options](/images/tutorial/snapping.png)

- Once snapping is enabled, when you are adding Features a small pink cross will appear whenever you move your mouse cursor close to a polygon edge or vertex.

- Next, enable the Advanced Digitizing Toolbar and the Advanced Digitizing Panel. These can be enabled by right clicking on the gray space by the toolbars at the top of the screen, or in the Views > Panels/Toolbars menus.

![Advanced Digitizing](/images/tutorial/digitizing.png)

- With these panels visible, click the "Add Features" button.

![Add features button](/images/tutorial/add_features.png)

- With "Add features" selected, the "Enable Advanced Digitizing Tools" button will become active. Click on it.

![Enable Advanced Digitizing Tools](/images/tutorial/advdig.PNG)

- Now you can place the first point of your external wall. With snapping enabled, your cursor will snap to the edge of any other polygon, in this case the building outline. Since you currently  have no other polygons, the easiest place to start is in a corner of the building outline. Once you have placed a point there, you can place the second point farther along the edge of the building outline.

- When you are actively placing points to create a polygon, two more buttons will become active on the Advanced Digitizing Panel. These two buttons will allow you to trace lines that are parallel and perpendicular to existing geometry. In this case we want the next segment of our wall to be perpendicular to the building outline so our polygon can be perfectly rectangular, so click on the perpendicular button

![Perpendicular](/images/tutorial/perpendicular.PNG)

- With the button selected, move the mouse over the vertex or segment that you would like to draw perpendicular to. Observe the blue dotted line that appears: this shows you the line that your next point will be snapped to. If the position of the line looks good, click on the vertex or segment, and observe that as you move your mouse your next point is locked to the blue dotted line. When you are ready to place your next point, click again.
To draw the other side of the wall, use the same method with the parallel button.

![Parallel](/images/tutorial/parallel.PNG)

- Click the parallel button, then click the vertex or segment that you would like to draw parallel to. Then, move your mouse to the desired position and left click to place the point. Note that while in this mode, you can still snap to vertices and segments by moving your mouse close to them. By doing this, you can create a wall with perfectly parallel edges and 90 degree angles.

- Take care not to overlap any features (except for your building outline). See the images below for an idea of how your features will appear in your 3D map.


![Adding a wall](/images/tutorial/add_wall.PNG)

![Wall in 3D map](/images/tutorial/wall_in_app.png)

- In some circumstances, it may be better to use the `room` feature type instead of creating the walls around a room. The room feature type will appear hollowed out with walls around the edge of the polygon. Additionally, if you enter any text into the "name" field of your room, it will appear in your map as a text label hovering over the room.

![Room feature type](images/tutorial/room_type.png)

- It is possible to show certain icons over features in place of text labels. This is done by entering a specific string into the name field. For example, in the image below, the bathrooms have been given a bathroom icon by using the name "Bathroom". A full list of these strings can be found in the [format documentation.](FORMAT.md)

![Bathroom icon](/images/tutorial/poilabel.png)

- Door features should be used to cover doorways. This feature appears in the app slightly larger than the polygon created in QGIS, so it is important to keep the door parallel with the connecting walls otherwise it will appear incorrect. In addition, double doors or sliding doors should be represented by a single door polygon. See the images below for an example.

![Adding a door](images/tutorial/doors_geojson.PNG)

![Doors in 3D map](images/tutorial/doors_in_app.png)

- We'd recommend building out a first version, examining the result and then creating further iterations with slight tweaks to the way features are categorised. Here's an image showing Westport House with its interior features mapped. This example map is available to download [here](/examples/) and can give you an idea of how to build your indoor map.

![Multiple rooms added](/images/tutorial/wph.PNG)

<a name="generate-feature-ids"/>

- Finally, let’s set the feature ids (if you’ve been manually entering ids after creating polygons, these steps are not necessary and can safely be skipped).

  Each feature needs an id, and the id must be unique **across all levels of your indoor map** (e.g. if level 1 has a room with an id of 1 and level 2 also has a room with the same id of 1, it is illegal).

  Thankfully, we can use QGIS's Field Calculator feature to generate ids for a level.

- Open the layer’s attribute table via Layer > Open Attribute Table
- Click the Field Calculator button.

![Field Calculator](/images/tutorial/field_calculator_button.png)
- Change the checkbox to “Update existing field”.
- In the combobox, select the “id” field.
- In the left hand expression box enter the following:

  `toint(concat('n', tostring(@row_number)))`
  ... where `n` is the floor/level number

  Replace 'n' for your level's number (but, keep the single quotes).  

  E.g. for the second level of our indoor map, we'd modify the above to:

  `toint(concat('2', tostring(@row_number)))`
- If you were generating ids for level 2 of your indoor map, the dialog would look something like:

![Field calculator](/images/tutorial/field_calculator.png)
- Click “OK”.

  This will generate unique features ids. Each feature id starts with the interior level id (e.g., level 2 features will have ids 21, 22, 23..., level 3 features will have ids 31, 32, 33...)

- Verify the ids have been generated correctly by opening the layer's Attribute Table

  Layer > Open Attribute Table

  Here's Westport House level 2 with auto-generated ids:

![Attribute table with auto-generated ids](/images/tutorial/attribute_table_with_generated_ids.png)

---

#### <a name="export-level-to-geojson"/>Export the level to GeoJSON

QGIS Shapefile layers default to storing geometry as Multipolygons.  To prepare our level to be imported into WRLD's tools we need to convert to GeoJSON format, containing only polygons, in the WGS84 Coordinate Reference System.

- Select the layer containing your level features in the Layers panel.
- Open the Vector > Geometry Tools > Multipart to Singlepart... tool.
- Click "Run".
- Right-click on the newly-created "Single parts" layer in the Layers panel and choose Export > Save Features As...
  - Under format, choose GeoJSON.
  - Click on the File Name browser (...) to select the location and name for your file.
  - Change the CRS setting to WGS84 ([EPSG: 4326](http://spatialreference.org/ref/epsg/4326/)).
  - Check the "Add saved file to map".
  - Click “OK”.
- If you want to make further edits to your floor plan you can just edit the GeoJSON layer rather than editing the Shapefile layer you first created and going through the conversion and export steps each time.  It's now safe to remove the "Single parts" and your original Shapefile layer.

You now have a single level of your building digitised! If you have more floor plans for your building, simply repeat the above steps for each level.

**Note**: It's often useful to examine the exported json, but QGIS exports unformatted json which makes it tough to read due to the lack of indenting.

This is entirely optional, but if you wish to re-format your GeoJSON files, you can paste them into [jsonlint](http://jsonlint.com) or run a single command from a terminal (this assumes python is installed and in your path):

```sh
$ cat unformatted.geojson | python -m json.tool > formatted.geojson
```

---

#### <a name="create-main-json-file"/>Create the main JSON file

Now that you have one or more levels of your building traced and exported in an WRLD-friendly format, the next step is to create the main JSON file.

Navigate to the directory to which you exported the indoor map level(s), create an empty text document named `main.json`.

The `main.json` file specifies general information about the indoor map and also references the level GeoJSON file(s) that were previously exported from QGIS. For this part of the guide, it may be more instructive to use example files as a reference.

For more detail, please see the [format documentation](FORMAT.md).

After creating the `main.json` file, the directory structure should look something like this:

```
directory: westport-house
├── main.json
├── westport-house-level-2.geojson
```

Choose an `id`, `name`, `owner` and `location`, as well as referencing the `levels` that were exported previously.

The indoor map `id` is arbitrary; choose something that is recognisable to you, such as the name of the building. `name` is a human-readable indoor map name. `owner` should be the name of your company or organisation. `location` should have a `type` of `Point`, and the `coordinates` is a list containing two elements: the longitude & latitude of the indoor map in degrees.

`levels` is an array of [level](FORMAT.md#level) objects. Ensure that each exported indoor map level has a corresponding entry in this array. If a floor plan was traced and a level exported, but the `main.json` file does not reference it, the level in question will not be included in the processed indoor map.

For the example of westport-house, there is only a single level with an id of `westport-house-level-2` and a GeoJSON file of `westport-house-level-2.geojson`.

For westport-house, the `main.json` file contains:
```
{
	"id": "westport_house",
	"name": "Westport House",
	"owner": "WRLD",
	"location": {
		"type": "Point",
		"coordinates": [-2.977999, 56.459920]
	},
	"levels": [{
		"id": "westport-house-level-2",
		"name": "2",
		"readable_name": "Second floor",
		"z_order": 2,
		"filename": "westport-house-level-2.geojson"
	}]
}
```

This concludes the editing phase.

---

#### <a name="create-map-package"/>Package your map for upload

- Place all of the indoor map files (the `main.json` file and the level GeoJSON file[s]) and place them in a directory (e.g. `~/my-indoor-map`)
- Zip the directory.
  - On Mac OS X, right click on the folder and select Compress.
  - On Windows, navigate to the directory containing your indoor map files.
    - Right click > New > Compressed (zipped) Folder and choose an appropriate name.
    - Drag the .json & .geojson files onto the .zip file.
  - On Linux, you can do this via opening a terminal and entering the following commands:
```
$ cd ~/my-indoor-map
$ zip -r my-indoor-map.zip .
```
- Verify that the .zip structure matches the one given in the [format documentation](FORMAT.md#archive-structure).
- Return to the [Indoor Map Designer](https://mapdesigner.wrld3d.com/indoormap/latest) and upload your indoor map packageusing the Upload Map section of the Upload Map Tool in the left hand side panel.
---

Troubleshooting
===================
#### <a name="multipolygons"/>Multipolygons
If you are receiving an error related to multipolygons in your submission, first check that you ran the [Multipart to Singleparts](#export-level-to-geojson) conversion step when you exported your layer to geojson.

If the error still occurs, it probably refers to one of your units being split into two or more parts. For instance:

[<img src="/images/tutorial/multipolygon_appearance.png">](/images/tutorial/multipolygon_appearance.png) 

If you are struggling to locate the multipolygon in question, you can use QGIS' **Topology Checker** to find it:

1. Enable the Topology Checker plugin with the Plugins > Manage and Install Plugins... dialog.
2. Open the Topology Checker panel via *Vector > Topology Checker* 

  [<img src="/images/tutorial/topology_checker_location.png">](/images/tutorial/topology_checker_location.png)
  
3. Click the *Configure* button (Wrench icon)
4. Under *Current Rules* set the layer you want to check and set the rule to *"must not have multi-part geometries"*
5. Click *Add Rule* then click *OK*

  [<img src="/images/tutorial/topology_checker_steps.png">](/images/tutorial/topology_checker_steps.png)
  
6. Click *Validate All* (check mark) in the Topology Checker panel
7. The offending multipolygon will be displayed in the error list, and will be highlighted in red in QGIS

  [<img src="/images/tutorial/topology_checker_results.png">](/images/tutorial/topology_checker_results.png)

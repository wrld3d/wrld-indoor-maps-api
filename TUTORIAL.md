Submitting your indoor map to eeGeo
===================
eeGeo’s 3D Indoor Maps offering is available and we are now accepting submissions. If you’re interested in seeing your building’s indoor space in our immersive 3D maps, read on!

This post covers creating an indoor map by georeferencing floor plan imagery and then submitting it to the eegeo indoor maps API. After we've processed your indoor map, you'll receive an email containing details of how to view it using the eeGeo SDK.

eeGeo will not share your map data; this means that any submitted indoor maps will remain private to your organisation.

You can follow along on Mac OS X, Linux or Windows.

| eeGeo Indoor Map Format | eeGeo 3D Indoor Map |
|:-----------:|:------------:|
|![Indoor map source data](/images/tutorial/overview_coloured.png)|![Indoor map in app](/images/tutorial/overview_eegeo.png)|

eeGeo's office building, Westport House, will be used as the example for this post. The owners have kindly given us permission. Please note that if you do not have the building owner’s approval to submit a map to the service, your submission will be rejected.

For detailed information on the eeGeo Indoor Map format, please refer to the [format documentation](FORMAT.md).

A rough outline of the process:

1. [Install the required software](#install-software)
1. [Georeference the floor plan image](#georeference-floor-plan)
1. [Create a indoor map level](#create-indoor-map-level)
1. [Export the level to GeoJSON](#export-level-to-geojson)
1. [Create the main JSON file](#create-main-json-file)
1. [Package your map for submission](#create-map-package)
1. [Submit to the eeGeo Indoor Maps API](#submit-package)

---

#### <a name="install-software"/>Install the required software
Pre-requisites:
- A floor plan image (tif, png or bmp) for each of the floors you wish to submit.
- [QGIS](https://www.qgis.org/en/site/forusers/download.html) (2.12 Lyon at the time of writing) with the Georeferencer and OpenLayers plugins (**Note**: Other GIS software such as [ArcMap](http://desktop.arcgis.com/en/arcmap/) is available, but we're using QGIS for this guide as it's free).
- [curl](https://curl.haxx.se/download.html) (comes as standard on OS X and Linux).

We're using QGIS in this example because it's free and easily available.  If you have access to other GIS software though, anything that supports georeferencing, polygon creation and exports to GeoJSON should work, although of course you'll need to adapt the instructions below as appropriate.

Install and run QGIS, then ensure you have access to the [Georeferencer](http://docs.qgis.org/2.2/en/docs/user_manual/plugins/plugins_georeferencer.html) plugin (under Raster > Georeferencer). On Windows, this functionality comes as standard. On OS X and Linux, you may have to install it via the plugins manager (Plugins > Manage and Install Plugins > Search for “Georeferencer” > Choose “Georeferencer GDAL” > click “Install Plugin”).

We’d also recommend you install the [OpenLayers](http://docs.qgis.org/2.2/en/docs/training_manual/qgis_plugins/plugin_examples.html?highlight=openlayers#basic-fa-the-openlayers-plugin) plugin. OpenLayers allows you to pull map & satellite imagery into your QGIS scene. This makes certain steps of the process much easier, as you can line up your indoor map features with the OpenLayers imagery.

**Note**: For advanced users accustomed to GIS packages, if you have access to useful data (such as a shapefile for the building that contains your indoor map), feel free to use these, as they’ll likely be more accurate than OpenLayers data.

---

#### <a name="georeference-floor-plan"/>Georeference the floor plan image
If you have an image of your building’s floor plan, you can use [georeferencing](https://en.wikipedia.org/wiki/Georeference) to help turn it into a map. Unfortunately, images do not contain geographic location data so while they may be detailed and legible, there’s nothing to associate the contents of the image with spatial locations in the world, or orient it correctly.  

[Georeferencing](https://en.wikipedia.org/wiki/Georeference) basically means saying ‘point **P** on the image is at geographic coordinate **Q**’. This will allow you to view your floor plan image in QGIS with the correct location and orientation.

- Open QGIS and add a layer of your choice via OpenLayers (e.g. to add an Open Street Map layer, choose Web > OpenLayers plugin > and then choose a layer -- experiment with each to see which one best suits your location of interest).
- Browse to your building’s location via left-click dragging & zooming with the mouse wheel.
- Open the Georeferencer (Raster > Georeferencer > Georeferencer).

![Georeferencer toolbar](/images/tutorial/georeferencing_toolbar.png)
- Click “Add Raster”.
- Add your floor plan image. If you’re using OpenLayers, set the CRS (coordinate reference system) as WGS84 / Pseudo Mercator ([EPSG: 3857](http://spatialreference.org/ref/sr-org/6864/)).
- The image should be displayed in the Georeferencer window.

[<img src="/images/tutorial/georeference_plan_thumb.png">](/images/tutorial/georeference_plan.png)
- The next step involves selecting a point on the indoor map image and then assigning it a location.
- In the Georeferencer window, ensure the “Add Point” tool selected and left click the first point to be referenced (a corner is a good choice).
- Click the “From map canvas” button.
- Left click the location on the map matching the point you’ve just selected.

![Point in Georeferencer](images/tutorial/georeference_side_by_side.png)
- Repeat this process for a handful of points on the building perimeter; try to pick the points that are clearly visible on the map (e.g. building corners). Inaccurately georeferenced points cause distortion, so if in doubt, don't add it (in this example, only 4 points were georeferenced).
- Open the Transformation Settings dialog and choose a filename for “Output raster”. If you're using OpenLayers, the Target SRS should be set to [EPSG:3857](http://spatialreference.org/ref/sr-org/6864/). Otherwise, set it the QGIS project's CRS; to check this, choose Project > Project Properties > CRS. The rest of the options should roughly match the following:

![Transform settings](/images/tutorial/transform_settings.png)
- Click the “OK” button.
- Click the “Start Georeferencing” button.
- The transformed image should open in the QGIS main scene view.

![Georeferenced image](images/tutorial/georeferenced_image.jpg)
- If nothing appeared, you likely forgot to check the “Load in QGIS when done” box. You can drag the georeferenced TIF into QGIS, or simply re-export with the box checked.
- In the Layers panel, locate the layer that’s just been added. Double click it, adjust with style and transparency so you can see the underlying map imagery.
- If the result is sub-par and heavily distorted, try the process again and choose different points to georeference. Again, fewer is often better.

---

#### <a name="create-indoor-map-level"/>Create an indoor map level

We’ve now got a georeferenced floor plan image, and we’re ready to begin tracing the indoor map features for a single building level. For the purposes of this tutorial, we're tracing the westport house floor that houses the eeGeo offices -- this is the second level.

- Set the opacity on your floor plan image layer fully opaque once more.
- Create a new QGIS layer via Layer > Create Layer > New Shapefile Layer.
- When prompted, choose the Type: Polygon radio button option, then choose the appropriate CRS (again, this is typically [EPSG:3857](http://spatialreference.org/ref/sr-org/6864/) unless your QGIS project is using something else).
- Finally, add “type” and “name” to the attribute list (under “New attribute”, fill in the attribute name and click the “Add to attributes list” button). The default data types are fine (String, 80 width).

![New level layer](/images/tutorial/new_level_layer.png)
- Click “OK”.
- Give your new layer a suitable name. Something like my-indoor-map-name-level-x is good (where level-x corresponds to whatever floor of the building you're about to create).

![New level layer confirmation](/images/tutorial/new_level_layer_confirmation.png)
- Ensure that your new feature layer is selected in the Layers panel
- In the layers panel, left click & drag the new feature layer to the top of the panel (we need our new layer to be the top-most layer, or it will be hidden by the others).
- Open the Layer Style menu (Layer > Properties > Style) and move the transparency slider to 50%.

  This allows you to see through to the floor plan image layer, which makes tracing easier. Furthermore, it's expected that polygons will overlap; setting the transparency helps us make sense of overlaps (overlapping polygons have a darker shade).

- The first feature we’re going to create is an outline of this particular indoor map level. A building outline will be used to create geometry for the level's floor plane.
- Click the “Toggle Editing” button.

![Toggle editing button](/images/tutorial/toggle_editing.png)
- Click the “Add Features” button.

![Add features button](/images/tutorial/add_features.png)
- Trace the boundary of the floor plan image by drawing a polygon, one point at a time (don't worry about geometry warnings).
- When you’re happy with the polygon, right click to accept it.
- In the confirmation dialog, select the ‘type’ field and change its value to `building_outline`.

  If you have a descriptive name to use, enter it. Otherwise, leave the name as *NULL*.

  Leave the id as *NULL*. While it is possible to manually enter ids after creating each feature, I prefer to leave them as NULL and fix them later. I would recommend this, as it's less error-prone.

![Building outline confirmation](/images/tutorial/building_outline_confirmation.png)
- You should now have a building outline polygon in your QGIS layer.

![Traced floor plan outline](/images/tutorial/level_outline.png)
- We've now got our building outline; the next thing to do is add the contents of the floor.

- eeGeo’s map format displays the polygons you create differently, depending on the feature type you give them. The “building_outline” will appear as the floor of your indoor map. On top of it, we’ll add features such as rooms, walls and windows. There are additional feature types which can be used to give your map more detail; Refer to the [format documentation](FORMAT.md) for a full list.

- A good first step is to create the walls and windows around the outside of the floor, using the feature types “wall” and “window”. Drawing perfectly straight lines can be tricky in QGIS; fortunately, there are some tools that make it possible to trace polygons with precision.

- First, enable snapping. The snapping settings can be found under Settings > Snapping Options. Ensure that snapping mode is set to "All layers", and that you are snapping "To vertex and segment". If you are editing in EPSG:3857 then setting your Tolerance to around 0.1 should be suitable. If not, experiment with raising or lowering it.

![Snapping Options](/images/tutorial/snapping.PNG)

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

- In some circumstances, it may be better to use the “room” feature type instead of creating the walls around a room. The room feature type will appear hollowed out with walls around the edge of the polygon. Additionally, if you enter any text into the name field of your room, it will appear in your map as a text label hovering over the room.

![Room feature type](images/tutorial/room_type.png)

- It is possible to show certain icons over features in place of text labels. This is done by entering a specific string into the name field. For example, in the image below, the bathrooms have been given a bathroom icon by using the name "Bathroom". A full list of these strings can be found in the [format documentation.](FORMAT.md)

![Bathroom icon](/images/tutorial/poilabel.png)

- Door features should be used to cover doorways. This feature appears in the app slightly larger than the polygon created in QGIS, so it is important to keep the door parallel with the connecting walls otherwise it will appear incorrect. In addition, double doors or sliding doors should be represented by a single door polygon. See the images below for an example.

![Adding a door](images/tutorial/doors_geojson.PNG)

![Doors in 3D map](images/tutorial/doors_in_app.png)

- We'd recommend building out a first version, examining the result and then creating further iterations with slight tweaks to the way features are categorised. Here's an image showing Westport House with its interior features mapped. This example map is available to download [here](/examples/) and can give you an idea of how to build your indoor map.

![Multiple rooms added](/images/tutorial/wph.PNG)

<a name="generate-feature-ids"/>   
- Finally, let’s fix up the feature ids (if you’ve been manually entering ids after creating polygons, these steps are not necessary and can safely be skipped).

  Each feature needs an id, and the id must be unique **across all levels of your indoor map** (e.g. if level 1 has a room with an id of 1 and level 2 also has a room with the same id of 1, it is illegal).

  Thankfully, we can use QGIS's Field Calculator feature to generate ids for a level.

- Open the layer’s attribute table via Layer > Attribute Table
- Click the Field Calculator button.

![Field Calculator](/images/tutorial/field_calculator_button.png)
- Change the checkbox to “Update existing field”
- In the combobox, select the “id” field
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

- Highlight the layer you created in the Layers panel
- Right click and choose “Save As…”
- Under format, choose GeoJSON
- Under encoding, select “UTF-8”
- Change the CRS setting to WGS84 ([EPSG: 4326](http://spatialreference.org/ref/epsg/4326/))
- Enter a filename/location
- Click “OK”

You now have a single level of your building digitised. If you have more floor plans for your building, simply repeat the above steps for each level.

**Note**: It's often useful to examine the exported json, but QGIS exports unformatted json which makes it tough to read due to the lack of indenting.

This is entirely optional, but if you wish to re-format your GeoJSON files, you can paste them into [jsonlint](http://jsonlint.com) or run a single command from a terminal (this assumes python is installed and in your path):

```sh
$ cat unformatted.geojson | python -m json.tool > formatted.geojson
```

---

#### <a name="create-main-json-file"/>Create the main JSON file

Now that you have one or more levels of your building traced and exported in an eeGeo-friendly format, the next step is to create the main JSON file.

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
	"owner": "eeGeo",
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

#### <a name="create-map-package"/>Package your map for submission

- Place all of the indoor map files (the `main.json` file and the level GeoJSON file[s]) and place them in a directory (e.g. `~/my-indoor-map`)
- Zip the directory
- On OS X / Linux, you can do this via opening a terminal and entering the following commands:
```
$ cd ~/my-indoor-map
$ zip -r my-indoor-map.zip .
```
- On Windows, navigate to the directory containing your indoor map files
  - Right click > New > Compressed (zipped) Folder and choose an appropriate name
  - Drag the .json & .geojson files onto the .zip file
- Verify that the .zip structure matches the one given in the [format documentation](FORMAT.md#archive-structure)

---

#### <a name="submit-package"/>Submit to the eeGeo Indoor Maps API

Now that you have your archive in eeGeo’s format, you can submit it to our Indoor Map API to make it part of our 3D world.  Note that for all of these commands, you’ll need to include your developer authentication token, which you can find on the [API keys](https://www.eegeo.com/developers/apikeys/) page.  If you haven’t signed up yet, please take a moment to do so now.

The first step is to make a post request to our submission service, which is accessible via a simple [REST API](https://en.wikipedia.org/wiki/Representational_state_transfer).  [CURL](https://curl.haxx.se/) commands are shown here as examples.  Required parameters include contact details for the rights holders of the building you’re submitting.  These allow eeGeo to confirm that correct approval has been given for the map submission.  We also request that you submit a [GeoJSON](http://geojson.org/) outline file showing the regions in which your indoor map edits will take place.

The supplied outline file should contain one or more Polygon features in the default [Coordinate Reference System](http://geojson.org/geojson-spec.html#coordinate-reference-system-objects).  It can be created via QGIS (as above), using online editors such as [GeoJSON.io](http://geojson.io) or with any other GIS software capable of exporting to GeoJSON.  Later edits will be checked against the polygons described in this file, so please make sure they fully cover the regions you want to edit but only intersect those buildings which form part of the indoor map you are submitting.

```sh
$ curl -v -XPOST https://indoor-maps-api.eegeo.com/v1/edits/?token=dev_auth_token -F name="my venue name" -F venue_street_address="<address>" -F venue_phone_number="<phone no.>" -F venue_email="<email address>" -F submission_contact_email="<email address for notifications>" -F venue_outline="@/path/to/my/file"
```
On successful completion of this request, you should receive a JSON packet containing a [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier).  Please make note of this UUID, as subsequent API calls will need to refer to it.
```json
{"uuid":"ad578b1f-d3d6-46ed-8945-787527d1efe0"}
```
At this point your edit will be in the *AwaitingApproval* state and will need to be moved into the *ApprovedForSubmission* state by eeGeo before it can be added to the world.  You can periodically query the status of the edit with the following command (note that you’ll need to replace the UUID shown with the one that was returned by your initial POST request):
```sh
$ curl -v https://indoor-maps-api.eegeo.com/v1/edits/ad578b1f-d3d6-46ed-8945-787527d1efe0/status?token=dev_auth_token
```
Once your edit has been approved, you should see this response from a status query:
```json
{"status":"ApprovedForSubmission"}
```
You will now be able to upload your indoor map for compilation using the following PUT request.  Again, you’ll need to replace the UUID with the one you got in response to your original POST request.
```sh
$ curl -v -XPUT https://indoor-maps-api.eegeo.com/v1/edits/ad578b1f-d3d6-46ed-8945-787527d1efe0?token=dev_auth_token -F comment="my venue comment" -F file="@/path/to/my/file"
```
When your submission has been processed we will send you the details required to view it using the eeGeo SDK.  
If at any time you decide you’d rather delete your edit, you can do so with the following command:
```sh
$ curl -v -XDELETE https://indoor-maps-api.eegeo.com/v1/ad578b1f-d3d6-46ed-8945-787527d1efe0?token=dev_auth_token
```
If you have any problems, the [cheatsheet](CHEATSHEET.md) might be able to help, or feel free to [raise an issue](https://github.com/eegeo/indoor-maps-api/issues/new) or get in touch with us at support@eegeo.com.

---

Troubleshooting
===================
#### <a name="multipolygons"/>Multipolygons
If you are receiving an error related to multipolygons in your submission, it probably refers to one of your units being split into two or more parts. For instance:


[<img src="/images/tutorial/multipolygon_appearance.png">](/images/tutorial/multipolygon_appearance.png) 

If you are struggling to locate the multipolygon in question, you can use QGIS' **Topology Checker** to find it:

1. Open the Topology Checker panel via *Vector > Topology Checker > Topology Checker*

  [<img src="/images/tutorial/topology_checker_location.png">](/images/tutorial/topology_checker_location.png)
2. Click the *Configure* button (Wrench icon)
3. Under *Current Rules* set the layer you want to check and set the rule to *"must not have multi-part geometries"*
4. Click *Add Rule* then click *OK*

  [<img src="/images/tutorial/topology_checker_steps.png">](/images/tutorial/topology_checker_steps.png)
5. Click *Validate All* (check mark) in the Topology Checker panel
6. The offending multipolygon will be displayed in the error list, and will be highlighted in red in QGIS

  [<img src="/images/tutorial/topology_checker_results.png">](/images/tutorial/topology_checker_results.png)

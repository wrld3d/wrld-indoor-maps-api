| WRLD Indoor Map Format |   | WRLD 3D indoor map |
|:-----------:|:-:|:------------:|
|![Indoor map source data](/images/tutorial/overview_coloured.png)|![Right arrow](/images/readme/arrow_right.png)|![Indoor map in app](/images/tutorial/overview.png)|

WRLD Indoor Map REST API v1.0.0
===============================

This repository contains details of the Open [CC-BY-4.0](https://github.com/wrld3d/wrld-indoor-maps-api/blob/master/LICENSE.md) WRLD Indoor Map Format &amp; API. 

---

Quick Start
===========

Get started creating 3D indoor maps with the [TUTORIAL.md](TUTORIAL.md)

* [TUTORIAL.md](TUTORIAL.md) - a worked example showing how to go from a floor plan to a 3D map
* [FORMAT.md](FORMAT.md) - a description of the Open geojson-based WRLD Indoor Map Format
* [Full Indoor Map API Specification](#) - a description of the REST API for submitting maps in the WRLD Indoor Map Format
* [examples/](examples/) - a folder containing example indoor maps in the WRLD Indoor Map Format, including the correct directory structure to use when submitting indoor maps.
* [TROUBLESHOOTING.md](TROUBLESHOOTING.md) - tips for solving common problems


---

Full Indoor Map API Specification
=================================

### Claim the location for which you will upload a new indoor map:

```sh
$ curl -v -XPOST https://indoor-maps-api.wrld3d.com/v1/edits/?token=dev_auth_token -F name="<name>" -F venue_street_address="<address>" -F venue_phone_number="<phone no.>" -F venue_email="<email address>" -F submission_contact_email="<email address for notifications>" -F venue_outline="@/path/to/my/file"
```

This will return a UUID string which can be used to identify the indoor map edit and query its status.

The supplied outline is a [GeoJSON](http://geojson.org/) file containing one or more polygons in the default [Coordinate Reference System](http://geojson.org/geojson-spec.html#coordinate-reference-system-objects).  This can be created via [QGIS](TUTORIAL.md), using online editors such as [GeoJSON.io](http://geojson.io) or with any other GIS software capable of exporting to GeoJSON.  

When you come to upload your indoor map, your edits will be checked against the GeoJSON submitted during this claim request, so please make sure the submitted polygons fully cover the regions you want to edit, but only intersect those buildings which form part of your indoor map.

### Query the status of an indoor map edit:

```sh
$ curl -v https://indoor-maps-api.wrld3d.com/v1/edits/UUID/status?token=dev_auth_token
```

### Upload a file against a new indoor map edit:

```sh
$ curl -v -XPUT https://indoor-maps-api.wrld3d.com/v1/edits/UUID?token=dev_auth_token -F comment="my venue comment" -F file="@/path/to/my/file"
```

This submits the map for processing, so the referenced file should contain the full detail that the user wants to see in the client.

### Query an indoor map edit:

```sh
$ curl -v https://indoor-maps-api.wrld3d.com/v1/edits/UUID?token=dev_auth_token
```

### Query all indoor map edits made by a developer 

```sh
$ curl -v https://indoor-maps-api.wrld3d.com/v1/edits/?token=dev_auth_token
```

### Delete an indoor map edit:

```sh
$ curl -v -XDELETE https://indoor-maps-api.wrld3d.com/v1/edits/UUID?token=dev_auth_token
```

### Update indoor data privacy setting

```sh
curl -v -X PUT https://indoor-maps-api.wrld3d.com/v1/indoor-data/UUID?token=dev_auth_token -d '{"private":true}'
```


### Give an API key access to a private indoors

```sh
curl -v -X POST https://indoor-maps-api.wrld3d.com/v1/api-keys/UUID?token=dev_auth_token -d '{"apikey":"<api_key>"}'
```

### Remove an API key access to a private indoors
```sh
curl -v -X DELETE https://indoor-maps-api.wrld3d.com/v1/api-keys/UUID/<api_key>?token=dev_auth_token
```

### Add custom user data

```sh
curl -v -X PUT https://indoor-maps-api.wrld3d.com/v1/indoor-data/UUID?token=dev_auth_token -d '{"user_data":{"key":"value"}}'
```

---

## License

The WRLD Indoor Map data format is an open format released under Creative Commons Attribution 4.0 International. See the [LICENSE.md](https://github.com/wrld3d/wrld-indoor-maps-api/blob/master/LICENSE.md) file for details.

---

#### Disclaimer
This is a stable, semantically versioned API. 

WRLD may make changes to the API from time to time but will adhere to [Semantic Versioning](http://semver.org/) and provide backward compatible end points for as long as possible.

---

#### Contact us
If you have any problems or queries please [raise an issue](https://github.com/wrld/wrld-indoor-map-api/issues/new).

# eegeo/indoor-maps-api
Example REST calls against the indoor map service API.

### Request permission to upload a new indoor map:

```sh
$ curl -v -XPOST https://indoor-maps-api.eegeo.com/v1/edits/?token=dev_auth_token -F name="<name>" -F venue_street_address="<address>" -F venue_phone_number="<phone no.>" -F venue_email="<email address>" -F submission_contact_email="<email address for notifications>" -F venue_outline="@/path/to/my/file"
```

This will return a UUID string which can be used to identify the indoor map edit and query its status.  It will start out with a status of AwaitingApproval and switch to ApprovedForSubmission once the developer's right to edit the venue has been approved.

The supplied outline is a [GeoJSON](http://geojson.org/) file containing one or more polygons in the default [Coordinate Reference System](http://geojson.org/geojson-spec.html#coordinate-reference-system-objects).  This can be created via [QGIS](TUTORIAL.md), using online editors such as [GeoJSON.io](http://geojson.io) or with any other GIS software capable of exporting to GeoJSON.  Later edits will be checked against the areas indicated by these polys, so please make sure they fully cover the regions you want to edit but only intersect those buildings which form part of the indoor map you are submitting.

### Query the status of an indoor map edit:

```sh
$ curl -v https://indoor-maps-api.eegeo.com/v1/edits/UUID/status?token=dev_auth_token
```

### Upload a file against a new indoor map edit (will only work once the edit's status has been set to ApprovedForSubmission):

```sh
$ curl -v -XPUT https://indoor-maps-api.eegeo.com/v1/edits/UUID?token=dev_auth_token -F comment="my venue comment" -F file="@/path/to/my/file"
```

This submits the map for processing, so the referenced file should contain the full detail that the user wants to see in the client.

### Query an indoor map edit:

```sh
$ curl -v https://indoor-maps-api.eegeo.com/v1/edits/UUID?token=dev_auth_token
```

### Query all indoor map edits made by a developer 

```sh
$ curl -v https://indoor-maps-api.eegeo.com/v1/edits/?token=dev_auth_token
```

### Delete an indoor map edit:

```sh
$ curl -v -XDELETE https://indoor-maps-api.eegeo.com/v1/edits/UUID?token=dev_auth_token
```


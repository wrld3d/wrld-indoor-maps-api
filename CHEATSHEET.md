# eegeo/venue-edit-service
Example REST calls against venue-edit-service

### Request the right to upload a new Venue Edit:

```sh
$ curl -v -XPOST https://venue-edit-service.eegeo.com/venue-edits/?token=dev_auth_token -F venue_street_address="<address>" -F venue_phone_number="<phone no.>" -F venue_email="<email address>" -F venue_outline="@/path/to/my/file"
```

This will return a UUID string which can be used to identify the venue and query its status.  It will start out with a status of AwaitingApproval and switch to ApprovedForSubmission once the developer's right to edit the venue has been approved.  

The supplied outline file is a geojson file in Eegeo's venue format.  The venue file does not need to be fully specified at this point, but should at least provide the outline of the plot for the venue that the user intends to claim.

### Query the status of a Venue Edit:

```sh
$ curl -v https://venue-edit-service.eegeo.com/venue-edits/UUID/status?token=dev_auth_token
```

### Upload a file against a new Venue Edit (will only work once the venue's status has been set to ApprovedForSubmission):

```sh
$ curl -v -XPUT https://venue-edit-service.eegeo.com/UUID?token=dev_auth_token -F name="my venue name" -F comment="my venue comment" -F file="@/path/to/my/file"
```

This submits the venue for processing, so the referenced file should contain the full detail that the user wants to see in the client.

### Query a Venue Edit:

```sh
$ curl -v https://venue-edit-service.eegeo.com/venue-edits/UUID?token=dev_auth_token
```

### Query all venue edits made by a developer 

```sh
$ curl -v https://venue-edit-service.eegeo.com/venue-edits/?token=dev_auth_token
```

### Delete a venue edit:

```sh
$ curl -v -XDELETE https://venue-edit-service.eegeo.com/venue-edits/UUID?token=dev_auth_token
```


# Summary

This document is intended as a summary for Centrify engineers who want a high level summary of the API guidelines before reading into the details.

It also highlights how these guidelines are different from Centrify's traditional RPC based APIs.

## Differences from Centrify's traditional APIs

### Verbs

This standard embraces standard HTTP verbs which Centrify historically did not use, specifically:

* PATCH (see section 7.4.2)
* DELETE
* GET

### Url format

This standard embraces traditional/standard REST url formation.

See: 7.1, 9.3

### Error format

This standard embraces an industry standard error format, namely:
RFC7807

### Response bodies

This standard recommends a standard response body shape which is common across top level collections.

### Links

This standard embraces links as a means to provide clients with related operations, and specifically with if the authorized user MAY use that relation operation or not.

### Naming

This standard provides guidance on naming to ensure a good developer experience.

### Collections

This standard provides guidance on collections which are free from Redrock's influence.

### Embed

This standard recommends supporting the embed query parameter to allow clients to make fewer requests.

### Include

This standard recommends supporting the include query parameter to allow the client some flexibility over network bandwidth.

### Functions

This standard recommends deviating from typical REST patterns to send "messages" to the server for operations which do not neatly map to CRUD.
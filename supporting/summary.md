# Summary

This document is intended as a summary for Centrify engineers who want a high level summary of the API guidelines before reading into the details (if ever)

It also highlights how these guidelines are different from Centrify's traditional RPC based APIs.

## REST API Cheat Sheet

Centrify services *should* follow this cheat sheet to provide developers with a consistent experience.

Centrify service *may* deviate from the cheat sheet for exceptional use cases.


|     Action                   | HTTP Method |       Parameters       |               Target path                    |               Meaning                             |            Example path               |            Response                                                  |  Example existings APIs                                        |
|      ---                     |     ---     |          ---           |                  ---                         |                 ---                               |                ---                    |               ---                                                    |  ---                                                           |
| Create, add to               |    POST     |         Body           | /$resourceList                               | Add a new resource to a list                      | /accounts                             | LocationHeader `\|` DenseResourceRepresentation `\|` Error           |  ServerManage/AddDatabase, ServerManage/AddDomain              |
| Create, add to               |    POST     |         Body           | /$resourceList/$nameOrId/$relatedList        | Add a new resource to a resource's list           | /accounts/db2-root/credentials        | LocationHeader `\|` DenseResourceRepresentation `\|` Error           |  ServerManage/AddAccount, Aws/AddAccessKey                     |
| Read, search...              |    GET      |         Query          | /$resourceList                               | Get a list of resources                           | /accounts                             | ListOfSparseResources `\|` Error                                     |  CloudProvider/GetAllCloudProviders                            |
| Read, search...              |    GET      |         Query          | /$resourceList/$nameOrId/$relatedList        | Get a list of related resources                   | /accounts/db2-root/credentials        | ListOfSparseResources `\|` Error                                     |  CloudProvider/GetCloudProviderCollectionPermissions           |
| Read, search...              |    GET      |         Query          | /$resourceList/$nameOrId                     | Get a single resource                             | /accounts/db2-root                    | DenseResourceRepresentation `\|` Error                               |  CloudProvider/GetCloudProvider                                |
| Modify partially             |    PATCH    |         Body           | /$resourceList/$nameOrId                     | Partially modify a single resource                | /accounts/db2-root                    | LocationHeader `\|` DenseResourceRepresentation `\|` Error           |  Roles/UpdateRole                                              |
| Replace                      |    PUT      |         Body           | /$resourceList/$nameOrId                     | Fully modify a single resource                    | /accounts/db2-root                    | LocationHeader `\|` DenseResourceRepresentation `\|` Error           |  ServerManage/UpdateAccount, ServerManage/UpdateServer         |
| Remove, delete               |    DELETE   |         Query          | /$resourceList/$nameOrId                     | Delete a single resource from a list              | /accounts/db2-root                    | OperationResult `\|` Error                                           |  Aws/DeleteAccessKey, CloudProvider/DeleteCloudProvider        |
| Remove, delete               |    DELETE   |         Query          | /$resourceList/$nameOrId>/$relatedList       | Delete a single resource from a resource list     | /accounts/db2-root/credentials        | OperationResult `\|` Error                                           |  Aws/DeleteAccessKey, Aws/DeleteAccessKey                      |
| $complexNotCrudOperation     |    POST     |      Body, Query       | /$function                                   | Initiate a standalone complex operation           | /lock                                 | OperationResult `\|` Error                                           |  Core/GeneratePassword                                         |
| $complexNotCrudOperation     |    POST     |      Body, Query       | /$resourceList/$function                     | Initiate a complex operation on a list            | /accounts                             | OperationResult `\|` Error                                           |  Aws/VerifyAccessKey                                           |
| $complexNotCrudOperation     |    POST     |      Body, Query       | /$resourceList/$nameOrId/$function           | Initiate a complex operation on a single resource | /accounts/db2-root/manage             | OperationResult `\|` Error                                           |  Aws/VerifyAccessKeyForUserAccount                             |
| $alias                       |    `\*`     |         `\*`           | `\*`                                         | A shorthand path for the consumer                 | /me                                   | `\*`                                                                 |  UserMgmt/GetUserInfo                                          |

### Cheat sheet definitions

#### Actions

| Action                       | Part of speech    | Plurality | Notes                                                                                                                                                                                  |
| ---                          | ---               | ---       | ---                                                                                                                                                                                    |
| $complexNotCrudOperation     | N/A               | N/A       | Some operation in our system which does not neatly map to CRUD. Also useful for "updates" with wide reaching side effects, and complex requirements around interdependent properties   |
| $alias                       | `\*`              | `\*`      | Undefined, for instance, GET /accounts/db2-root/credentials/the-password may be simpler aliased to GET /accounts/db2-root/password, where it returns the first password, if one exists |

#### Paths

| Action                       | Part of speech    | Plurality | Notes                                                                                                                                                                                  |
| ---                          | ---               | ---       | ---                                                                                                                                                                                    |
| $relatedList                 | Noun              | Plural    | Ends in s, unless the plural form of the noun does not, example: people                                                                                                                |
| $resourceList                | Noun              | Plural    | Ends in s, unless the plural form of the noun does not, example: people                                                                                                                |
| $nameOrId                    | N/A               | N/A       | Undefined, for instance, GET /accounts/db2-root/credentials/the-password may be simpler aliased to GET /accounts/db2-root/password, where it returns the first password, if one exists |
| $function                    | Verb              | N/A       | An industry standard verb, or verb that intuitively maps to what the user wants to do. Present tense.                                                                                  |

#### Response

| Action                       | Summary                                                                                                                                                                                  |
| ---                          | ---                                                                                                                                                                                      |
| LocationHeader               | An HTTP header providing the URL of the new resource instead of having a full DenseResourceRepresentation.  Saves bandwidth/processing but slightly harder to use for clients.           |
| DenseResourceRepresentation  | A "full" representation of the resource, which optionally can be controlled by client using "include" and "embed" query parameters                                                       |
| ListOfSparseResources        | A "sparse" representation, tuned for performance and common use cases.                                                                                                                   |
| OperationResult              | A standard operation result format defined below.                                                                                                                                        |
| Error                        | A RFC7807 compliant error response                                                                                                                                                       |

### Common patterns

Patterns for "global" functionality which is common to many resources, or accepts the input of a CRN.

#### List Representation

Centrify specifies a common list of resources representation so clients can make some common decisions, regardless of the type of resource.

#### Sparse resource representation

Centrify specifies a common sparse resource representation so clients can make some common decisions, regardless of the type of resource.

#### Dense resource representation

Centrify specifies a common dense resource representation so clients can make some common decisions, regardless of the type of resource.

#### Effective Policy

Centrify specific a common pattern for reading the "effective policy" as it applies to a resource, list of resources, or principal.

#### Effective Permissions

Centrify specifies a common pattern for reading the "effective permission" of the currently authenticated user as it applies to a resource or list of resources.

#### Reading assigned ACLs

Centrify specifies a common pattern for reading assigned ACLs to a resource.

#### Writing ACLs related to a resource

Centrify specifies a common pattern for reading assigned ACLs to a resource.

#### Writing ACLs related to a list

Centrify specifies a common pattern for reading assigned ACLs to a resource.

#### Workflow


#### Activity


## Differences from Centrify's traditional APIs

### Verbs

This standard embraces standard HTTP verbs which Centrify historically did not use, specifically:

* PATCH (see section 7.4.2, 7.4.2.1)
* DELETE
* GET (for most READ operations)

### Url format

This standard embraces traditional/standard REST url format.

See: 7.1, 9.3

### Error format

This standard embraces an industry standard error format, namely:
RFC7807

### Response bodies

This standard recommends a standard response body shape which is common across top level collections.

### Links

This standard recommends links as a means to provide clients with related operations, and specifically for Centrify, to indicate if the authorized user MAY use that relation operation or not.

This reduces the complexity for the client and removes many APIs we historically built which do the same thing.

### Naming

This standard provides guidance on naming to ensure a good developer experience.

### Collections

This standard provides guidance on collections which are familiar to API users.

### Embed

This standard recommends supporting the embed query parameter to allow clients to make fewer requests.

### Include

This standard recommends supporting the include query parameter to allow the client some flexibility over network bandwidth.

### Functions

This standard recommends deviating from typical REST patterns to send "messages" to the server for operations which do not neatly map to CRUD.
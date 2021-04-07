# Summary

This document is intended as a summary for Centrify engineers who want quick overview of the API guidelines before reading into the details (if ever)

It also highlights how these guidelines are different from Centrify's traditional RPC based APIs.

## REST API Cheat Sheet

Centrify services *should* follow this cheat sheet to provide developers with a consistent experience.

Centrify service *may* deviate from the cheat sheet for exceptional use cases.

### Single resource

|     Action                   | HTTP Method |       Parameters       |               Target path                    |               Meaning                             |            Example path               |            Response                                                  |  Example existings APIs                                        |
|      ---                     |     ---     |          ---           |                  ---                         |                 ---                               |                ---                    |               ---                                                    |  ---                                                           |
| Create, add to               |    POST     |         Body           | /$resourceList                               | Add a new resource                                | /accounts                             | LocationHeader `|` DenseResource `|` Error                           |  ServerManage/AddDatabase, ServerManage/AddDomain              |
| Read, search...              |    GET      |         Query          | /$resourceList/$nameOrId                     | Get a single resource                             | /accounts/db2-root                    | DenseResource `|` Error                                              |  CloudProvider/GetCloudProvider                                |
| Modify partially             |    PATCH    |         Body           | /$resourceList/$nameOrId                     | Partially modify a single resource                | /accounts/db2-root                    | LocationHeader `|` DenseResource `|` Error                           |  Roles/UpdateRole                                              |
| Replace                      |    PUT      |         Body           | /$resourceList/$nameOrId                     | Fully modify a single resource                    | /accounts/db2-root                    | LocationHeader `|` DenseResource `|` Error                           |  ServerManage/UpdateAccount, ServerManage/UpdateServer         |
| Remove, delete               |    DELETE   |         Query          | /$resourceList/$nameOrId                     | Delete a single resource                          | /accounts/db2-root                    | OperationResult `|` Error                                            |  Aws/DeleteAccessKey, CloudProvider/DeleteCloudProvider        |
| $complexNotCrudOperation     |    POST     |      Body, Query       | /$resourceList/$nameOrId/$function           | Initiate a complex operation on a single resource | /accounts/db2-root/manage             | OperationResult `|` Error                                            |  Aws/VerifyAccessKeyForUserAccount                             |

### Lists

|     Action                   | HTTP Method |       Parameters       |               Target path                    |               Meaning                             |            Example path               |            Response                                                  |  Example existings APIs                                        |
|      ---                     |     ---     |          ---           |                  ---                         |                 ---                               |                ---                    |               ---                                                    |  ---                                                           |
| Create, add to               |    POST     |         Body           | /$resourceList/$nameOrId/$relatedList        | Add a new resource to a resource's list           | /accounts/db2-root/events             | LocationHeader `|` DenseResource `|` Error                           |  ServerManage/AddAccount, Aws/AddAccessKey                     |
| Read, search...              |    GET      |         Query          | /$resourceList                               | Get a list of resources                           | /accounts                             | List`<`SparseResource`>` `|` Error                                   |  CloudProvider/GetAllCloudProviders                            |
| Read, search...              |    GET      |         Query          | /$resourceList/$nameOrId/$relatedList        | Get a list of related resources                   | /accounts/db2-root/events             | List`<`SparseResource`>` `|` Error                                   |  CloudProvider/GetCloudProviderCollectionPermissions           |
| Remove, delete               |    DELETE   |         Query          | /$resourceList/$nameOrId/$relatedList        | Delete a single resource from a resource list     | /accounts/db2-root/events             | OperationResult `|` Error                                            |  Aws/DeleteAccessKey, Aws/DeleteAccessKey                      |
| $complexNotCrudOperation     |    POST     |      Body, Query       | /$resourceList/$function                     | Initiate a complex operation on a list            | /accounts/rotate                      | OperationResult `|` Error                                            |  Aws/VerifyAccessKey                                           |

### Singletons

|     Action                   | HTTP Method |       Parameters       |               Target path                    |               Meaning                             |            Example path               |            Response                                                  |  Example existings APIs                                        |
|      ---                     |     ---     |          ---           |                  ---                         |                 ---                               |                ---                    |               ---                                                    |  ---                                                           |
| $complexNotCrudOperation     |    POST     |      Body, Query       | /$function                                   | Initiate a standalone complex operation           | /lock                                 | OperationResult `|` Error                                            |  Core/GeneratePassword                                         |

### Aliasing

|     Action                   | HTTP Method |       Parameters       |               Target path                    |               Meaning                             |            Example path               |            Response                                                  |  Example existings APIs                                        |
|      ---                     |     ---     |          ---           |                  ---                         |                 ---                               |                ---                    |               ---                                                    |  ---                                                           |
| $alias                       |    `\*`     |         `\*`           | `\*`                                         | A shorthand path for the consumer                 | /me, /api                             | `\*`                                                                 |  UserMgmt/GetUserInfo                                          |

### Definitions

#### Actions

| Action                       | Part of speech    | Plurality | Notes                                                                                                                                                                                  |
| ---                          | ---               | ---       | ---                                                                                                                                                                                    |
| $complexNotCrudOperation     | N/A               | N/A       | Some operation in our system which does not neatly map to CRUD. Also useful for "updates" with wide reaching side effects, and complex requirements around interdependent properties   |
| $alias                       | `\*`              | `\*`      | Undefined, for instance, GET /accounts/db2-root/credentials/the-password may be simpler aliased to GET /accounts/db2-root/password, where it returns the first password, if one exists |

#### Paths

| Action                       | Part of speech    | Plurality | Notes                                                                                                                                                                                  |
| ---                          | ---               | ---       | ---                                                                                                                                                                                    |
| $relatedList                 | Noun              | Plural    | Ends in s, unless the plural form of the noun does not, examples: people, accounts                                                                                                     |
| $resourceList                | Noun              | Plural    | Ends in s, unless the plural form of the noun does not, example: people, accounts                                                                                                      |
| $nameOrId                    | N/A               | N/A       | Undefined, for instance, GET /accounts/db2-root/credentials/the-password may be simpler aliased to GET /accounts/db2-root/password, where it returns the first password, if one exists |
| $function                    | Verb              | N/A       | An industry standard verb, or verb that intuitively maps to what the user wants to do. Present tense.                                                                                  |

#### Response

| Action                       | Summary                                                                                                                                                                                  |
| ---                          | ---                                                                                                                                                                                      |
| LocationHeader               | An HTTP header providing the URL of the new resource instead of having a full DenseResourceRepresentation.  Saves bandwidth/processing but slightly harder to use for clients.           |
| DenseResourceRepresentation  | A "full" representation of the resource, which optionally can be controlled by client using "include" and "embed" query parameters                                                       |
| List                         | A common list representation which contains sparse resources                                                                                                                             |
| OperationResult              | A standard operation result format defined below.                                                                                                                                        |
| Error                        | A RFC7807 compliant error response                                                                                                                                                       |

### Common patterns

Patterns for "global" functionality which is common to many resources, or accepts the input of a CRN.

#### List Representation

Centrify specifies a common list of resources representation so clients can make some common decisions, regardless of the type of resource.

This is specified in our shared open API specification repository.

Note the response does *not* show the full count of objects, and returns *server* controlled urls.

For example:

```text

GET /accounts?limit=75&orderBy=name%20desc HTTP/1.1

{
    object: 'account',
    next_url: 'https://uf142.my-dev.centrify.net/accounts?cursor=45432342xdfxcvsd234',
    previous_url: 'https://uf142.my-dev.centrify.net/accounts?cursor=zxc2342xdfxcvsd234',
    items: [{
        id: 'b47cd113-6cb9-47e2-93ec-85115680ae4e',
        name: 'db2:root',
        crn: 'centrify:accounts:db2:root',
        created: '2017-09-05T17:47:53.767Z',
        modified: '2017-09-05T17:49:53.767Z',
        authorityName: 'db2',
        authorityId: 'sdf-sdf-sdfdsf-sdfsdf',
        authorityType: 'Windows'
    }, ...]
}

```

#### SparseResource

A sparse resource is a flat, minimal representation of a resource, optimized for performance, while still providing enough information to help a consumer/user disambiguate between items in the list.

It should contain some human readable information, and the ability to retrieve a dense resource representation.

#### DenseResource

A dense resource representation is not flat. The representation may contain read only properties, and writable properties. The writable properties should mirror the request body representation for POST and PATCH operations.

The dense representation *must* contain a meta object which contains at least: id, crn, name, created, modified

This allows clients to perform some common reasoning about any resource returned by Centrify's APIs.

For example:

```text
GET /accounts/db2-root?embed=workflow,authority,credentials,links,excludedLinks,capabilities,checkouts HTTP/1.1

{
    checkoutDurationSeconds: 60,
    credentialType: 'password',
    meta: {
        id: 'b47cd113-6cb9-47e2-93ec-85115680ae4e',
        name: 'db2:root',
        object: 'account',
        crn: 'centrify:accounts:db2:root',
        created: '2017-09-05T17:47:53.767Z',
        modified: '2017-09-05T17:49:53.767Z'
    },
    embedded: {
        credentials: [{
            type: 'password',
            managed: true,
            lastRotated: '2017-09-05T17:49:53.767Z'
        }],
        authority: {
            id: 'b47cd113-6cb9-47e2-93ec-85115680ae4e',
            name: 'db2',
            crn: 'centrify:systems:db2',
            created: '2017-09-05T17:47:53.767Z',
            modified: '2017-09-05T17:49:53.767Z',
            ...
        },
        workflow: {
            enabled: false
        },
        capabilities: {
            domain: true,
            domainAccountReconciliation: true
        },
        checkouts: null,
        links: [{
            "href": "db2-root/rotate",
            "rel": "rotate",
            "type": "POST"
        }, {
            "href": "db2-root/manage",
            "rel": "manage",
            "type": "POST"
        }, {
            "href": "db2-root/checkout",
            "rel": "checkout",
            "type": "GET"
        }, {
            "href": "db2-root/session?type=rdp...",
            "rel": "session",
            "type": "GET"
        }],
        excludedLinks: [{
            "href": "db2-root",
            "rel": "self",
            "type": "PATCH",
            "reason": "Insufficient administrative rights."
        }, {
            "href": "db2-root/checkouts",
            "rel": "self",
            "type": "GET",
            "reason": "Insufficient administrative rights."
        }]
    }
}
```

#### Activity

Centrify specifies a common related list to resources which generate activity.

This list is left simple and spare on purpose.

A report can be written against Events if desired.

```test
GET /accounts/db2-root/activities

{
    [{
        type: 'login',
        message: 'User a@a.com logged into root account on system db2',
        userId: 'b48cd113-6cb9-47e2-93ec-85115680ae4e',
        userName: 'a@a.com',
        objects: [{
            id: 'b47cd113-6cb9-47e2-93ec-85115680ae4e',
            name: 'db2',
            object: 'system',
            crn: 'centrify:systems:db2'
        }, {
            id: 'b47cd113-6cb9-47e2-93ec-85115680ae4e',
            name: 'db2:root',
            object: 'account',
            crn: 'centrify:accounts:db2:root'
        }]
    }, {

    }, {

    }]
}
```

### Include query parameter

Clients may want control over how much data is included in a Sparse or DenseRepresentation.

The include query parameter may be implemented as a way to give the client that flexibility.

```text
GET /accounts?include=id,name

{
    object: 'account',
    next_url: 'https://uf142.my-dev.centrify.net/accounts?cursor=45432342xdfxcvsd234',
    previous_url: 'https://uf142.my-dev.centrify.net/accounts?cursor=zxc2342xdfxcvsd234',
    items: [{
        id: 'b47cd113-6cb9-47e2-93ec-85115680ae4e',
        name: 'db2'
    }, {
        id: 'b47cd113-6cb9-47e2-93ec-85115680ae4e',
        name: 'db2'
    }, ...]
}

### Embed query parameter

This standard recommends supporting the embed query parameter to allow consumers to decrease # of round trips to get information related to a resource.

Note each "embed" directly maps to a url, for example:

/accounts/db2-root/workflowsettings
/accounts/db2-root/authority
/accounts/db2-root/credentials
/accounts/db2-root/links

For example:

```text
GET /accounts/db2-root?embed=workflowsettings,authority,credentials,links,excludedLinks,capabilities,checkouts HTTP/1.1

{
    checkoutDurationSeconds: 60,
    credentialType: 'password',
    meta: {
        id: 'b47cd113-6cb9-47e2-93ec-85115680ae4e',
        name: 'db2:root',
        object: 'account',
        crn: 'centrify:accounts:db2:root',
        created: '2017-09-05T17:47:53.767Z',
        modified: '2017-09-05T17:49:53.767Z'
    },
    embedded: {
        credentials: [{
            type: 'password',
            managed: true,
            lastRotated: '2017-09-05T17:49:53.767Z'
        }],
        authority: {
            id: 'b47cd113-6cb9-47e2-93ec-85115680ae4e',
            name: 'db2',
            crn: 'centrify:systems:db2',
            created: '2017-09-05T17:47:53.767Z',
            modified: '2017-09-05T17:49:53.767Z',
            ...
        },
        workflowSettings: {
            enabled: false
        },
        capabilities: {
            domain: true,
            domainAccountReconciliation: true
        },
        checkouts: null,
        links: [{
            "href": "db2-root/rotate",
            "rel": "rotate",
            "type": "POST"
        }, {
            "href": "db2-root/manage",
            "rel": "manage",
            "type": "POST"
        }, {
            "href": "db2-root/checkout",
            "rel": "checkout",
            "type": "GET"
        }, {
            "href": "db2-root/session?type=rdp...",
            "rel": "session",
            "type": "GET"
        }],
        excludedLinks: [{
            "href": "db2-root",
            "rel": "self",
            "type": "PATCH",
            "reason": "Insufficient administrative rights."
        }, {
            "href": "db2-root/checkouts",
            "rel": "self",
            "type": "GET",
            "reason": "Insufficient administrative rights."
        }]
    }
}
```

### Links / Excluded links

This standard recommends links & excluded links as a means to provide clients with related operations, and specifically for Centrify, to relieve the client from having to understand ACLs, Tasks, Licenses + more by simply including a link or not.

This reduces the complexity for the user and removes many APIs we historically built so the user can figure out "can I do x?"

Why will this be a good thing for everyone?

* It will stop our anti-pattern of coding business logic twice, once in the back-end and once in the ui.
  * How so? Think about it. An API to do X, must already be secure, i.e. it must check things like ACLs (Table/TableRow/Task), Policies, Global settings, Business logic rules etc..
    * Our UI, when it's trying to not "surprise" the customer, will do its best to determine if an API operation will definitely fail, and hide it from the user. To do that today, most of the time the UI invokes N APIs, re-codes the same business logic as the back-end and decides whether to show the UI that leads to an API operation.
* It will make our system more testable. We can easily test the business logic of "is this operation enabled/allowed for the authenticated user". We can negative/positive test it easily.
* It will decrease bugs encountered from coding the same thing twice
* It will make it easier to code our UI
  * UI code can be more concerned about providing customers with a good UI, and not understanding all business logic.

To make sure you fully understand the problem, here is an example of what our UI does today to determine what API operations are allowed for the currently authenticated user on a single account, which has a system authority type:

1. First it makes sure it has a lookup dictionary which states for every system type in Centrify's Vault, what are that system types capabilities? Does it support account reconciliation etc...
2. It gets the account's systems type
3. It then uses the lookup dictionary + type to initialize 16 boolean variables, some of which are logical operations on previous variables.
4. It then performs a bunch of conditional logic per operation that *might* be applicable to the account in question:
   1. Manage - 3 expressions
   2. Login - 6 expressions
   3. Request login - 7 expressions
   4. Checkout/View/Checkin/Request - 8 expressions
   5. Reset password - 3 expressions
   6. Rotate password - 7 expressions
   7. Unlock - 2 expressions
   8. Health Check - 3 expressions
   9. Set as admin - 6 expressions
5. When performing that logic, the UI is exposed to: ACLs, "Authority capabilities", "Authority Type", Tenant Configuration and Licenses.

Here's a list of APIs that were developed explicitely to tell our UI, or the API consumer, whether it will fail or not.
"/Acl/CheckRowRight",
"/Acl/GetEffectiveDirRights",
"/Acl/GetEffectiveFileRights",
"/Acl/GetEffectiveRowRights",
"/Security/TaskCheck",
"/Security/TaskChecks",
"/ServerManage/CanDeleteSshKey"

So if providing "links" / "excludedLinks" in a dense representation can remove all of this knowledge, that feels like a good thing.

#### Effective Policy

Centrify specific a common pattern for reading the "effective policy" as it applies to a resource, list of resources, or principal.

TBD

#### Effective Permissions

Centrify specifies a common pattern for reading the "effective permission" of the currently authenticated user as it applies to a resource or list of resources.

Encoding this knowledge in links may be enough.

TBD

#### Reading assigned ACLs

Centrify specifies a common pattern for reading assigned ACLs to a resource.

TBD

#### Writing ACLs related to a resource

Centrify specifies a common pattern for reading assigned ACLs to a resource.

TBD

#### Writing ACLs related to a list

Centrify specifies a common pattern for reading assigned ACLs to a resource.

TBD

#### Workflow

TBD


## Differences from Centrify's traditional APIs

### Verbs

This standard embraces standard HTTP verbs which Centrify historically did not use, specifically:

* PATCH (see section 7.4.2, 7.4.2.1)
* DELETE
* GET (for most READ operations)
* PUT (although not recommended, PATCH preferred with a unique string attribute "name" which provides PATCH idempotent creates if desired)

### Url format

This standard embraces traditional/standard REST url format.

See: 7.1, 9.3, cheat sheet.

### Error format

This standard embraces an industry standard error format, namely:
RFC7807

### Response bodies

This standard recommends a standard response body shape which is common across top level collections.

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
# Advice



## Good Advice from the Azure services team

* Think about naming from the context of a **developer experience**.
  * Start with the "things" your API manipulates, then think about the operations that a developer needs to do to these "things".
  * Keep the verbs present-tense.  Avoid the use of past or future tense in most cases.
  * Avoid the use of generic names like "Object", "Job", "Task", "Operation" (for example - the list is not exhaustive).
  * What happens to the names when the focus of the service expands?  It may be worth starting with a less generic name to avoid a breaking change later on.
* Think about the code that a customer will write both before and after the REST API call.  How will a developer use this API in the canonical use case?
  * Consider multiple languages, and include at least one dynamically typed language (for example, Python or JavaScript) and one statically typed language (for example, Java or C#).
* Use previews to get the shape right.  There are different notification, breaking change, and lifetime requirements on preview API versions.
  * We recommend a minimum of 2 preview versions prior to your first GA release.  However, there is no hard rule for previews.
  * You should gather feedback from your customers and iterate until the API is useful.  Actively solicit feedback from your preview customers.

Preventing future breaking changes is a source of concern during initial review.  Without a history, the review process attempts to identify patterns that may result in breaking changes later on.  For instance, think about the following:

* [Think about idempotency](https://stripe.com/blog/idempotency).  In a distributed cloud, each HTTP call must be idempotent.  You must be resilient in the face of failure.  A developer must rely on idempotency to build fault-tolerant systems.
  * The HTTP specification requires that GET, PUT, DELETE, and HEAD be idempotent.
  * Prefer allowing the developer to use PUT or PATCH to create a resource with a user-specified name or ID.  A developer will commonly want to download a specific resource by name or ID.  This provides the developer with an idempotent mechanism for creating resources.
* Collections are a common source of review comments:
  * Return collections with server-side paging, even if your resource does not currently need paging.  This avoids a breaking change when your service expands.
  * A collection should return a homogeneous collection (single type).  Do not return heterogeneous collections unless there is a really good reason to do so.  If you feel heterogeneous collections are required, discuss the requirement with an API reviewer prior to implementation.
  * Think about how the developer can reason about the collection organization.  Filtering is a common customer request.
* Avoid polymorphism.  An endpoint should work with a single type to avoid problems during SDK creation.  Remember that a change to the model is a breaking change.
* Use extensible enumerations unless you are completely sure that the enumeration will never expand.  Extensible enumerations are modeled as strings - expanding an extensible enumeration is not a breaking change.
* Implement [conditional requests](https://tools.ietf.org/html/rfc7232) early.  This allows you to support concurrency, which tends to be a concern later on.
* If your API specifies access conditions to another resource:
  * Think about how to represent that model polymorphically.  For example, you may be using a SQL Azure connection now, but extend to Cosmos DB, Azure Data Lake, or Redis Cache later on.  Think about how you can specify that resource in a non-breaking manner.
  * Implement managed identity access controls for accessing the other resource.  Do not accept connection strings as a method of specifying access permissions.
* Be concerned about data widths of numeric types.  Wider data types (e.g. 64-bit vs. 32-bit) are more future-proof.
* Think about how the interface will be represented by an SDK.  For example, JavaScript can only support numbers up to 2<sup>53</sup>, so relying on the full width of a 64-bit number should be avoided.
* Implement (and encourage) the use of PATCH for resource modifications.
  * The PATCH operation should be able to modify any mutable property on the resource.
  * Prefer JSON merge-patch ([RFC 7396](https://tools.ietf.org/html/rfc7396)) over JSON patch ([RFC 6902](https://tools.ietf.org/html/rfc6902)) as the accepted data format for PATCH operations.
* Get a security review of your API.  You should be especially concerned with PII leakage, GDPR compliance and any other compliance regulations appropriate to your situation.

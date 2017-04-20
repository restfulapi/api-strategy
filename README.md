
# RESTful API Strategy

<p/><p/>

## TL;DR

>This document provides guidance regarding the development of **non-trivial resource-oriented and RESTful HTTP APIs.**

----

_Disclaimer: This strategy document is **not** an invention, but only represents a gathering of best practices and insights found across the web that we have adopted. Numerous blogs and papers have provided the foundation for the content found in this strategy. The recommendations in this strategy are simply 'recommendations'._

----

## Background

#### What is an API?

>An Application Programming Interface (API) is a specification intended to be used as an interface by software components to communicate with each other.  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Wikipedia](http://en.wikipedia.org/wiki/Application_programming_interface)

#### What is REST?

>REST is a set of principles that define how Web standards, such as HTTP and URIs, are supposed to be used (which often differs quite a bit from what many people actually do).[1](http://www.infoq.com/articles/rest-introduction)

REST stands for REpresentational State Transfer, and was first described in chapter 5 of [Roy Fielding's Ph.D dissertation](http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm).  There are many resources for learning about REST. For those who are not yet familiar with REST principles, you may want to review the [REST API Tutorial](http://www.restapitutorial.com).

APIs that adopt REST principles are 'stateless', 'resource-orientated', 'expose a uniform interface', and use 'hypermedia as the engine of application state' (HATEOAS). It is this last item which distinguishes a RESTful API from an HTTP based API. Roy Fielding has stated:

>'[hypermedia-driven application state](http://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven)' is a 'requirement' of REST.

_For those not familiar with HATEOAS, a nice overview was authored by Jim Webber: "[How to GET a Cup of Coffee](http://www.infoq.com/articles/webber-rest-workflow)".  Essentially, HATEOAS exposes the state machine of an application such that the client interacting with the application is provided with appropriate options at each step within a workflow.  HATEOAS is not often supported by APIs stated as being RESTful, however per Roy Fielding it is a requirement._

This API strategy references both 'proper' HTTP and 'RESTful' APIs, the latter employing hypermedia. When this strategy discusses (proper) HTTP APIs, it is referring to using HTTP correctly and fully but not being hypermedia-based.

Expanding a bit upon the above, we can state HTTP APIs:

* Expose 'Resources' to represent important concepts and objects
* Ensure each resource is uniquely 'addressable' via a URI, so that clients may interact with them over HTTP
* Provide one or more 'representations' (e.g., using JSON and or XML) of those resources
* Provide a consistent, standard interface based upon the standard HTTP methods
* Ensure interaction with the API is stateless in nature, in order to provide flexibility in deployment as well as to promote scalability

In addition to the above, RESTful APIs:

* Use hypermedia as the engine of application state

While RESTful APIs provide important benefits to the clients of the APIs (as outlined above), REST principles may also be used to guide internal application architecture and design (referred to as [Resource-Oriented Architecture](http://en.wikipedia.org/wiki/Resource-oriented_architecture)).

>Thinking in terms of resources can promote elegant and simple solutions.

Martin Fowler authored a very nice overview of the [Richardson Maturity Model](http://martinfowler.com/articles/richardsonMaturityModel.html) (RMM) used for gauging an API's 'level of RESTfulness'.  This seems to be the most popular maturity model for REST (and includes what we call HTTP APIs here).

**A note about demand-driven APIs**

This strategy document is specific to resource-oriented APIs and does not address API alternatives such as '[GraphQL](http://graphql.org/)' and '[Falcor](https://netflix.github.io/falcor/)' that use the HTTP protocol but are not resource-oriented and use a small subset of HTTP methods. _(See the [Demand Driven Architecture](#demand-driven) section in this document for a brief discussion.)_

Regardless, these newer 'demand-driven' approaches address some of the shortcomings/challenges of a resource-oriented approach.

>[GraphQL](http://graphql.org/) is highly recommended and warrants consideration.

**A note about Service Oriented Architecture (SOA)**

_This strategy document will not reference '[Service Oriented Architecture](http://en.wikipedia.org/wiki/Service-oriented_architecture)' (SOA) terminology, as the term 'SOA' is very overloaded. Rather than debate SOA merits or whether or not REST 'is' SOA, discussing SOA provides little constructive benefit and whether rightfully or not has bad connotations among some within the IT community._

#### What makes an HTTP/RESTful API good?

So, what makes an API a good API? We will use the following tenets gathered from various authors across the web:

* Uses HTTP correctly (with respect to HTTP methods, status codes, headers, caching, etc.)
* Has documentation sufficient to guide the user of the API (and that does not redundantly document REST principles but instead focuses on representations, validation rules, security, etc.).
* Provides support for backward compatibility and or versioning
* Uses hypermedia as the engine of application state _(this is required for an HTTP API to also be considered a RESTful API.)_
* Follows Jon Postel's Robustness Principle

>Follow a general principle of robustness: be conservative in what you do, be liberal in what you accept from others."
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Jon Postel, TCP document RFC 761

----

## Guidance for HTTP and REST/Hypermedia APIs

Expose APIs that adhere to REST principles whenever possible.  This section provides specific recommendations and guidance for developing HTTP/RESTful APIs.

#### <a id="meaningful-resources"></a>Expose Meaningful Resources for Your API

>The key abstraction of information in REST is a resource. Any information that can be named can be a resource: a document or image, a temporal service (e.g. "today's weather in Los Angeles"), a collection of other resources, a non-virtual object (e.g. a person), and so on.
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Roy Fielding

Or, more concisely:

>"A resource is anything that's important enough to be referenced as a thing in itself.
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Richardson and Ruby

Determining the resources to be exposed is similar to determining the classes one specifies within an object oriented design. In fact, resources often correspond to persistent domain objects (aka models, business objects, or entities) within an application. However, akin to object reification, resources may also be used to represent less tangible concepts (e.g., transactions) that are not persisted.

There is much material available on-line or within [texts](#texts) regarding '[resource orientation](http://en.wikipedia.org/wiki/Resource-oriented_architecture)'. A few key points to remember include:

* Resources are addressable through a URI
* All interaction with a resource is limited to using one of the standard REST actions (i.e., one of the HTTP Methods discussed [below](#http-methods))
* Resources are not exposed directly but instead 'representations' of resources are exposed.
* Resources are not just back-end domain models, but are designed for external client use. That is, while resources are often constructed using data and structure from a backend domain model, it is not recommended the resource 'be' the domain object (without any transformation or thought).
* Resources may be 'nested'. That is, the URIs may reflect that one resource is nested within another like in this made-up example:  http://greatvalley.edu/example-app/courses/2305/sections/2465 (where section '2465' is a nested resource under course 2305).
* Resources must be interacted with using a single request. An important consideration is that the interaction with a resource must occur within a single unit of work. For example, if one cannot update a resource without also updating another resource, then those two resources must not be exposed separately. It is important to consider transaction boundaries and relation constraints when exposing resource endpoints.

Identifying the resources to expose, and the content of those resources, is an iterative process and requires feedback.  Consequently, agile development practices that are focused on rapid feedback loops should be used when developing an API.  Developing APIs without rapid feedback from users of the API is simply not following an agile process. It is also important not to allow one client to drive the API design at the expense of other clients (if they are also known), or to deviate from common API practices or this strategy.

Lastly, it is very beneficial to use the API internally whenever possible - as the saying goes: "to eat your own dog food".  Exercising an API internally will help uncover inefficiencies and problems, so you may improve the API more quickly than if the only feedback was from external users.

#### <a id="http-methods"></a>Use Standard HTTP Methods

APIs should at a minimum expose a standard interface using HTTP methods (aka verbs) shown below:

| HTTP Method | Purpose |
|:---------:|:----------|
| GET | Read a resource or list of resources |
| POST | Create a new resource (when the key is not known a-priori) |
| PUT | Update an existing resource or create one if the key is pre-defined |
| PATCH* | Partially update an existing resource |
| DELETE | Remove a resource |

_\* note: While PATCH support is not always required, it is highly recommended._

These methods correspond nicely to database CRUD (Create, Read, Update, Delete) operations, although this correlation is not absolute (as discussed later).

In addition, APIs must ensure that:

* 'GET' is 'safe', meaning that it does not have side effects and does not modify state on the server.
* 'GET', 'PUT', 'PATCH' and 'DELETE' must be 'idempotent', meaning subsequent invocations of the same request will have no adverse consequences (other than using CPU/memory/etc. in order to provide a response to the client).

That is, if a client application issues a 'PUT' request to update a resource, any subsequent issuing of that same request will have no effect (other than reporting an error, as the resource has already been updated).

Ensuring PUT and PATCH are idempotent is a developer responsibility and is best explained using an example. Let's consider a resource that contains a numeric property. In this example, the API must not allow PUT to be used to simply 'increment' this value (e.g., to add 'one' to the current value) but must instead set the property to a specific value.  Optimistic locking and using ETag-based conditional requests are two approaches (discussed later) that can ensure idempotency.  Similarly, in the case of 'DELETE', a second issuance of the same request must result in an error (as the resource was already deleted).

When using PUT to 'update' a resource, PUT support may employ PATCH semantics. That is, PUT may be used to 'replace' (i.e., employ proper PUT semantics) a resource or partially modify (employ PATCH semantics) an existing resource. Regardless of the semantics used for PUT, the preferred approach for partially modifying existing resources is to use HTTP PATCH.  

When using JSON representations, it is very highly recommended [JSON PATCH](https://tools.ietf.org/html/rfc6902) be used for supporting PATCH. JSON Patch, discussed later in this document, provides a very explicit mechanism to describe changes to a resource.

Two additional HTTP methods may be needed:

| HTTP Method | Purpose |
|:---------:|:----------|
| HEAD | Read metadata about a resource, but not the resource itself |
| OPTIONS | Discover the supported methods, as returned in an 'Allow' header |

Support for OPTIONS is particularly important to allow use of [CORS](http://en.wikipedia.org/wiki/Cross-origin_resource_sharing) (to support pre-flight requests issued by modern browsers).

#### <a id="http-codes"></a>Use Standard HTTP Status Codes

HTTP APIs should be designed to use standard [HTTP status codes](http://httpstatus.es).  While there is no 'standard' regarding their use within APIs, the following status codes are recommended:

| Status Code | Description |
|:---------:|:----------|
| 200 | OK - The request was successful. |
| 201 | Created - A resource was successfully created.|
| 202 | Accepted - Request has been received. This is only used for asynchronous processing. |
| 204 | No Content - Request was successful and no content is returned. This is a common response to a DELETE request, and is permitted for POST if there is no value in returning the newly created resource. |
| 304 | Not Modified - The GET request was conditional and contained an 'If-None-Match' header that was used to determine the current representation has not changed |
| 400 | Bad Request - The request cannot be understood. This should be used if there are syntax errors and validation errors, ('422' is also acceptable for validation errors.) |
| 401 | Unauthorized - when a user is unknown (e.g., an Authorization token is missing or unreadable)|
| 403 | Forbidden - the known user is not authorized. |
| 404 | Not Found - The requested resource instance could not be found. |
| 405 | Method Not Allowed - The method is not supported by the resource. |
| 406 | Not Acceptable - Return this code when the Accept header (or a custom header as discussed in this strategy) requests a response in an unsupported format. |
| 409 | Conflict - Return this if the request reflects stale data (that violates an optimistic lock). |
| 412 | Precondition Failed - Used for conditional requests (e.g., when an 'If-Match' header does not match a resource's Etag value). |
| 415 | Unsupported Media Type - Return when a client request contains content in an unsupported format. |
| 417 | Expectation Failed - Return this if appropriate when supporting 'look-before-you-leap' requests |
| 422 | Unprocessable Entity - See note below. |
| 429 | Too Many Requests - The configured rate limit is exceeded by the client. |
| 500 | Internal Server Error - An undisclosed server error occurred. This is a generic 'fail whale' response. |
| 501 | Not Implemented - Return when support for the HTTP method is not implemented for any resource. |
| 502 | Bad Gateway - Return when acting as a gateway to another service that is not available. |
| 503 | Service Unavailable - Return when the service is down (this is a friendly way to inform the client) |
| 504 | Gateway Timeout - Return when acting as a gateway to another service that is not responding. |

Note: 422 is sometimes used for _validation errors_ which is permitted by this strategy. However, '400' is preferred since '422' is really a '[WebDAV extension](http://www.webdav.org/specs/rfc2518.html)'.

No. You really should **not** use '418 (I'm a teapot)' :-)

While the above table represents a minimum list for API developers, your middleware will certainly use additional codes, particularly those related to redirects.

#### Support Conditional Requests and Caching

It is recommended that HTTP APIs support conditional requests and caching by including an ETag header within every response to a GET request.  ETags should be used to provide an opaque cache validator using an MD5 or SHA1 hash of the bytes within a representation.

When a representation includes an optimistic lock token (e.g., often labeled as a resource 'version'), this can be used to augment the current optimistic lock verification. That is, since an ETag value will be constructed using the representation that includes the optimistic lock token, clients may use conditional GET requests to support optimistic locking.

A conditional GET request may use the 'If-None-Match' header containing an ETag value returned from a previous GET request. If the service finds the resource still has a representation that has this ETag, the service should return an HTTP status code of 304 instead of returning the resource.  This is intended to reduce bandwidth requirements (although likely won't reduce service processing, as the representation will need to be constructed in order to perform this check).

Similarly, a conditional PUT or PATCH may be used. If the 'If-Match' header is different than an ETag calculated for a representation of the current state of the resource, an HTTP status code of 412 should be returned. Note this will occur if the optimistic lock token that is part of the representation is different. In this case, if a conditional request were not used, the optimistic lock validation will fail and an HTTP status code of 409 should be returned.

To avoid unnecessarily sending a large representation to the server, an API may also support 'look-before-you-leap' (LBYL, as coined by [Richardson and Ruby](https://www.amazon.com/RESTful-Web-Services-Leonard-Richardson/dp/0596529260/)) requests. An LBYL request is either a PUT or POST request that contains no body and includes an 'Expect' header with a value of '100-continue'.  This is useful when determination of whether a PUT or POST 'would' be successful can be made using the current state of the resource or the HTTP headers included in the request. If the server determines the request would not be accepted (e.g., perhaps the request's 'Content-length' header indicated the representation was excessively large) it should return a '417 Expectation Failed' status code.  Support for LBYL requests is not required.

#### Report Validation Errors

To augment use more generic status codes like '400', it is recommended that a custom HTTP Header be used to provide more information. For example, if using a '400' status code for a validation error, a custom HTTP Header can provide additional information:

```http
    HETech-Status-Reason: Validation failed
```

Note that [RFC 6648](https://tools.ietf.org/html/rfc6648)) deprecates a previous 'best practice' of prefixing all custom header names with 'X-' (as in 'X-Resource-Version'). To reduce risk of name collision, it is recommended a 'domain' be used as the prefix (as in 'Hedtech-Resource-Version'.

Validation errors should also be provided within the response body.

The JSON or XML should provide details of any validation errors if a 400 or 422 status code is returned.  Following is an example error:

```json
  { "errors" : [
      {
        "type": "validation",
        "resource": {
                      "id": 25
                    },
        "message": "districtDivision value exceeds two character limit"
      }
    ]
  }
```

The specific structure and format of detailed error information is not standardized, and this strategy allows for different representations since various frameworks handle such responses differently.

#### Report Optimistic Lock Violations

Resources may be protected against stale updates by incorporating an optimistic lock.

It is recommended that '409' be returned whenever an optimistic lock violation occurs while updating the database, regardless of whether there are other validation errors. This is often not the behavior implemented within frameworks (including Grails) and may require special logic to handle. The argument for this recommendation is: 'Do you really know if a resource representation is invalid when that representation is based on stale data? Some validation checks might fail simply because the representation is not consistent with the current persisted state. This recommendation is not a requirement, but warrants consideration when developing an API.

When handling a conditional PUT or PATCH request as discussed above, a 412 should be returned.

Using an ETag-based conditional request (as discussed earlier) is preferred over incorporating an explicit optimistic lock check (and returning a '409' when violated) as described in this section.

#### <a id="uri-conventions"></a>Use Common URI Conventions

With respect to URIs, HTTP APIs should:

* Use transparent (readable) URIs unless specific requirements preclude
* Use dashes (aka hyphens) in lieu of underscores (when easily supported by your framework)
* Support nested resources when doing so is logical and only two levels deep
* Use pluralized resource names regardless of whether addressing a collection or a single instance. For example, a list of colleges might be retrieved using 'GET /colleges' while a specific college instance might be retrieved using 'GET /colleges/\{id\}'. (Using singular naming is not incorrect, but using plural naming is common and may arguably read better.)
* Avoid use of multi-part keys to identify a resource.  If an exposed resource does not have a single, immutable key, attempt to model it as a nested resource.  For example, if exposing items from a checklist, and your legacy data model can only retrieve items by a combination of checklist name and item number within the checklist, prefer exposing items as '/checklists/myList/items/3' as opposed to /items/myList-3, which requires both caller and application to agree on an encoding mechanism for the multi-part key 'myList-3'.

A nice overview of URIs is available at '[http://www.skorks.com/2010/05/what-every-developer-should-know-about-urls](http://www.skorks.com/2010/05/what-every-developer-should-know-about-urls)'.

An example URL compliant with this strategy is:

```http
  https://greatvalley.edu/student-course-catalog/api/colleges/college-2453
```

#### <a id="json-patch"></a>Support JSON PATCH

When supporting PATCH and JSON representations, it is _highly recommended_ that [JSON Patch](https://tools.ietf.org/html/rfc6902) be employed as a standard approach to PATCH. JSON Patch is now supported on most platforms and there are many supporting libraries to facilitate supporting this standard.

The proper custom media type for PATCH requests using JSON Patch is 'application/json-patch+json'. This standard custom media type should be set as the Content-Type header irrespective of the resource being patched.  
A [JSON Schema for JSON Patch](https://github.com/chasdev/sample-json-schemas/blob/master/json-patch/json-patch.json) may be used to validate a patch.

#### <a id="api-versioning"></a>Use API Versioning (or not?)

It *may* be important to version APIs in order to provide a stable API to clients.

There are several common approaches used across the industry today for versioning APIs, including:

* using a custom header in requests and responses to identify a version
* identifying the version explicitly as part of the address (URL) of a resource
* using a query parameter to request a particular version
* using a custom media type to indicate versions within Accept and Content-Type headers

If versioning is deemed necessary, it is recommended HTTP APIs use a custom header. For requests, a simple integer version indicator (e.g., v1, v2, v3) is recommended (as this may be used for internal routing to different non-backward compatible versions of the API). An API client will not likely benefit from communicating the 'minor' or 'patch' components of a version (assuming [Semantic Versioning](http://semver.org/)) as minor.patch components represent backward compatible changes to an API that would not be implemented separately. Communicating a 'minor' release within a response is optional, but may allow clients to start using new features once an expected 'minor' version is made available.

When a client does not specify a version, the latest (current) 'production' version of the API should be assumed. That is, specifying the version should be 'optional' (with the default version being the latest version supported by the API).

Regarding the other approaches:

* Embedding version identifiers within the URL results in URLs that change, even though the 'resource' itself has not changed. This violates a major tenet of API design. However, many APIs do adopt this approach and while not 'pure' it does offer simplicity. While not explicitly recommended in this document, there are certainly well respected champions of this approach. If this approach is taken, only 'major' API versions should be reflected.
* Using a query parameter to specify versions is not convenient as it requires parsing of query parameters. It also does not allow for communicating version information within responses. Lastly, the use of query parameters within POST is not standard (i.e., and may not be effective in all implementations of HTTP endpoints). This approach is 'not recommended'.
* Using a custom media type to indicate versioning may become inconvenient and may result in a very large number of custom media types that a client application must understand. Using custom media types for versioning is 'dual purposing' custom media types to specify versions as well as to specify alternative representations of a resource.

Note when using custom media types to identify alternative representations and or potentially versioning, they do not need to be [registered](http://www.iana.org/assignments/media-types/application) nor do they need to correspond to a schema document (although they often may). Following is an example custom media type that includes version:

```http
  application/vnd.hedtech.persons.v2+json
```

_Please reference the '[Use Standard Media Types](#media-types) section of this document for additional discussion regarding media types (that is not focused on versioning)._

A final note regarding API versioning: In some cases **it may not be warranted.**  In fact, Roy Fielding states the best practice for versioning an API is:

>["Don't"](http://www.slideshare.net/evolve_conference/201308-fielding-evolve/31).

Versioning of APIs introduces complexity both within the service implementation as well as within clients using the API.  If an API is 'relatively stable' (a subjective measure), the day-to-day cost may exceed the potentially very rare need to introduce a non-backward compatible representation of a resource. HATEOAS, if employed, also provides mechanisms to introduce changes without versioning.

#### <a id="api-pagination"></a>Support Pagination

When returning collections, it will often be necessary to support pagination.  There is no single standard for pagination. Two very common approaches are 'page-based' or 'offset-based' pagination, although other 'cursor-based' (or 'token-based') are also found across the web.

Neither an 'offset-based' or 'page-based' approach can be stated as 'being more correct', however an offset-based approach does provide more granular control to the client. Consequently, an 'offset-based' approach is recommended, although a page-based approach would also satisfy most needs.

It is recommended two query parameters be used to provide pagination control to the client, as shown in the following example and table:

```http
  https://greatvalley.edu/student-course-catalog/api/colleges/?offset=350&limit=50
```

| Query Param | description |
|:---------------:|:------------|
| offset | This represents the offset from the first item in the dataset. |
| limit | This represents the maximum number of items that may be included in the response. |

While 'offset' and 'limit' are recommended, the specific query parameter names may differ based upon your framework. For instance, Grails uses 'offset' and 'max'.

It is a developer responsibility to provide validation and safeguards of pagination control. For example, if a client issues a request using a 'limit' of 10000, the API implementation may either reset the 'limit' to an acceptable pre-configured maximum before handling the request or i5 may return a 400 error code. In general, it is recommended to set this to a pre-defined maximum and not return an error.

##### HTTP Response Headers

While the two query parameters above are used by a client to communicate pagination within an HTTP Request, an API will need to communicate pagination information within the response. It is recommended this be accomplished using two HTTP Headers, one standard and one custom, as described below.

*Link Header*
A standard [Link Header](http://tools.ietf.org/html/rfc5988) is recommended to communicate pagination within a response to a GET request for multiple resource instances whenever the response does not include the full dataset. (If it does include the full dataset (i.e., there is only one page) the Link Header may optionally still be included, but isn't necessary.)

A Link Header identifies four 'link relation types' ('first', 'prev', 'next', and 'last') that pertain to pagination. These are shown in the example Link Header below:

```http
Link: <https://{host:port}/courses?offset=500&limit=100>; rel="next",
  <https://{host:port}/courses?offset=2700&limit=100>; rel="last",
  <https://{host:port}/courses?offset=0&limit=100>; rel="first",
  <https://{host:port}/courses?offset=300&limit=100>; rel="prev",
```

<br/>
The above example shows use of a concatenated Link header as defined at [http://www.w3.org/Protocols/9707-link-header.html](http://www.w3.org/Protocols/9707-link-header.html), where multiple lines need to be prefixed with a whitespace character.

Note each response may not contain all four of these link relations (aka relation types). For example, an initial request for the first 100 items will not be able to include a 'prev' link relation (as there is no previous batch of items at the beginning of the dataset). Similarly, the last batch of items would not be able to include the 'next' link relation.

Also note multiple link relations may be combined for one link.  For instance, when the 'next' batch of items is also the 'last' batch, these two link relations may be expressed together as shown below:

```
  <https://{host:port}/courses?offset=500&limit=100>; rel="next last"
```

*Hedtech-Total-Count*
A custom Header is recommended to communicate the total number of items in a dataset (not just the number returned within a single response).

Providing the total count is quite useful although may not be supported in cases where a total count is difficult to assertain.

#### Support Querying

There is no current standard for querying HTTP APIs.

When feasible, query criteria should be specified using query parameters in a GET request.  For instance, the following URL could be used to retrieve a list of forest science courses for term '200510 Fall 2004'.

```http
  https://greatvalley.edu/student-course-catalog/api/courses?subject=FRSC&term=200510%20Fall%202004
```
A more structured alternative is to use a query parameter with a value of encoded JSON. This is a very expressive approach as JSON can be used to express very complex queries. It is common to support basic operations (e.g., $gt, $gte, $lt, $lte, $ne). Many APIs and databases provide query support using this approach (e.g., [MongoDB Query Documents](https://docs.mongodb.com/manual/tutorial/query-documents/)).

Here is a simple example:

```
  # GET /courses?filter={"title" : "my course title"}
  # encoded as:
  GET /courses?filter=%7B%22title%22%3A%22my%20course%20title%22%7D
```

Note that 'nested' resources can be used to provide an elegant HTTP API that may reduce the need for (and the complexity of) queries or filtering.  Use of nested resources is highly recommended as it can result in very well organized APIs.

The following example shows how a resource may be exposed directly as well as be exposed as a nested resource, to allow a client application to retrieve a list of Forest Science courses for term '200510 Fall 2004'.

```http
  GET /courses?subject=FRSC&term=200510%20Fall%202004
  GET /subject/FRSC/courses?term=200510%20Fall%202004
  GET /subject/FRSC/terms/200510%20Fall%202004/courses
```

Note the last example above has deep nesting, which is not recommended. It is recommend that resources only be nested one level. Also note, that same resource (courses) could be accessed as a nested resource under yet another resource

```http
  GET /instructor/3895483/courses
```

Nested resources may be used as a scoping mechanism (where the ID of the nested resource is unique only within the scope of its parent) or only as a 'filtering' mechanism (where the nested resource ID is unique and usable beyond the scope of its parent resource). This latter use can be thought of as 'sugar' for filtering (that could alternatively be expressed as criteria specified using query parameters).

Including query (or filter) criteria within a GET request body is not recommended, as while the [HTTP 1.1 specification](http://www.w3.org/Protocols/rfc2616/rfc2616.html) doesn't preclude a GET containing a body there is [no convention in HTTP\/1.1 for safe interaction](http://www.w3.org/2001/tag/doc/whenToUseGet-20040321#safe) when a body is used. Nevertheless, some solutions such as [Elastic Search](http://www.elasticsearch.org) do implement search using this approach.

For situations where complex query criteria must be used that is cumbersome to encode within the URL, a better approach is to model the query as a separate 'query' resource.  This would enable a client application to create a new 'query' resource (using an appropriate representation to codify the query criteria). The POST may result in execution of the query (referred to as 'query-by-POST'), or the API may require a subsequent GET of the newly created query resource (whose representation would include both the query criteria and the query results).

When query criteria includes Personally Identifiable Information (PII), it must either be encrypted (if it is being passed as query parameters) or a POST must be used as discussed above.  This is because query parameters will likely be recorded within an HTTP server's access log.

#### <a id="prefer-json"></a>Prefer JSON Representations

JSON ([JavaScript Object Notation](http://www.json.org/)) is a data interchange standard that is a subset of the ECMAScript 5 language (JavaScript).  It provides a nice balance between expressiveness and simplicity, and is the preferred representation for HTTP APIs.

XML based representations may be employed in addition to or instead of JSON when requirements dictate.  Selected APIs may also support other formats in lieu of JSON and or XML when this is beneficial to the clients.

Using representations that support hypermedia is discussed later in this document within section [Support 'Hypermedia' APIs](#representation-hypermedia).

#### <a id="media-types"></a>Accept Both Standard and Custom Media Types

Determining the media type of a request is referred to as 'content negotiation', and is normally based upon the HTTP 'Content-Type' and 'Accept' headers.  For example, a client may submit a request using a 'Content-Type' header of 'application/xml' to inform the service that XML is being used, and also include an 'Accept' header of 'application/json' to request the service to respond with JSON content.

When needed, RESTful APIs should support multiple [application media types](http://www.iana.org/assignments/media-types/application/index.html) by employing content negotiation. When a request identifies a custom media type that is not supported, a 415 (unsupported media type) error should be returned.

RESTful APIs should use the standard 'application/json' (or 'application/xml) media type when a custom type is not needed (i.e., when the default representation is being used). If no Accept header is specified, 'application/json' (or 'application/xml') should be assumed (based upon whether the API primary representation is JSON or XML). RJSON is the preferred representation.

While JSON is recommended for most endpoints, other representations may be required. For instance, 'ical' may be specified using 'text/calendar' and PDF may be specified using 'application/pdf' (or their custom media types counterparts 'text/vnd.hedtech.v2+calendar' and 'application/vnd.hedtech.v2+pdf' respectively).

#### <a id="standard-media-types"></a>Respond with Standard Media Types

When a custom media type is used, the service may return a response using a Content-Type header with the standard 'application/json' (or 'application/xml') media type and an additional custom header such as shown below:

```http
  Hedtech-Media-Type: application/vnd.hedtech.persons.v4+json
```

This will facilitate viewing the response in a browser (e.g., using an add-on to render JSON or XML) or development tool while still communicating the version or custom format to the client.  Returning standard application/json is particularly useful for clients learning about your API, as browser add-ons will enable clients to more easily view the response.

#### <a id="domain-style"></a>Use a Consistent Representation 'Domain Style'

Defining representations includes selection of the properties to be expressed, selecting appropriate attribute and element names, defining the structure (including hierarchical structures), and selecting the affordances that are deemed important to include.  Mike Amundsen[*](#texts) identifies three primary styles for representing domains: 'domain-specific', 'general', and 'agnostic'.

| Domain Style | Description |
|:-------------:|:-----------------|
| domain-specific | Closely models the domain with respect to element names and structure.  Example element: "home-address" |
| general | General element names are used whenever possible (and these are then decorated with attributes to provide the specificity). \\Example element: "address": \{"type":"home \} |
| agnostic | Uses generic element names and relies on attributes to provide context.  Example element: "ul": \{"type":"home-address" \} |

This strategy does not (at this time) require one style versus another but provides the following guidance:

* Recognize these styles and apply them consciously
* Favor use of the 'domain-specific' style due to it's simplicity
* Collaborate with GA to determine if there are reusable representations that may be used. (See '[Governance](#governance)'.)
* Balance "purity" with pragmatism. Authors and standards like OData specify affordances that some may not consider pragmatic.  It is recommended to balance the 'purity' of approach with simplicity and productivity.

#### <a id="prefer-unwrapped-representations"></a>Prefer Unwrapped Representations

It is preferred by this strategy to accept and return content that is comprised solely of one or more representations, as opposed to wrapping the representation within another structure (i.e., an envelope).  

While returning a 'pure' representation is very common when returning a single representation, some services return collections of a resource within another structure in order to include additional information such as paging information, total counts, etc.  Use of such an envelope is not recommended.

It is preferable to communicate any additional information such as pagination information using HTTP headers (e.g., using a ['Link' header](http://www.w3.org/Protocols/9707-link-header.html) is recommended).  Please see '[Support Pagination](#api-pagination)' which discusses pagination and the use of the Link header.

The content is returned as a JSON array of the resources:

```json
  [
    {
      "id": 25,
      "version": 3,
      "code": "SA",
      "description": "College of Sciences & Arts",
    },
    {
      "id": 26,
      "version": 1,
      "code": "ZO",
      "description": "College of Zoology",
    },
  ]
```

#### <a id='bulk'></a>Bulk Processing (Collections) _(synchronous)_

HTTP APIs may be developed to support 'bulk' processing when warranted. Such APIs expose resources that represent a collection of resources of a single type. Specifically, a 'bulk' API exposes a 'collection' resource that can include multiple resource instances to be handled in one synchronous invocation.  A 'bulk API' is simply an API that exposes a 'collection' resource. Since the 'collection' is represented as a single 'resource' that holds a collection of other resources, no 'bending of the rules' is required to support 'bulk APIs'.

APIs that expose 'collection' resources may support GET, POST, PUT, PATCH, and DELETE following normal HTTP API guidelines. However, the use of GET should be supported only if the collection resource has a meaningful ID (as discussed below). Since it is easy to page through a filtered collection of resources without using a 'collection' resource, the only reason to retrieve one or more pages of 'collection' resources is if those collections have true business meaning (and weren't just being used as a mechanism to support 'bulk' operations).

Using a 'collection' resource to represent many items is helpful to enable bulk POST, PUT, PATCH, and DELETE operations even when a collection may not itself have business meaning. GET should only be supported when the collection itself has business meaning (i.e., represents meaningful groupings of other resources).  

_Again, a 'bulk API' (i.e., exposing a 'collection' resource) is not necessary for filtering and paging a large number of individual resources. It is helpful when creating/modifying/deleting a collection of resources in a single operation._

The response should include a representation of the 'collection' resource that includes at a minimum the identifiers necessary to allow a client to subsequently interact with any contained resources individually.

A 'collection' resource should, like any other resource, have a unique ID. However, this strategy allows the use of collection resources that do not have a unique ID (and do not have any business value). In these cases, processing is performed on the items within the collection, each of which must have a unique ID, and the collection itself has no meaning other than being used as a mechanism for issuing a single POST/PUT/PATCH/DELETE request that affecs multiple resources.

When a collection does not have business meaning (and thus does not have a meaningful ID), it is recommended a '1' be used to represent the ID (as we must retain the normal URL patterns that include an ID). However, in this situation, it is very important to consider the implications of caching (obviously, you will not want collection resources that do not have a meaningful ID to be cached). If a collection has a meaningful ID, GET should be supported to retrieve that identified collection.

```http
\# Note: id of '1' is not really used but included to comply with conventions
PUT /collection-of-persons/1
Content-Type: vnd.hedtech.collection-of-persons.v1+json;charset=utf-8
Accept: vnd.hedtech.collection-of-persons.v1+json
```

```json
{
  "persons": [
    { "name": "Bart", "someAttribute": "someValue" },
    { "name": "Homer", "someAttribute": "someValue" }
  ]
}
```

**Characteristics of a bulk API include:**

* Employs the use of 'collection' resources, which is a common pattern
* RESTful (albeit the 'id' used for PUT, PATCH and DELETE may not be meaningful as discussed above)
* May be directly supported by a resource-collection-specific service which leverages batch database operations.
* Supports only a single operation for all elements in the collection, based upon the HTTP method used (per normal REST principles).
* May represent a single unit of work resulting in 'all or nothing' semantics. Whether an endpoint supports 'partial success' or 'all or nothing' semantics is an implementation detail (of the service layer). Both approaches are legitimate and may be beneficial in specific situations.  API Documentation must *explicitly* specify the semantics of each bulk API.
* When a bulk API supports 'partially successful' semantics, the status code should indicate success (200/201) if at least one operation on an element in the collection was successful, and the body should reflect the individual status of each element in the collection. The individual status may be represented as affordances.
* When a bulk API supports 'all or nothing' semantics, it is highly recommended that database interactions be optimized for batch processing in order to reduce overhead (e.g., through JDBC batching, session management (flushing and clearing at batch boundaries), 2nd level cache management, use of server-side cursors, etc.).

#### <a id='batch'></a>Batch Processing APIs _(asynchronous)_

HTTP APIs may support 'batch' processing. Such APIs perform work *asynchronously*. Specifically, a 'batch (job)' API supports submission of asynchronous tasks.

When an API is asynchronous it should respond with a '202 Accepted' HTTP status code.

Following is an example request that shows a 'bulk' collection-oriented resource (see '[RESTful Bulk Processing](#bulk)' above) being created asynchronously.

While 'batch (job)' APIs may often be used with 'collection' resources, a 'batch' API may be used for any resource. That is, an API can be exposed to support 'bulk', 'batch', or both bulk and batch job processing.)

```http
POST /collection-of-persons
Content-Type: vnd.hedtech.collection-of-persons.v1+json;charset=utf-8
```

```json
{
   "persons": [
      { "name": "Lisa", "someAttribute": "someValue" },
      { "name": "Marge", "someAttribute": "someValue" }
    ]
}
```

and an example response that includes a reference to the 'batch' processing:

```http
HTTP 1.1 202 ok
Hedtech-Message: collection-of-persons resource accepted
```

```http
{
  "_link": "collection-of-persons/12345"
}
```

In this case, where we used a 'batch' API to create a 'collection' resource, that collection resource must be identifiable. Having such an identifier allows the client to issue a subsequent GET:

```http
GET /collection-of-persons/12345
Accept: vnd.hedtech.collection-of-persons.v1+json
```

whose response gives the current status of the asynchronous processing:

```json
{
  "_link": "collection-of-persons/12345",
  "status": "pending",
  "results": [
      { "status": "succeeded",
        "resource": { "name": "Lisa" }
      },
      { "status": "queued",
        "resource": { "name": "Maggie" }
      },
      { "status": "failed",
        "errors": [
          { "error":
             { "type": "validation",
               "resource": {
                  "id": 25
               },
             "message": "firstName value exceeds 100 character limit"
             }
          }
        ]
      }
    ]
}
```

The life of a 'collection' resource created through a batch API may limited, and the SLA of the API should specify how long the collection may be accessible as a 'collection' resource (versus the affected individual resources that may have been created). That is, the 'collection' may simply be associated to a job that was used to create individual resources and many not itself have long-term business value.

**Characteristics of a batch (job) API are:**

* Supports submission of multiple, homogeneous operations against a specific resource using a single RESTful API invocation.
* Adheres to REST principles (as only a single resource is acted upon, albeit that resource may represent a 'collection').
* The 'asynchronous' nature is transparent to the RestfulApiController as it is encapsulated within the backend service to which the controller delegates. The controller only needs to know that a '202 Accepted' is the correct 'success' code.
* May or may not represent a single unit of work (transaction) - depending upon the implementation (as discussed within '[RESTful Bulk Processing](#bulk)' above).

**Special considerations for implementing a batch (job) API include:**

* If it is deemed necessary to expose a resource asynchronously, is there a need to also allow synchronous invocation? If so, a different prefix (e.g., 'aapi/') may be introduced to distinguish asynchronous from synchronous endpoints.)
* An API may expose a 'bulk' collection-oriented resource used in an asynchronous request such that subsequent GET invocations may be used to retrieve the status of the asynchronous work.  If the resource instead represents a single entity (versus a collection), the true 'id' of that resource should be returned if possible.  If this is not possible (e.g., the 'id' cannot be specified until the asynchronous processing completes) then a temporary id may be returned *as long as that 'id' cannot conflict with a real 'id'. If a real 'id' is returned and a client issues a GET using that 'id' before the resource has been created asynchronously, a '404' should be returned.

#### <a id="representation-hypermedia"></a>Support 'Hypermedia' APIs

Support of 'hypermedia' is a requirement for RESTful APIs as previously stated.

Unfortunately, neither XML nor JSON provide direct support for hypermedia. That is, neither XML nor JSON provides direct support for 'affordances' (or 'Hypermedia Factors') that express hypermedia related metadata.  Consequently, hypermedia APIs must codify this information.

>A design affordance is a design element that communicates how something should be used without requiring documentation.[*](http://blog.apigee.com/detail/restful_api_design_nouns_are_good_verbs_are_bad)

>"When I say Hypertext, I mean the simultaneous presentation of information and controls such that the information becomes the affordance through which the user obtains choices and selects actions." &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Roy Fielding

There is currently no de-facto standard with respect to XML or JSON H-Factors.  HTML5 and XHTML are currently the only standard media types that directly support affordances.  There are various solutions including [OData](#odata), [Linked Data Platform](#ldp]), [JSON-LD](http://json-ld.org/spec/latest/json-ld-syntax/), and [JSON Hypermedia API Language](http://tools.ietf.org/html/draft-kelly-json-hal-03) (HAL) that address hypermedia.

Through the use of hypermedia, 'Affordance-Rich Messages' (ARMs) may be used to build very generic solutions that "are capable of recognizing and reacting to affordances in the message itself."[*](http://ws-rest.org/2012/proc/a8-1-amundsen.pdf)

While APIs may simply incorporate additional links such as 'next' and 'previous' to support a multi-step process, link relations may be used to represent relationships between resources. These may be implemented using [atom:link](http://tools.ietf.org/html/rfc4287#section-4.2.7) or [HAL](http://blog.stateless.co/post/13296666138/json-linking-with-hal).

```console
  $ curl -H "Accept: application/json" -u batman:{PROTECTED} https://greatvalley.edu/student-course-catalog/api/colleges/25/
```

```json
  {
    "id": 25,
    "version": 3,
    "code": "SA",
    "dataOrigin": "robin",
    "description": "College of Sciences & Arts",
    "districtDivision": null,
    "_links": {
      "self": {
        "href": "/colleges/25",
        "title": "College of Sciences & Arts"
      }
    },
    "_supplementalData": null,
  }
```

When a representation complies fully with HAL, a media type of application/hal+json may be used instead of application/json. While other emerging standards (e.g., JSON-LD) may be considered, HAL is recommended due to its simplicity. (It is not a requirement to use HAL over JSON-LD or other approach; the product architect should review the referenced libraries before making a decision. When these 'standards' are adopted more widely, this strategy will very likely require one over the other.)

#### <a id="governance"></a>Governance

Governance is a broad area that may encompass service registries, schema catalogs, server configurations, service management (consumption, usage patterns, etc.), and even development processes.

At a minimum, a lightweight process and an organization-wide schema reuse library, data dictionary, or registry is needed to promote commonality across an organization creating APIs.

Governance should ensure consistent exposure of resources with respect to representations and semantics. For example, when an API client requests a 'student' resource and specifies 'vnd.hedtech.student.v2+json' in the 'Accept' header, a very specific representation is expected.

#### Avoid Data Binding Approaches

Binding approaches may hinder support of Jon Postel's Robustness Principle. Rigid binding strategies used to support representations of the internal resources can make it very difficult to be 'forgiving' (or liberal) with respect to the input that is acceptable.

Historically, many Java applications employed popular XML binding libraries like JAXB and Castor which simplified marshaling/unmarshaling but also resulted in very rigid implementations.  Often these applications validated the input using full validation (such as XML Schema validation) before it would be accepted.

It is preferable to be more flexible on what input is accepted. Architects should assess whether it is more important to ensure input is complete and valid versus accepting partial content if that content is sufficient to handle the request.

For example, if one needs to update a 'description' property within a resource, one need not supply a representation containing all of the other properties of that resource.  Supporting this actually requires additional code, but results in a more 'user friendly' API.

Accepting partial content is not a 'requirement', but it is an area that warrants consideration by the product architect.

#### Validate Representations During Testing

Despite the above discussion on 'binding' and the desire to be liberal in what one accepts, validation of representations contained in a request is certainly important for some applications and always vital for testing.

There are many tools supporting validation against XML Schema or JSON Schema.  When using JSON, a JSON Schema version 4 compatible validator should be used.

#### <a id="api-testing"></a>Use Automated Testing
It is an absolute, non-negotiable requirement that RESTful APIs be tested using automated regression tests.  Every modern platform supports this best practice, and no exceptions should be permitted.

It is important to test all representations of a resource, and all expected responses (both success and error).

Support for testing RESTful endpoints is often built-in to a testing framework or supported through a third party library. When available,  higher-level abstractions (i.e., DSLs) should be used.  For example, the following Groovy snippet shows the use of a higher-level DSL supporting REST:

```groovy
  post("/api/colleges") {   // 'POST' /api/colleges => 'create'
    headers[ 'Content-Type' ] = 'application/json'
    headers[ 'Authorization' ] = authHeader()
    body { """
      {
        "description": "Great Valley University",
        "code": "GV",
        "systemRequiredIndicator": "Y",
      }
      """
    }
  }
  assertStatus 201
  assertEquals 'application/json', page?.webResponse?.contentType
  def stringContent = page?.webResponse?.contentAsString
  def data = JSON.parse( stringContent ).data

  id = data.id
  assertNotNull "Expected new College with an id but got: ${data}", id
  assertEquals "Expected code 'Z9' but got ${data.code}", 'Z9', data.code
```

#### <a id="performance"></a>Load Testing

Testing must be conducted to ensure satisfactory performance under load. The specific performance requirements (requests per second, response times, etc.) should be established by the product owner.

This testing need not be complex. RESTful APIs can often be tested using simple tools such as '[httperf](http://www.hpl.hp.com/research/linux/httperf/)' or '[ab](http://httpd.apache.org/docs/2.2/programs/ab.html) (apache bench).  There are, however, many cloud-based services providing sophisticated support for load testing.

*While beyond the scope of this RESTful API guide, it is highly recommended API implementations be reactive systems.  Please reference [The Reactive Manifesto](http://www.reactivemanifesto.org).*

####<a id="cors"></a>Support Cross Origin Resource Sharing (CORS)

Modern solutions are likely to be implemented as single-page web applications comprised of JavaScript modules communicating to backend APIs residing in different domains.  To facilitate the ability to build such solutions, the API must support cross domain communication.

The preferred (and newer) approach is to support [Cross Origin Resource Sharing](http://www.w3.org/TR/cors) (CORS). CORS support is not necessarily required but is in many cases needed.

An alternative, older, approach to circumvent cross domain issues is to support JSON-P callbacks. This may be supported by allowing clients to include a '?callback' parameter to GET requests in order for the results to be wrapped within a JSON function. While permitted by this strategy, CORS is the preferred approach.

#### <a id="api-security"></a>HTTP API Authentication and Authorization

APIs will usually need to be protected from unauthenticated access and should also be accessed over an encrypted [TLS](http://en.wikipedia.org/wiki/Transport_Layer_Security) connection to prevent eavesdropping and tampering.

There are many approaches for authentication of API clients, some of which are listed below.  It is imperative the API developer understand security requirements and vulnerabilities, and ensure proper authentication and authorization. API security is not discussed in detail within this guide.

* [JSON Web Token](http://jwt.io) is **strongly recommended**. JSON Web Token (JWT) is the most common approach and is an excellent choice for APIs (as it is fully stateless). JWT should be used unless requirements dictate another approach.

Other options include:

* [HMAC-SHA256](https://en.wikipedia.org/wiki/Hash-based_message_authentication_code) Digest
* [OAuth 2.0 authorization protocol](http://tools.ietf.org/html/rfc6749)
 - [OpenID Connect](http://openid.net/connect/)
* [Basic Authentication](http://en.wikipedia.org/wiki/Basic_access_authentication) (over TLS, use only for legacy integration)
* [Jasig CAS](http://www.jasig.org/cas) (within a domain, use only for legacy integration)

_Note: [SAML 2.0](http://en.wikipedia.org/wiki/SAML_2.0) does not currently define a standard binding for RESTful API endpoints.  SAML assertions may be supported through integration with a CAS server or an OAuth 2.0 authorization server._

#### <a id="multi-tenancy"></a>Support Multi-Tenancy or Multi-Entity Processing

Codifying tenancy/entity information within the URI path is recommended if 'sharing' is allowed (e.g., through authorization policies) across tenants. The 'owning tenant' of a resource should be part of the address of a resource if that resource can be accessed by other tenants. The owning tenant may or may not be the same as the tenant of the caller (which should be identified via the Authorization above). For example, if the 'owning tenant' (identified as part of the URI) is different than the tenant identified in the request (e.g., within a JWT), 'authorization rules/policies' should be used to determine if the request can be handled.

```http
  /{application-name}/{owning-tenant}/api/{pluralized-resource-name}
```

Specifying the owning tenant within the URL is recommended over the use of subdomains if installed at client institutions that refuse to employ wildcard DNS entries (e.g., to avoid the higher cost of wildcard SSL certificates versus using standard SSL certificates). Also, wildcard certs are not supported in some older browsers \-- it is the responsibility of the API developer to assess browser requirements.

Using subdomains to support either multi-tenancy or multi-entity processing (MEP) is permitted.  In fact, the use of subdomains seems to be considered a best practice for two primary reasons: First, using a subdomain to specify a tenant/entity allows the URI path to stay consistent across tenants or entities and thus provides for a consistent API across all tenants or entities.  Secondly, since the FQDN (fully qualified domain name) references a particular tenant/entity, client applications may be configured to support a specific tenant or entity without needing to know anything about multi-tenancy or MEP. Employing subdomains may also facilitate configuration of load balancers and the use of content delivery networks (CDNs).

Lastly, when an API is intended to be used solely by client applications that are tenant-aware it is permitted to codify the entity using a custom header or directly within representations. While not recommended for most APIs, some client applications may benefit from more opaque MEP concepts that are propagated across the solution boundaries.

#### <a id="restdesignpatterns"></a>Understand Common REST Design Patterns

REST is simple and does not include standards (like the various WS-* standards used with SOAP) which address areas like reliable messaging, asynchronous messaging, or business transactions.  Consequently, when such support is necessary it must be modeled explicitly by the API designer.

**Reliable Messaging with REST**
Reliable messaging is critical within SOAP as that protocol uses only HTTP Post which is not idempotent. It is therefor dangerous to 'resend' a message.  While REST does not have built-in 'reliable messaging' support, it uses synchronous communication over the HTTP protocol and can thus recover more safely from potentially lost messages. The caller is responsible for checking the HTTP status code and ensuring a request has been handled.

**Asynchronous Processing with REST**
Asynchronous processing may be supported by having an endpoint perform work asynchronously, and immediately return a '202' status code with a link that the caller may subsequently use to check the status of the asynchronous process.  While this differs from asynchronous messaging (using store and forward model), it does support asynchronous processing.

**Business Transactions with REST**
SOAP allows for externalization of business transactions using WS-BusinessActivity.  Business (aka logical) transactions may be modeled explicitly as a resource, so that a business transaction can be started using a POST, status updated using PUT, and committed using PUT and or DELETE. Please note that physical transactions should never be exposed, as holding database resources hostage to external clients would be a 'very bad thing'.

----

## Other "Standards"

As mentioned previously, there are many efforts underway to address the current limitations with current REST best practices and framework support.

#### <a id="odata"></a>OData

[OData](http://www.odata.org) is an OASIS project originally driven by Microsoft.  OData is based upon the Entity 1 Data Model (EDM) from Microsoft's 'Entity Framework'.  While Forrester has positioned OData as speculative, .Net projects that already incorporate EDM may find this a reasonable approach.

OData is, however, very flexible with respect to both ‘filtering/querying’ and ‘refinement’ (where ‘refinement' refers to the practice of allowing the returned representations to be defined on the fly based upon the request). OData supports both of these concepts (the latter through the $select option).  (OData also supports $expand, a similar concept, to eagerly fetch related data.)  Regardless, ‘refinement’ is good for exposing a 'database-like' interface but may not be desireable for many APIs.

OData, or any other approach that supports ‘refinement’, can be very problematic for APIs supporting integration. That is, a client that requests a subset of a resource representation may find it cannot perform a subsequent PUT/PATCH as it doesn’t have the ‘real’ representation of the resource. This practice arguably marginalizes the concept of a 'resource', and is not generally recommended for APIs used for integration.

If tailoring of content is important, consider adopting a solution based upon '[Demand Driven Architecture](#demand-driven)' (that doesn't merge 'representations' with such customization). OData is not recommended.

#### <a id="ldp"></a>Linked Data Platform

[Linked Data Platform](http://www.w3.org/2012/ldp/wiki/Main_Page) was a W3C working group that focused on addressing hypermedia requirements not sufficiently supporting using current REST best practices. This is part of the 'semantic web' and builds upon RDF. Other standards such as JSON-LD also facilitate expression of directed graphs.  

This may be appropriate for services that have inherent support for core semantic web standards (e.g., [RDF](http://en.wikipedia.org/wiki/Resource_Description_Framework), [OWL2](http://en.wikipedia.org/wiki/Web_Ontology_Language), [JSON-LD](http://json-ld.org)), along with complementary standards like [SPARQL](http://en.wikipedia.org/wiki/SPARQL).

####  <a id="demand-driven"></a>Demand Driven Architecture

[Demand-Driven Architecture](http://www.infoq.com/presentations/domain-driven-architecture) (perhaps a better name is 'client-driven'?) addresses limitations of representation-based APIs by giving clients control with respect to tailoring the content to be retrieved. (This is similar to how OData allows 'refinement' of representations, but is separate from the concept of a 'representation' -- it doesn't try to do both like OData.)

Demand-driven architecture is compelling as it allows different client types to request only the data they need. This may be particularly important for mobile clients.

Both Netflix and Facebook are championing demand-driven architectures, and both have open sourced frameworks. Facebook released [GraphQL](http://graphql.org/) and a JavaScript [reference implementation](https://github.com/graphql/graphql-js). Netflix released [Falcor](http://netflix.github.io/falcor/), which is a JavaScript library that uses JSON Graph.

Demand-Driven Architecture addresses some limitations of a 'representation-based', 'one-size-fits-all' approach and may become a critical component of an overall solution. While it may or may not be suitable for your integration purposes, it allows multiple UIs implemented for various devices to fetch only the data needed.

>**GraphQL is highly recommended as an alternative to resource-oriented APIs,** particularly to support UIs.  GraphQL addresses some limitations and challenges found within resource-based APIs (e.g., avoiding one-size-fits-all responses, ensuring up-to-date documentation (by exposing a GraphQL Schema), client-side tooling (that can leverage the schema), and versioning (being able to deprecate within the schema)). It is very possible GraphQL may be a superior for your requirements, and it warrants consideration.

#### Using RPC-Based APIs

RPC-based APIs include those implemented using SOAP as well as native procedure invocations over a network.

_Some may argue that SOAP is 'document centric' and is not the same as RPC. While developers may want to make such arguments, the reality is the 'RPC' is embedded within the document.  This is true regardless of the WSDL style used, including Microsoft's 'document/literal wrapped' pattern._

While SOAP is not recommended, it can be convenient and appropriate to expose a simple API that is not based upon 'resources' or 'representations'.

##### SOAP and WS-* Web Services

WS-\* should not be employed unless there are specific requirements to support* (e.g., due to a partner agreement or 3rd party service). There are significant disadvantages to using SOAP and supporting WS-* standards, including:

* Requires use of XML (and thus precludes use of JSON)
* Not 'web' based, and in fact 'hides' the web.  While SOAP is usually used over HTTP, it uses only POST which is neither safe nor idempotent. SOAP provides it's own envelope versus using HTTP directly.
* It is *very* complex, with many sometimes-conflicting standards like WS-Addressing, WS-Eventing, WS-Coordination, WS-Reliability, WS-ReliableMessaging, WS-BusinessActivity, WS-Security, WS-BPEL, etc.
* It requires the use of WSDL and in practice requires the use of client frameworks due to higher complexity. There are [multiple WSDL styles](http://www.ibm.com/developerworks/webservices/library/ws-whichwsdl), although Microsoft's 'document/literal wrapped' pattern is the most common and should be employed in most cases.
* While cross platform, support varies greatly across platforms and frameworks (particularly for the various WS-* standards). The [WS-I](http://ws-i.org/deliverables/Default.aspx) (now within OASIS) was formed to mitigate this concern by defining a subset of interoperable standards referred to as the [Basic Profile](http://ws-i.org/deliverables/workinggroup.aspx?wg=basicprofile).
* It was primarily driven by vendors whose primary focus was on selling middleware, versus the community
* Strict typing and validation hinder support of Jon Postel's Robustness Principle (although this isn't specific to SOAP, it is very common within SOAP frameworks)

This does not mean to imply that REST is a replacement for WS-* Web Services.  WS-* standards address a number of areas that are not well suited for REST - such as advanced partial-message encryption, digital signing, asynchronous and reliable messaging, orchestration, etc. While REST APIs can be constructed to address many of these areas (recall section [REST Design Patterns](#restdesignpatterns) above), doing may incur additional complexity akin to using these WS-* standards.

##### Other RPC

While socket-oriented integration has had a reduced role since the emergence of the web, the recent 'Web Sockets' standard will be very important for developing rich 'real time' applications. Web Sockets enable a full duplex, bi-directional channel between the browser and the server and may be used to greatly improve the interactivity of a web application. While tools like Node.js greatly simplify socket programming, architects are expected to justify communication over sockets versus using HTTP requests.

Architects are cautioned not to allow the use of Web Sockets to diminish the focus on developing RESTful/hypermedia APIs. Web Sockets must be used to enable higher interactivity but not be used as a substitute for RESTful APIs.  (Note: There is work being performed within the community to codify the use of REST principles over Web Sockets.)

#### Asynchronous Messaging

Asynchronous messaging provides a number of benefits and may augment the use of HTTP and RESTful/hypermedia APIs.  Asynchronous messaging provides:

* Reliability and guaranteed delivery
* Asynchronous 'store and forward' semantics
* Support for the 'Pub Sub' domain (subscription based consumption)
* Support for event-based architectures

However, caution is again necessary to avoid 'Enterprise Application Integration' (EAI) failures such as high complexity and difficult administration of middleware. Web standards often provide a more desirable (less complex) approach than propagating the use of message broker middleware within an organization.  Additionally, firewall issues may require special permissions or HTTP tunneling.

Cloud-hosted asynchronous messaging solutions typically avoid these issues and expose HTTP APIs to support sending and retrieving messages. Amazon Web Service (AWS) [SQS](http://aws.amazon.com/sqs/) and [Kinesis](http://aws.amazon.com/kinesis/) are two such examples of asynchronous messaging services that may provide the specific semantics appropriate for a need.

In addition, some NoSQL databases including MongoDB and Redis may be used to implement pub/sub semantics. Redis is particularly popular for this purpose. Using Redis is particularly attractive in cases where the key store is leveraged for other purposes such as configuration or caching. The pub sub semantics are often employed internally (not for the purpose of integration but to distribute the work load across asynchronous workers).

Nevertheless, asynchronous messaging solutions may play an important role. [AMQP](http://www.amqp.org) is a preferred standard for cross platform asynchronous messaging, and many AMQP solutions exist (such as [RabbitMQ](http://www.rabbitmq.com)).

Other asynchronous messaging solutions that warrant consideration include [ZeroMQ](http://zeromq.org) and [MQTT](http://mqtt.org) (an OASIS [standard](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.html) that is _very_ popular when supporting the [Internet of Things](http://en.wikipedia.org/wiki/Internet_of_Things) (IoT)).

[JMS](http://en.wikipedia.org/wiki/Java_Message_Service) is not recommended for integration as this is a 'Java' standard, although JMS may certainly continue to be employed internally within JVM-based solutions. (Note many AMQP providers also support JMS, including RabbitMQ.)

#### Using Relational Database Integration (sans API)

Database integration (that exposes a relational schema and uses SQL) has a number of drawbacks and is not recommended for near real-time integration.

Specifically, database-level integration:

* Precludes refactoring/modernization of the database schema (without breaking 'clients')
* Exposes internal data constructs and precludes transformation of data prior to consumption
* Requires additional database connections (which may incur licensing costs)
* Limits deployment and network topology options (unless tunneled through either HTTP or SSH)
* May require database vendor lock-in (due to differences in SQL dialect, extensions, or feature set)
* Hinders modernization of client-server applications as needed business logic cannot readily be moved out of the database into a middle tier
* Allows clients to circumvent public APIs (if introduced) that may provide validation and or important business logic

While direct database-level integration has significant drawbacks, it should be recognized that ETL remains a viable integration approach in some situations (e.g, batch integrations).

----

## <a id="texts"></a>References

* [Hypermedia APIs with HTML5 & Node](http://www.amazon.com/Building-Hypermedia-APIs-HTML5-Node/dp/1449306578/ref=sr_1_1?ie=UTF8&qid=1354201129&sr=8-1&keywords=Hypermedia+APIs+with+HTML5+%26+Node), Mike Amundsen
* [RESTful Web Services](https://www.amazon.com/RESTful-Web-Services-Leonard-Richardson/dp/0596529260/), Leonard Richardson & Sam Ruby
* Numerous blogs found across the web. _(This strategy document is not an invention, but is a gathering of best practices and insights provided by many across the community.)_
* Many APIs (e.g, those from Google, Amazon, GitHub, Netflix) provide detailed documentation that helped form the recommendations herein.
* [JSON Hypermedia API Language](http://tools.ietf.org/html/draft-kelly-json-hal-03), Mike Kelly ([specification](http://stateless.co/hal_specification.html)
* [Collection+JSON](https://github.com/collection-json), Mike Amundsen

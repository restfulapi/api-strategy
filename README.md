
# RESTful API Strategy

<p/><p/>

## TL;DR

>This document provides guidance regarding the development of **non-trivial RESTful APIs.**

----

_Disclaimer: This strategy document is **not** an invention, but only represents a gathering of best practices and insights found across the web that we have adopted. Numerous blogs and papers have provided the foundation for the content found in this strategy. The recommendations in this strategy are simply 'recommendations'._

----

## Background

#### What is an API?

>An Application Programming Interface (API) is a specification intended to be used as an interface by software components to communicate with each other.  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Wikipedia](http://en.wikipedia.org/wiki/Application_programming_interface)

#### What is REST?

>REST is a set of principles that define how Web standards, such as HTTP and URIs, are supposed to be used (which often differs quite a bit from what many people actually do).[1](http://www.infoq.com/articles/rest-introduction)

REST stands for REpresentational State Transfer, and was first described in chapter 5 of [Roy Fielding's Ph.D dissertation](http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm).  There are many resources for learning about REST. For those who are not yet familiar with REST principles, you may want to review the [REST API Tutorial](http://www.restapitutorial.com).

APIs that adopt REST principles are 'stateless', 'resource-orientated', 'expose a uniform interface', and use 'hypermedia as the engine of application state' (HATEOAS).

For those not familiar with HATEOAS, a nice overview was authored by Jim Webber: "[How to GET a Cup of Coffee](http://www.infoq.com/articles/webber-rest-workflow)".  Essentially, HATEOAS exposes the state machine of an application such that the client interacting with the application is provided with appropriate options at each step within a workflow.  HATEOAS is not often supported by APIs stated as being RESTful, however Roy Fielding has stated '[hypermedia-driven application state](http://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven)' is a 'requirement' of REST.

Expanding a bit upon the above, we can state RESTful APIs:

* Expose 'Resources' to represent important concepts and objects
* Ensure each resource is uniquely 'addressable' via a URI, so that clients may interact with them over HTTP
* Provide one or more 'representations' (e.g., using JSON and or XML) of those resources
* Provide a consistent, standard interface based upon the standard HTTP methods
* Ensure interaction with the API is stateless in nature, in order to provide flexibility in deployment as well as to promote scalability
* Use hypermedia as the engine of application state

Martin Fowler authored a very nice overview of the [Richardson Maturity Model](http://martinfowler.com/articles/richardsonMaturityModel.html) (RMM) used for gauging an API's 'level of RESTfulness'.  This seems to be the most popular maturity model for REST.

While RESTful APIs provide important benefits to the clients of the APIs (as outlined above), REST principles may also be used to guide internal application architecture and design (referred to as [Resource-Oriented Architecture](http://en.wikipedia.org/wiki/Resource-oriented_architecture)).

**Thinking in terms of resources can promote elegant and simple solutions.**

_This strategy document will not reference '[Service Oriented Architecture](http://en.wikipedia.org/wiki/Service-oriented_architecture)' (SOA) terminology, as the term 'SOA' is very overloaded. Rather than debate SOA merits or whether or not REST 'is' SOA, discussing SOA provides little constructive benefit and whether rightfully or not has bad connotations among some within the IT community._

#### What makes a RESTful API good?

So, what makes an API a good API? We will use the following tenets gathered from various authors across the web:

* Adheres to core REST principles
* Has documention sufficient to guide the user of the API (and that does not redundantly document REST principles but instead focuses on representations, validation rules, security, etc.).
* Provides support for backward compatibility and or versioning
* Does not expose internal domain object structures as resource representations
* Requires knowledge only of a single URI entry point and access to documentation specifying the media types. _(Note this is an hypermedia 'stretch goal'.)_
* Follows Jon Postel's Robustness Principle

>Follow a general principle of robustness: be conservative in what you do, be liberal in what you accept from others."
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Jon Postel, TCP document RFC 761

----

## Guidance for REST/Hypermedia APIs

Expose APIs that adhere to REST principles whenever possible.  This section provides specific recommendations and guidance for developing RESTful APIs.

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
* Resources are not just back-end domain models, but are designed for external client use. That is, while resources are often constructed using data and structure from a backend domain model, it is not recommended the resource 'be' the domain object (without any transformation).
* Resources may be 'nested'. That is, the URIs may reflect that one resource is nested within another like in this made-up example:  http://greatvalley.edu/example-app/courses/2305/sections/2465 (where section '2465' is a nested resource under course 2305).
* Resources must be interacted with using a single request. An important consideration is that the interaction with a resource must occur within a single unit of work. For example, if one cannot update a resource without also updating another resource, then those two resources must not be exposed separately. It is important to consider transaction boundaries and relation constraints when exposing resource endpoints.

Identifying the resources to expose, and the content of those resources, is an iterative process and requires feedback.  Consequently, agile development practices that are focused on rapid feedback loops should be used when developing an API.  Developing APIs without rapid feedback from users of the API is simply not following an agile process. It is also important not to allow one client to drive the API design at the expense of other clients (if they are also known), or to deviate from common API practices or this strategy.

Lastly, it is very beneficial to use the API internally whenever possible - as the saying goes: "to eat your own dog food".  Exercising an API internally will help uncover inefficiencies and problems, so you may improve the API more quickly than if the only feedback was from external users.

#### <a id="http-methods"></a>Use Standard HTTP Methods
APIs should at a minimum expose a standard REST interface using the four HTTP methods (aka verbs) shown below:

| HTTP Method | Purpose |
|:---------:|:----------|
| GET | Read a resource or list of resources |
| POST | Create a new resource (when the key is not known a-priori) |
| PUT | Update an existing resource or create one if the key is pre-defined |
| PATCH* | Partially update an existing resource |
| DELETE | Remove a resource |

_\* note: PATCH support is currently considered optional._

These methods correspond nicely to database CRUD (Create, Read, Update, Delete) operations, however this correlation is not absolute (as discussed later).

In addition, APIs must ensure that:
* 'GET' is 'safe', meaning that it does not have side effects and does not modify state on the server.
* 'GET', 'PUT', 'PATCH' and 'DELETE' must be 'idempotent', meaning subsequent invocations of the same request will have no adverse consequences (other than using resources in order to provide a response to the client).

That is, if a client application issues a 'PUT' request to update a resource, any subsequent issuing of that same request will have no effect (as the resource has already been updated).

Ensuring PUT and PATCH are idempotent is a developer responsibility and is best explained using an example. Let's consider a resource that contains a numeric property. In this example, the API must not allow PUT to be used to simply 'increment' this value (e.g., to add 'one' to the current value) but must instead set the property to a specific value.  One strategy to ensure updates are idempotent is to use an optimistic lock. Optimistic locking, whose primary purpose is to protect a client from updating a resource based on stale data, is discussed later.  Similarly, in the case of 'DELETE', a second issuance of the same request must result in an error (as the resource was already deleted).

When using PUT to 'update' a resource, PUT support may employ PATCH semantics. That is, PUT may be used to 'replace' (using PUT semantics) a resource or partially modify (aka using PATCH semantics) an existing resource. However, the preferred approach for modifying existing resources is to employ PATCH.  

When using JSON representations, it is highly recommended [JSON PATCH](https://tools.ietf.org/html/rfc6902) be used for supporting PATCH. JSON Patch is discussed later in this document. 

Two additional methods are encouraged but not required (as many popular frameworks do not provide support for them).  These are:

| HTTP Method | Purpose |
|:---------:|:----------|
| HEAD | Read metadata about a resource, but not the resource itself |
| OPTIONS | Discover the supported methods, as returned in an 'Allow' header |

Support for OPTIONS is particularly important when supporting [CORS](http://en.wikipedia.org/wiki/Cross-origin_resource_sharing), to support pre-flight requests issued by modern browsers.

#### <a id="http-codes"></a>Use Standard HTTP Status Codes
RESTful APIs should be designed to use standard [HTTP status codes](http://httpstatus.es).  While there is no 'standard' regarding their use within REST APIs, the following status codes are recommended:

| Status Code | Description |
|:---------:|:----------|
| 200 | The request was successful. |
| 201 | A resource was successfully created.|
| 202 | Request has been received. This is only used for asynchronous processing. |
| 204 | Request was successful and no content is returned. This is a common response to a DELETE request, and is permitted for POST if there is no value in returning the newly created resource. |
| 304 | The GET request was conditional and contained an 'If-None-Match' header that was used to determine the current representation has not changed |
| 400 | The request cannot be understood. This should be used if there are syntax errors and validation errors, ('422' is also acceptable for validation errors.) |
| 401 | Authentication has failed, or the authenticated client is not authorized. |
| 404 | The requested resource could not be found. |
| 405 | The method is not supported by the resource. |
| 406 | Not Acceptable. Return this code when the Accept header (or a custom header as discussed in this strategy) requests a response in an unsupported format. |
| 409 | There is a conflict. Return this if the request reflects stale data (that violates an optimistic lock). |
| 412 | A precondition failed. Used for conditional requests (e.g., when an 'If-Match' header does not match a resource's Etag value). |
| 415 | Unsupported Media Type. Return when a client request contains content in an unsupported format. |
| 417 | Expectation Failed. Return this if appropriate when supporting 'look-before-you-leap' requests |
| 422 | 'Unprocessable' entity.  See note below. |
| 429 | The rate limit is exceeded |
| 500 | An undisclosed server error occurred. This is a generic 'fail whale' response. |

Note: 422 is sometimes used for _validation errors_ which is permitted by this strategy. However, '400' is preferred since '422' is really a '[WebDAV extension](http://www.webdav.org/specs/rfc2518.html)'.

No. You really should **not** use '418 (I'm a teapot)' :-)

While the above table represents a minimum list for API developers, your middleware will certainly use additional codes, particularly those related to redirects.

#### Support Conditional Requests and Caching

It is recommended that RESTful APIs support conditional requests and caching by including an ETag header within every response to a GET request.  ETags should be used to provide an opaque cache validator using an MD5 or SHA1 hash of the bytes within a representation.

When a representation includes an optimistic lock token (e.g., often labeled as a resource 'version'), this can be used to augment the current optimistic lock verification. That is, since an ETag value will be constructed using the representation that includes the optimistic lock token, clients may use conditional GET requests to support optimistic locking.

A conditional GET request may use the 'If-None-Match' header containing an ETag value returned from a previous GET request. If the service finds the resource still has a representation that has this ETag, the service should return an HTTP status code of 304 instead of returning the resource.  This is intended to reduce bandwidth requirements (although likely won't reduce service processing, as the representation will need to be constructed in order to perform this check).

Similarly, a conditional PUT or PATCH may be used. If the 'If-Match' header is different than an ETag calculated for a representation of the current state of the resource, an HTTP status code of 412 should be returned. Note this will occur if the optimistic lock token that is part of the representation is different. In this case, if a conditional request were not used, the optimistic lock validation will fail and an HTTP status code of 409 should be returned.

To avoid unnecessarily sending a large representation to the server, an API may also support 'look-before-you-leap' (LBYL, as coined by [Richardson and Ruby](http://www.amazon.com/RESTful-Web-Services-ebook/dp/B0043D2ED6/ref=sr_1_1?s=books&ie=UTF8&qid=1370445392&sr=1-1&keywords=restful+web+services)) requests. An LBYL request is either a PUT or POST request that contains no body and includes an 'Expect' header with a value of '100-continue'.  This is useful when determination of whether a PUT or POST 'would' be successful can be made using the current state of the resource or the HTTP headers included in the request. If the server determines the request would not be accepted (e.g., perhaps the request's 'Content-length' header indicated the representation was excessively large) it should return a '417 Expectation Failed' status code.  Support for LBYL requests is not required.

#### Report Validation Errors

To augment use more generic status codes like '400', it is recommended that a custom HTTP Header be used to provide more information. For example, if using a '400' status code for a validation error, a custom HTTP Header can provide additional information:

```
    X-Status-Reason: Validation failed
```

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

Note: When handling a conditional PUT or PATCH request as discussed above, a 412 should be returned.

#### <a id="uri-conventions"></a>Use Common URI Conventions

With respect to URIs, RESTful APIs should:

* Use transparent (readable) URIs unless specific requirements preclude
* Use dashes (aka hyphens) in lieu of underscores (when easily supported by your framework)
* Use dashes (aka hyphens) in lieu of camel case. (This is proposed only for 'API' URLs, and only if supported by your framework.)
* Support nested resources when doing so is logical and only two levels deep
* The use of pluralized resource names is preferred for URLs used to refer to either a collection or a single instance. For example, a list of colleges might be retrieved using 'GET /colleges' while a specific college instance might be retrieved using 'GET /colleges/\{id\}'. (Using singular naming is not incorrect, but using plural naming is also common and may arguably read better.)
* Avoid use of multi-part keys to identify a resource.  If an exposed resource does not have a single, immutable key, attempt to model it as a nested resource.  For example, if exposing items from a checklist, and your legacy data model can only retrieve items by a combination of checklist name and item number within the checklist, prefer exposing items as '/checklists/myList/items/3' as opposed to /items/myList-3, which requires both caller and application to agree on an encoding mechanism for the multi-part key 'myList-3'.

A nice overview of URIs is available at '[http://www.skorks.com/2010/05/what-every-developer-should-know-about-urls](http://www.skorks.com/2010/05/what-every-developer-should-know-about-urls)'.

An example URL compliant with this strategy is:

```
    https://greatvalley.edu/student-course-catalog/api/colleges/college-2453
```

#### <a id="api-versioning"></a>Use API Versioning

It may be important to version APIs in order to provide a stable API to clients.

There are several common approaches used across the industry today for versioning APIs, the most common being to specify a version:

* within the URL
* using a query parameter
* using a custom header
* using a custom media type identified in the Accept and Content-Type headers

It is recommended RESTful APIs use either a custom header or custom media types when versioning is needed.  This versioning should be specified on a resource by resource basis.

When using custom media types, they do not need to be [registered](http://www.iana.org/assignments/media-types/application) nor do they need to correspond to a schema document (although they often may).

Employing custom media types provides flexibility beyond versioning by allowing multiple representations to be used for the same resource. For instance, one could establish 'synopsis' and 'detail' representations with corresponding custom media types to allow a client to ask for a lighter weight or detailed representation of a resource.

Using a custom header, such as 'X-Resource-Version', may be useful when versioning is based upon a versioned catalog of schemas.  It is recommended that [semver](http://semver.org) be used.

_A note regarding the two non-recommended approaches:_

* _Embedding version identifiers within the URL results in URLs that change, even though the 'resource' itself has not changed. This violates a major tenet of API design._
* _Using a query parameter to specify versions is not convenient (as it requires parsing of query parameters)_

An example custom media type consistent with this strategy is:

```
    application/vnd.hedtech.v2+json
```

An API client will likely find no benefit by using minor or patch point releases of an API, as minor and patch releases should be backward compatible. Consequently it may be simpler to use simple  indicators (e.g., v1, v2, v3) to indicate API versions. In some cases, however, it may be useful to indicate a full semver version (e.g., that corresponds to a schema catalog version supported by the API).

_Please reference the '[Use Standard Media Types](#media-types) section of this document for additional discussion regarding media types (that is not focused solely on versioning)._

A final note regarding API versioning: In some cases **it may not be warranted.**  In fact, Roy Fielding states the best practice for versioning an API is: ["Don't"](http://www.slideshare.net/evolve_conference/201308-fielding-evolve/31).

Versioning of APIs introduces complexity both within the service implementation as well as within clients using the API.  If an API is 'relatively stable' (a subjective measure), the day-to-day cost may exceed the potentially very rare need to introduce a non-backward compatible representation of a resource. And, HATEOAS if adopted provides mechansims to introduce changes without versioning.

#### <a id="api-paging"></a>Support Paging
When returning collections, it may be necessary to support paging.  There is no single standard for paging, and this strategy will allow flexibility to allow common frameworks to provide this support.

It is recommended that paging leverage query parameters as shown in the following example:

```
    https://greatvalley.edu/student-course-catalog/api/colleges/?per-page=50&page=7
```

While 'page' and 'limit' are recommended, the specific query parameter names may differ based upon your framework. For instance, Grails uses 'max' and 'offset' and 'page'. 

Please see [Prefer Unwrapped Representations](#prefer-unwrapped-representations) which includes guidance pertaining to how paging should be reflected within responses when returning an array of representations versus returning an envelope.

It is the developers responsibility to provide validation and safeguards. For example, if a client requests 10,000 items per page the service may either reset the 'limit' size to an acceptable pre-configured maximum before handling the request or the service may return a 400 error code.

#### Support Querying
While there is no current standard for querying RESTful APIs, there are important considerations for the product architect.

When feasible, query criteria should be specified using query parameters in a GET request.  For instance, the following URL could be used to retrieve a list of forest science courses for term '200510 Fall 2004'.

```
    https://greatvalley.edu/student-course-catalog/api/courses?subject=FRSC&term=200510%20Fall%202004
```
A more structured alternative to the above uses indexed query parameter 'filters' as shown below.

```
https://greatvalley.edu/student-course-catalog/api/courses?filter[0][field]=subject&filter[1][value]=200510%20Fall%202004&filter[0][operator]=eq&filter[1][field]=term&filter[1][operator]=eq&filter[0][value]=FRSC&max=50
```

Nested resources can be used to provide an elegant RESTful API and may reduce the need for and the complexity of queries or filtering.  Use of nested resources is highly recommended as it can result in very well organized APIs.

The following example shows how a resource may be exposed directly as well as be exposed as a nested resource, to allow a client application to retrieve a list of Forest Science courses for term '200510 Fall 2004'.

```
    GET /courses?subject=FRSC&term=200510%20Fall%202004
    GET /subject/FRSC/courses?term=200510%20Fall%202004
    GET /subject/FRSC/terms/200510%20Fall%202004/courses
```

Note the last example above has deep nesting, which is not recommended. It is recommend that resources only be nested one level. Also note, that same resource (courses) could be accessed as a nested resource under yet another resource

```
    GET /instructor/3895483/courses
```

It is recommended nested resources be used for 'filtering' or to provide conceptual clarity as opposed to truly scoping the nested resource. That is, if a nested resource is explicitly specified on the URL (i.e., the URL specifies the ID of a particular nested resource in addition to the parent resource) it is recommended that ID be unique and usable beyond the parent resource. Following this recommendation ensures all resources can be uniquely identified without nesting (and may simplify framework creation of href and link elements).

Using query parameters in conjunction with nested resources is recommended.  RESTful APIs should specify the queries that may be performed against a resource within API documentation and potentially identify them as affordances within response documents.

Including query (or filter) criteria within a GET request body is not recommended, as while the [HTTP 1.1 specification](http://www.w3.org/Protocols/rfc2616/rfc2616.html) doesn't preclude a GET containing a body there is [no convention in HTTP\/1.1 for safe interaction](http://www.w3.org/2001/tag/doc/whenToUseGet-20040321#safe) when a body is used. Nevertheless, some solutions such as [Elastic Search](http://www.elasticsearch.org) do implement search using this approach.

For situations where complex query criteria must be used that is cumbersome to encode within the URL, a better approach is to model the query as a separate 'query' resource.  This would enable a client application to create a new 'query' resource (using an appropriate representation to codify the query criteria). The POST may result in execution of the query, or the API may require a subsequent GET of the newly created query resource (whose representation should include both the query criteria and the query results).

When query criteria includes Personally Identifiable Information (PII), it must either be encrypted (if it is being passed as query parameters) or a POST must be used as discussed above.  This is because query parameters will likely be recorded within an HTTP server's access log.

#### <a id="prefer-json"></a>Prefer JSON Representations
JSON ([JavaScript Object Notation](http://www.json.org/)) is a data interchange standard that is a subset of the ECMAScript 5 language (JavaScript).  It provides a nice balance between expressiveness and simplicity, and is the preferred representation for RESTful APIs.

XML based representations are also permitted by this strategy, in addition to or instead of JSON when requirements dictate.  Selected APIs may also support other formats in lieu of JSON and or XML when this is beneficial to the clients.

Using representations that support hypermedia is discussed later in this document within section [Support 'Hypermedia' APIs](#representation-hypermedia).

#### <a id="media-types"></a>Accept Both Standard and Custom Media Types
Determining the media type of a request is referred to as 'content negotiation', and is normally based upon the HTTP 'Content-Type' and 'Accept' headers.  For example, a client may submit a request using a 'Content-Type' header of 'application/xml' to inform the service that XML is being used, and also include an 'Accept' header of 'application/json' to request the service to respond with JSON content.

When needed, RESTful APIs should support multiple [application media types](http://www.iana.org/assignments/media-types/application/index.html) by employing content negotiation. This is true when supporting API versioning as [discussed above](#api-versioning).  When a request identifies a custom media type that is not supported, a 415 (unsupported media type) error should be returned.

RESTful APIs should use the 'application/json' (or 'application/xml) media type when neither specification of an API version nor usage of a custom type is needed.

When a client does not specify a custom media type, the latest (current) version of the API should be assumed. That is, a client who prefers to always hit the latest version of the API may simply use the standard media type of: 'application/json'.

If no Accept header is specified, 'application/json' (or 'application/xml') should be assumed (based upon whether the API primary representation is JSON or XML). Recall that JSON is our preferred representation.

In some circumstances it may be more convenient to accept a Content-Type of 'application/x-www-form-urlencoded' with the data encoded as JSON. While not desirable, this is permitted by the strategy when deemed appropriate by the product architect.

While it is preferable for an API to leverage the Accept and Content-Type headers within a request to indicate a versioned or custom representation, the use of custom headers to identify this information is also permitted.  When a versioned or custom representation is indicated, both the custom header and standard header should identify the same custom media type. When custom headers used for content negotiation, it may be required by clients of the API (that is, a service need not fall back to the standard headers). Again, the use of standard headers is preferred.

Using custom media types to enable support of client-specified custom representations of a resource, while not prohibited by this strategy, is not encouraged. That is, RESTful APIs should establish specific representations for resources (albeit versioned as discussed earlier when needed) versus supporting multiple representations (beyond a default JSON and or XML representation).

While JSON is recommended for most endpoints, other representations may be required. For instance, 'ical' may be specified using 'text/calendar' and PDF may be specified using 'application/pdf' (or their custom media types counterparts 'text/vnd.hedtech.v2+calendar' and 'application/vnd.hedtech.v2+pdf' respectively).

_It will be important to periodically revisit the strategy pertaining to media types, to assess emerging best practices and standards (e.g., [WRML](http://www.wrml.org))._

#### <a id="standard-media-types"></a>Respond with Standard Media Types
When a custom media type is used, it is recommended the service return a response using a Content-Type header with the standard 'application/json' (or 'application/xml') media type and an additional custom header such as shown below:

```
    X-hedtech-Media-Type: application/vnd.hedtech.v4+json
```

This will facilitate viewing the response in a browser (e.g., using an add-on to render JSON or XML) or development tool while still communicating the version or custom format to the client.  Returning standard application/json is particularly useful for clients learning about your API, as browser add-ons will enable clients to more easily view the response.

An API may always respond with a custom header, even when identifying a standard media type.  That is, a custom header may be used even if it identifies the same standard media type identified in the Content-Type header.

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
It is preferred by this strategy to accept and return content that is comprised solely of one or more representations, as opposed to wrapping the representation within another structure (e.g., an envelope).  While returning a 'pure' representation is very common when returning a single representation, some services return collections of a resource within another structure in order to include additional information such as paging information, total counts, etc.  Use of such an envelope, while not prohibited, is not recommended.  It is preferable to communicate any additional information such as paging information using HTTP headers. While using a ['Link' header](http://www.w3.org/Protocols/9707-link-header.html) is recommended, it may be more convenient to expose paging information using separate custom headers.

For example, the following depicts returning a collection of 'colleges', with paging information reflected in a Link header:

```console
    $ curl -H "Accept: application/vnd.hedtech.v4+json" -u batman:{PROTECTED} https://greatvalley.edu/student-course-catalog/api/colleges?page=2&per_page=10
    HTTP/1.1 200 OK
    Content-Type: application/json
    X-hedtech-Media-Type: application/vnd.hedtech.v4+json
    Link: <https://greatvalley.edu/student-course-catalog/api/colleges?page=3&per_page=10>; rel="next",
      <https://greatvalley.edu/student-course-catalog/api/colleges?page=1&per_page=10>; rel="prev"
    X-hedtech-totalCount: 317
```

Note the addition of a custom header to communicate the total count. This is not required, but permitted. Alternatively, the following is permitted:

```console
$ curl -H "Accept: application/vnd.hedtech.v4+json" -u batman:{PROTECTED} https://greatvalley.edu/student-course-catalog/api/colleges?offset=2&max=10
HTTP/1.1 200 OK
Content-Type: application/json
X-hedtech-Media-Type: application/vnd.hedtech.v4+json
X-hedtech-pageOffset: 2
X-hedtech-pageMaxSize: 10
X-hedtech-totalCount: 317
```

Regardless, the content is returned as a JSON array of the resources:

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

Alternatively, although not preferred, a small envelope may be used to communicate the additional information (as in the following example):

```console
    $ curl -H "Accept: application/vnd.hedtech.v4+json" -u batman:{PROTECTED} https://greatvalley.edu/student-course-catalog/api/colleges?offset=2&max=10
    HTTP/1.1 200 OK
    Content-Type: application/json
    X-hedtech-Media-Type: application/vnd.hedtech.v4+json
```

The content is returned as a JSON array of the resources:

```json
  {
    "success": true,
    "data": [
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
    ],
    "page": 2,
    "per-page": 10
  }
```

#### <a id='bulk'></a>Bulk Processing (Collections) _(synchronous)_

RESTful APIs may be developed to support 'bulk' processing when warranted. Such APIs expose resources that represent a collection of resources of a single type. Specifically, a 'bulk' API exposes a 'collection' resource that can include multiple resource instances to be handled in one synchronous invocation.  A 'bulk API' is simply an API that exposes a 'collection' resource. Since the 'collection' is represented as a single 'resource' that holds a collection of other resources, no 'bending of the rules' is required to support 'bulk APIs'.

APIs that expose 'collection' resources may support GET, POST, PUT, PATCH, and DELETE following normal REST principlels. However, the use of GET should be supported only if the collection resource has a meaningful ID (as discussed below). Since it is easy to page through a filtered collection of resources without using a 'collection' resource, the only reason to retrieve one or more pages of 'collection' resources is if those collections have true business meaning (and weren't just being used as a mechanism to support 'bulk' operations).

Using a 'collection' resource to represent many items is helpful to enable bulk POST, PUT, PATCH, and DELETE operations even when a collection may not itself have business meaning. GET should only be supported when the collection itself has business meaning (i.e., represents meaningful groupings of other resources).  

_Again, a 'bulk API' (i.e., exposing a 'collection' resource) is not necessary for filtering and paging a large number of individual resources. It is helpful when creating/modifying/deleting a collection of resources in a single operation._

The response should include a representation of the 'collection' resource that includes at a minimum the identifiers necessary to allow a client to subsequently interact with any contained resources individually.

A 'collection' resource should, like any other resource, have a unique ID. However, this strategy allows the use of collection resources that do not have a unique ID (and do not have any business value). In these cases, processing is performed on the items within the collection, each of which must have a unique ID, and the collection itself has no meaning other than being used as a mechanism for issuing a single POST/PUT/PATCH/DELETE request that affecs multiple resources. 

When a collection does not have business meaning (and thus does not have a meaningful ID), it is recommended a '1' be used to represent the ID (as we must retain the normal URL patterns that include an ID). However, in this situation, it is very important to consider the implications of caching (obviously, you will not want collection resources that do not have a meaningful ID to be cached). If a collection has a meaningful ID, GET should be supported to retrieve that identified collection. 

```
\# Note: id of '1' is not really used but included to comply with conventions
PUT /collection-of-persons/1
Content-Type: vnd.hedtech.collection-of-persons.v1+json;charset=utf-8
Accept: vnd.hedtech.collection-of-persons.v1+json
```
```json
{
  "persons": [ { "name": "Bart", "someAttribute": "someValue" },
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

RESTful APIs may be developed to support 'batch' processing when warranted. Such APIs perform work *asynchronously*. Specifically, a 'batch' API supports submission of multiple, independent, asynchronous tasks.

When an API is asynchronous it should respond with a '202 Accepted' HTTP status code.

Following is an example request that shows a 'bulk' collection-oriented resource (see '[RESTful Bulk Processing](#bulk)' above) being created asynchronously. (While 'batch' APIs may often be used for 'collection' resources, a 'batch' API may be used for any resource. That is, an API can be exposed to support 'bulk', 'batch', or both bulk and batch processing.)

```
POST /collection-of-persons
Content-Type: vnd.hedtech.collection-of-persons.v1+json;charset=utf-8
```

```json
{
   "persons": [ { "name": "Lisa", "someAttribute": "someValue" },
                { "name": "Marge", "someAttribute": "someValue" }
              ]
}
```

and an example response that includes a reference to the 'batch' processing:

```
HTTP 1.1 202 ok
X-hedtech-message: collection-of-persons resource accepted
```

```
{
  "_link": "collection-of-persons/12345"
}
```

In this case, where we used a 'batch' API to create a 'collection' resource, that collection resource must be identifiable. Having such an identifier allows the client to issue a subsequent GET:

```
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

**Characteristics of a batch API are:**

* Supports submission of multiple, homogeneous operations against a specific resource using a single RESTful API invocation.
* Adheres to REST principles (as only a single resource is acted upon, albeit that resource may represent a 'collection').
* The 'asynchronous' nature is transparent to the RestfulApiController as it is encapsulated within the backend service to which the controller delegates. The controller only needs to know that a '202 Accepted' is the correct 'success' code.
* May or may not represent a single unit of work (transaction) - depending upon the implementation (as discussed within '[RESTful Bulk Processing](#bulk)' above).

**Special considerations for implementing a batch API include:**

* If it is deemed necessary to expose a resource asynchronously, is there a need to also allow synchronous invocation? If so, a different prefix (e.g., 'aapi/') may be introduced to distinguish asynchronous from synchronous endpoints.)
* An API may expose a 'bulk' collection-oriented resource used in an asynchronous request such that subsequent GET invocations may be used to retrieve the status of the asynchronous work.  If the resource instead represents a single entity (versus a collection), the true 'id' of that resource should be returned if possible.  If this is not possible (e.g., the 'id' cannot be specified until the asynchronous processing completes) then a temporary id may be returned *as long as that 'id' cannot conflict with a real 'id'. If a real 'id' is returned and a client issues a GET using that 'id' before the resource has been created asynchronously, a '404' should be returned.

#### <a id="json-patch"></a>Support JSON PATCH

When supporting PATCH and using JSON respresentations, it is highly recommended that [JSON Patch](https://tools.ietf.org/html/rfc6902) be employed as a standard approach to PATCH. JSON Patch is now supported on most platforms and there are many supporting libraries to facilitate supporting this standard.  

#### <a id="representation-hypermedia"></a>Support 'Hypermedia' APIs

Unfortunately, neither XML nor JSON provide direct support for hypermedia. That is, neither XML nor JSON provides direct support for 'affordances' (or 'Hypermedia Factors') that express hypermedia related metadata.  Consequently, hypermedia APIs must codify this information.

>A design affordance is a design element that communicates how something should be used without requiring documentation.[*](http://blog.apigee.com/detail/restful_api_design_nouns_are_good_verbs_are_bad)

>"When I say Hypertext, I mean the simultaneous presentation of information and controls such that the information becomes the affordance through which the user obtains choices and selects actions." &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Roy Fielding

There is currently no de-facto standard with respect to XML or JSON H-Factors.  HTML5 and XHTML are currently the only standard media types that directly support affordances.  Evolving (and currently speculative) 'standards' like [OData](#odata), [Linked Data Platform](#ldp]), [JSON-LD](http://json-ld.org/spec/latest/json-ld-syntax/), and [JSON hypermedia API language](http://tools.ietf.org/html/draft-kelly-json-hal-03) (HAL) may address this in the future.

When such standards mature, Affordance-Rich Messages (ARMs) may be used to build very generic solutions that "are capable of recognizing and reacting to affordances in the message itself."[*](http://ws-rest.org/2012/proc/a8-1-amundsen.pdf)

For now, RESTful APIs should be developed with a pragmatic approach to incorporate additional links such as 'next' and 'previous' to support a multi-step process.

To support affordances, link relations may be used to represent relationships between resources. The product architect may model link relations using [atom:link](http://tools.ietf.org/html/rfc4287#section-4.2.7) or [HAL](http://blog.stateless.co/post/13296666138/json-linking-with-hal).

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
           "self": { "href": "/colleges/25",
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

####<a id="cors"></a>Support Cross Origin Resource Sharing

Modern solutions are likely to be implemented as single-page web applications comprised of JavaScript modules communicating to backend RESTful APIs residing in different domains.  To facilitate the ability to build such solutions, the API must support cross domain communication.

The preferred (and newer) approach is to support [Cross Origin Resource Sharing](http://www.w3.org/TR/cors) (CORS). CORS support is not required but should be considered by the product architect.

An alternative approach to circumvent cross domain issues is to support JSON-P callbacks. This may be supported by allowing clients to include a '?callback' parameter to GET requests in order for the results to be wrapped within a JSON function. While permitted by this strategy, CORS is the preferred approach.

#### <a id="api-security"></a>RESTful API Authentication and Authorization

APIs will usually need to be protected from unauthenticated access and should also be accessed over an encrypted [TLS](http://en.wikipedia.org/wiki/Transport_Layer_Security) connection to prevent eavesdropping and tampering.

There are many approaches for authentication of API clients, some of which are listed below.  It is imperative the API developer understand security requirements and vulnerabilities, and ensure proper authentication and authorization. API security is not discussed in detail within this guide.

* [JSON Web Token](http://jwt.io)  (**recommended**)
* [HMAC-SHA256](https://en.wikipedia.org/wiki/Hash-based_message_authentication_code) Digest
* [OAuth 2.0 authorization protocol](http://tools.ietf.org/html/rfc6749)
 - [OpenID Connect](http://openid.net/connect/)
* [Basic Authentication](http://en.wikipedia.org/wiki/Basic_access_authentication) (over TLS, for legacy integration)
* [Jasig CAS](http://www.jasig.org/cas) (within a domain)

_Note: [SAML 2.0](http://en.wikipedia.org/wiki/SAML_2.0) does not currently define a standard binding for RESTful API endpoints.  SAML assertions may be supported through integration with a CAS server or an OAuth 2.0 authorization server._

#### <a id="multi-tenancy"></a>Support Multi-Tenancy or Multi-Entity Processing

Codifying tenancy/entity information within the URI path is recommended for on-premise deployments.

```
    /{application-name}/{tenant-name}/api/{pluralized-resource-name}
```

Specifying the tenant/entity within the URL is recommended over the use of a subdomain if installed at client institutions that may refuse to use wildcard DNS entries (e.g., to avoid the higher cost of wildcard SSL certificates versus using standard SSL certificates). Also, wildcard certs are not supported in some older browsers \-- it is the responsibility of the product architect to assess browser requirements.

Using subdomains to support either multi-tenancy or multi-entity processing (MEP) is also permitted.  In fact, the use of subdomains seems to be considered a best practice for two primary reasons: First, using a subdomain to specify a tenant/entity allows the URI path to stay consistent across tenants or entities and thus provides for a consistent API across all tenants or entities.  Secondly, since the FQDN (fully qualified domain name) references a particular tenant/entity, client applications may be configured to support a specific tenant or entity without needing to know anything about multi-tenancy or MEP. Employing subdomains may also facilitate configuration of load balancers and the use of content delivery networks (CDNs).  Use of subdomains is recommended for hosted solutions.

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

## Other REST "Standards"

As mentioned previously, there are many efforts underway to address the current shortfalls with current REST best practices and framework support.

#### <a id="odata"></a>OData

[OData](http://www.odata.org) is a newer OASIS project originally driven by Microsoft.  OData is based upon the Entity 1 Data Model (EDM) from Microsoft's 'Entity Framework'.  While Forrester currently positions OData as speculative, .Net projects that already incorporate EDM may find this a reasonable approach.  Consequently, this strategy does not preclude the adoption of OData.  OData is, however, not required and benefits must be weighed against risk of such early adoption.

#### <a id="ldp"></a>Linked Data Platform

[Linked Data Platform](http://www.w3.org/2012/ldp/wiki/Main_Page) is a W3C working group focused on addressing hypermedia requirements not sufficiently supporting using current REST best practices.

----

## Alternatives to REST

#### Using RPC-Based APIs

RPC-based APIs include those implemented using SOAP as well as native procedure invocations over a network.

_Some may argue that SOAP is 'document centric' and is not the same as RPC. While developers may want to make such arguments, the reality is the 'RPC' is embedded within the document.  This is true regardless of the WSDL style used, including Microsoft's 'document/literal wrapped' pattern._

##### SOAP and WS-* Web Services

*WS-\* should not be employed unless there are specific requirements to support* (e.g., due to a partner agreement or 3rd party service). There are significant disadvantages to using SOAP and supporting WS-* standards, including:

* Requires use of XML (and thus precludes use of JSON)
* Not 'web' based, and in fact 'hides' the web.  While SOAP is usually used over HTTP, it uses only POST which is neither safe nor idempotent. SOAP provides it's own envelope versus using HTTP directly.
* It is *very* complex, with many sometimes-conflicting standards like WS-Addressing, WS-Eventing, WS-Coordination, WS-Reliability, WS-ReliableMessaging, WS-BusinessActivity, WS-Security, WS-BPEL, etc.
* It requires the use of WSDL and in practice requires the use of client frameworks due to higher complexity. There are [multiple WSDL styles](http://www.ibm.com/developerworks/webservices/library/ws-whichwsdl), although Microsoft's 'document/literal wrapped' pattern is the most common and should be employed in most cases.
* While cross platform, support varies greatly across platforms and frameworks (particularly for the various WS-* standards). The [WS-I](http://ws-i.org/deliverables/Default.aspx) (now within OASIS) was formed to mitigate this concern by defining a subset of interoperable standards referred to as the [Basic Profile](http://ws-i.org/deliverables/workinggroup.aspx?wg=basicprofile).
* It was primarily driven by vendors whose primary focus was on selling middleware, versus the community
* Strict typing and validation hinder support of Jon Postel's Robustness Principle (although this isn't specific to SOAP, it is very common within SOAP frameworks)

This does not mean to imply that REST is a replacement for WS-* Web Services.  WS-* standards address a number of areas that are not well suited for REST - such as advanced partial-message encryption, digital signing, asynchronous and reliable messaging, orchestration, etc. While REST APIs can be constructed to address many of these areas (recall section [REST Design Patterns](#restdesignpatterns) above), doing may incur additional complexity akin to using these WS-* standards.

##### Other RPC

While socket-oriented integration has had a reduced role since the emergence of the web, the recent 'Web Sockets' standard will be very important for developing rich 'real time' applications. Web Sockets enable a full duplex, bi-directional channel between the browser and the server and may be used to greatly improve the interactivity of a web application. While tools like Node.js greatly simplify socket programming, architects are expected to justify communication over sockets versus over HTTP.

Architects are cautioned not to allow the use of Web Sockets to diminish the focus on developing RESTful/hypermedia APIs. Web Sockets must be used to enable higher interactivity but not be used as a substitute for RESTful APIs.  (Note: There is work being performed within the community to codify the use of REST principles over Web Sockets.)

#### Asynchronous Messaging

Asynchronous messaging provides a number of benefits and may augment the use of RESTful/hypermedia APIs.  Asynchronous messaging provides:

* Reliability and guaranteed delivery
* Asynchronous 'store and forward' semantics
* Support for the 'Pub Sub' domain (subscription based consumption)
* Support for event-based architectures

However, caution is again necessary to avoid 'Enterprise Application Integration' (EAI) failures such as high complexity and difficult administration of middleware. Web standards such as Atom syndication often provide a more desirable (less complex) approach than propagating the use of message broker middleware within an organization.  Additionally, firewall issues may require special permissions or HTTP tunneling.

In addition, some NoSQL databases including MongoDB and Redis may be used to implement pub/sub semantics. Redis is particularly popular for this purpose. Using Redis is particularly attractive in cases where the key store is leveraged for other purposes such as configuration or caching. The pub sub semantics are often employed internally (not for the purpose of integration but to distribute the work load across asynchronous workers).

Nevertheless, asynchronous messaging solutions may play an important role within the open digital campus.  [AMQP](http://www.amqp.org) is the preferred standard for cross platform asynchronous messaging. Many AMQP solutions exist, including [RabbitMQ](http://www.rabbitmq.com) which is very highly recommended.  [JMS](http://en.wikipedia.org/wiki/Java_Message_Service) is not recommended for integration as this is a 'Java' standard, although JMS may continue to be employed internally within JVM-based solutions. (Note many AMQP providers also support JMS, including RabbitMQ.)

#### Using Relational Database Integration (sans API)

Database integration (that exposes a relational schema and uses SQL) has a number of drawbacks and is no longer recommended for near real-time integration.

Specifically, database-level integration:

* Precludes refactoring/modernization of the database schema (without breaking 'clients')
* Exposes internal data constructs and precludes transformation of data prior to consumption
* Requires additional database connections (which may incur licensing costs)
* Limits deployment and network topology options (unless tunneled through either HTTP or SSH)
* May require database vendor lock-in (due to differences in SQL dialect, extensions, or feature set)
* Hinders modernization of client-server applications as needed business logic cannot readily be moved out of the database into a middle tier
* Allows clients to circumvent public APIs (if introduced) that may provide validation and or important business logic

While direct database-level integration has significant drawbacks, it should be recognized that ETL remains a viable and appropriate integration approach in some situations (e.g, batch integrations, business intelligence, etc.).

----

## <a id="texts"></a>References

* [Hypermedia APIs with HTML5 & Node](http://www.amazon.com/Building-Hypermedia-APIs-HTML5-Node/dp/1449306578/ref=sr_1_1?ie=UTF8&qid=1354201129&sr=8-1&keywords=Hypermedia+APIs+with+HTML5+%26+Node), Mike Amundsen

* [RESTful Web Services](http://www.amazon.com/Restful-Web-Services-Leonard-Richardson/dp/0596529260/ref=sr_1_1?s=books&ie=UTF8&qid=1354201179&sr=1-1&keywords=restful+web+services), Leonard Richardson & Sam Ruby

* Numerous blogs found across the web. _(This strategy document is not an invention, but is a gathering of best practices and insights provided by many across the community.)_ In addition, many APIs (e.g, those from Google, Amazon) provide detailed documentation.

* [JSON Hypermedia API Language](http://tools.ietf.org/html/draft-kelly-json-hal-03), Mike Kelly ([specification](http://stateless.co/hal_specification.html)

* [JSON-LD](http://json-ld.org/spec/latest/json-ld-syntax)

* [Collection+JSON](http://amundsen.com/media-types/collection/format), Mike Amundsen


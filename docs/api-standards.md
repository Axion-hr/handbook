# Axion API Standard - WIP

## What is Axion API Standard?
The Axion API Standard is a set of guidelines on how to design and API when building an AMI (Axion Micro Service).

It is loosely based on [OpenAPI Specification](https://spec.openapis.org/oas/latest.html) so feel free to read that, but this document should be much shorter to get you up to speed and coding faster. This document will skip all the common sense remarks and try to answer questions you actually could have. (example of a common-sense remark: The value for these path parameters MUST NOT contain any unescaped “generic syntax” characters described by [RFC3986](https://spec.openapis.org/oas/latest.html#bib-RFC3986): forward slashes (/), question marks (?), or hashes (#).)

## Media types
We recommend using application/json as a default media type for request bodies and responses.

## Versions
API versioning should be done by using a `major.minor.patch` versionins sheme. Bug fixes, new releases that don't change the feature set should increment the `patch` version. New features should increase the `minor` version and breaking changes should never be introduced. So `major` version should not be increased without scheduling an all-hands meeting and making a small presentation of why are we making breaking changes, how to implement them when using your API and there is a 90% chance you'll be instructed not to do it. Good luck.

## HTTP Methods
We'll be using HTTP methods for CRUD operation in a REST services. The primary or most-commonly-used HTTP verbs (or methods, as they are properly called) are POST, GET, PUT, PATCH, and DELETE. These correspond to create, read, update, and delete (or CRUD) operations, respectively. (Most of the text taken from [here](https://www.restapitutorial.com/lessons/httpmethods.html))

### POST
The POST verb is most-often utilized to **create** new resources. In particular, it's used to create subordinate resources. That is, subordinate to some other (e.g. parent) resource. In other words, when creating a new resource, POST to the parent and the service takes care of associating the new resource with the parent, assigning an ID (new resource URI), etc.

On successful creation, return HTTP status 201, returning a Location header with a link to the newly-created resource with the 201 HTTP status.

POST is neither safe nor idempotent. It is therefore recommended for non-idempotent resource requests. Making two identical POST requests will most-likely result in two resources containing the same information.

Examples:

POST http://www.example.com/customers

POST http://www.example.com/customers/12345/orders

### GET
The HTTP GET method is used to **read** (or retrieve) a representation of a resource. In the “happy” (or non-error) path, GET returns a JSON representation of the requsted object and an HTTP response code of 200 (OK). In an error case, it most often returns a 404 (NOT FOUND) or 400 (BAD REQUEST).

According to the design of the HTTP specification, GET (along with HEAD) requests are used only to read data and not change it. Therefore, when used this way, they are considered safe. That is, they can be called without risk of data modification or corruption—calling it once has the same effect as calling it 10 times, or none at all. Additionally, GET (and HEAD) is idempotent, which means that making multiple identical requests ends up having the same result as a single request.

Do not expose unsafe operations via GET—it should never modify any resources on the server.

Examples:

GET http://www.example.com/customers/12345

GET http://www.example.com/customers/12345/orders

GET http://www.example.com/buckets/sample

Getting one object

`GET <url>/<pluralNoun>/{id}`

Returns one JSON object with response code 200, or an empty 404 reponse.

Getting several objects

`GET <url>/<pluralNoun>?skip=0&take=500&<optionalFilter>=500`

When retreiving a list of objects you alway need to define skip and take or the defualts will be used. 

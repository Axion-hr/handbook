---
layout: default
title: Standard method - List
parent: Standard methods
nav_order: 2
---

# Standard methods: List

In many APIs, it is customary to make a `GET` request to a collection's URI
(for example, `/v1/publishers/1/books`) in order to retrieve a list of
resources, each of which lives within that collection.

APIs **should** generally provide a `List` method for resources and it's purpose is to return
data from a single, finite collection.

>Non-finite collection cannot be measured to support page size arguments, e.g. method which aggreates responses from 2 different APIs or simmilar. This must be covered by custom-method.

List methods are specified using the following pattern:

```
ListBooks(ListBooksRequest) returns (ListBooksResponse) 
```

- The RPC's name **must** begin with the word `List`. The remainder of the RPC
  name **should** be the plural form of the resource being listed.
- The request and response messages **must** match the RPC name, with
  `-Request` and `-Response` suffixes.
- The HTTP verb **must** be `GET`.
- The collection whose resources are being listed **should** map to the URI
  path.
  - The collection's parent resource **should** be called `parent`, and
    **should** be the only variable in the URI path. All remaining parameters
    **should** map to URI query parameters.
  - The collection identifier (`books` in the above example) **must** be a
    literal string.


### Request message (Query params)

List methods implement a common request message pattern:

```proto
message ListBooksRequest {
  page: int
  pageSize: int, 
  filter: string, 
  orderBy: string, 
  ... remaining filter and other parameters ... 
}
```

- The `page` and `pageSize` fields, which support pagination, **must**
  be specified on all list request messages. For more information, see
  [AIP-158][].
  - The comment above the `page_size` field **should** document the maximum
    allowed value, as well as the default value if the field is omitted (or set
    to `0`). If preferred, the API **may** state that the server will use a
    sensible default. This default **may** change over time.
  - If a user provides a value greater than the maximum allowed value, the API
    **should** coerce the value to the maximum allowed.
  - If a user provides a negative or other invalid value, the API **must** send
    an `INVALID_ARGUMENT` error.
- 
 `page` field **must** be included on all list request messages.
- The request message **may** include fields for common design patterns
  relevant to list methods, such as `string filter` and `string order_by`.

### Response message

List methods implement a common response message pattern:

```
message ListBooksResponse {
  Book[]: books, 
  int64: nextPage,
  int64: totalSize
}
```

- The response message **must** include one repeated field  (a list) corresponding to the resources being returned, and **should not** include any other repeated fields unless described in another AIP (for example, AIP-217).
  - The response **should** usually include fully-populated resources unless there is a reason to return a partial response.
- The `nextPage` field, which supports pagination, **must** be included on all list response messages. It **must** be set if there are subsequent pages, and **must not** be set if the response represents the final page. For
  more information, see AIP-158.
- The message **may** include a `int32 totalSize` (or `int64 totalSize`)
  field with the number of items in the collection.
  - The value **may** be an estimate (the field **should** clearly document
    this if so).
  - If filtering is used, the `totalSize` field **should** reflect the size of
    the collection _after_ the filter is applied.

### Ordering

`List` methods **may** allow clients to specify sorting order; if they do, the
request message **should** contain a `string order_by` field.

- Values **should** be a comma separated list of fields. For example:
  `"foo,bar"`.
- The default sorting order is ascending. To specify descending order for a
  field, users append a `" desc"` suffix; for example: `"foo desc, bar"`.
- Redundant space characters in the syntax are insignificant.
  `"foo, bar desc"`, `" foo , bar desc "`, and `"foo,bar desc"` are all
  equivalent.
- Subfields are specified with a `.` character, such as `foo.bar` or
  `address.street`.

<!-- TODO(#220): Add a reference to AIP-161 once it is written. -->

**Note:** Only include ordering if there is an established need to do so. It is
always possible to add ordering later, but removing it is a breaking change.

### Filtering

List methods **may** allow clients to specify filters; if they do, the request
message **should** contain a `string filter` field. Filtering is described in
more detail in AIP-160.

**Note:** Only include filtering if there is an established need to do so. It
is always possible to add filtering later, but removing it is a breaking
change.

# Standard methods: Create

In REST APIs, it is customary to make a `POST` request to a collection's URI
(for example, `/v1/publishers/{publisher}/books`) in order to create a new
resource within that collection.

Resource-oriented design ([AIP-121][]) honors this pattern through the `Create`
method. These RPCs accept the parent collection and the resource to create (and
potentially some other parameters), and return the created resource.

## Guidance

APIs **should** generally provide a create method for resources unless it is
not valuable for users to do so. The purpose of the create method is to create
a new resource in an already-existing collection.

Create methods are specified using the following pattern:

```proto
rpc CreateBook(CreateBookRequest) returns (Book) {
  option (google.api.http) = {
    post: "/v1/{parent=publishers/*}/books"
    body: "book"
  };
  option (google.api.method_signature) = "parent,book";
}
```

- The RPC's name **must** begin with the word `Create`. The remainder of the
  RPC name **should** be the singular form of the resource being created.
- The request message **must** match the RPC name, with a `-Request` suffix.
- The response message **must** be the resource itself. There is no
  `CreateBookResponse`.
  - The response **should** include the fully-populated resource, and **must**
    include any fields that were provided unless they are input only (see
    [AIP-203][]).
  - If the create RPC is [long-running](#long-running-create), the response
    message **must** be a `google.longrunning.Operation` which resolves to the
    resource itself.
- The HTTP verb **must** be `POST`.
- The collection where the resource is being added **should** map to the URI
  path.
  - The collection's parent resource **should** be called `parent`, and
    **should** be the only variable in the URI path.
  - The collection identifier (`books` in the above example) **must** be
    literal.
- There **must** be a `body` key in the `google.api.http` annotation, and it
  **must** map to the resource field in the request message.
  - All remaining fields **should** map to URI query parameters.
- There **should** be exactly one `google.api.method_signature` annotation,
  with a value of `"parent,{resource}"` if the resource being created is not a
  top-level resource, or with a value of `"{resource}"` if the resource being
  created is a top-level resource (unless the method supports [user-specified
  IDs](#user-specified-ids)).

### Request message

Create methods implement a common request message pattern:

```proto
message CreateBookRequest {
  // The parent resource where this book will be created.
  // Format: publishers/{publisher}
  string parent = 1 [
    (google.api.field_behavior) = REQUIRED,
    (google.api.resource_reference) = {
      child_type: "library.googleapis.com/Book"
    }];

  // The book to create.
  Book book = 2 [(google.api.field_behavior) = REQUIRED];
}
```

- A `parent` field **must** be included unless the resource being created is a
  top-level resource. It **should** be called `parent`.
  - The field **should** be [annotated as required][aip-203].
  - The field **should** identify the [resource type][aip-123] of the resource
    being created.
- The resource field **must** be included and **must** map to the POST body.
- The request message **must not** contain any other required fields and
  **should not** contain other optional fields except those described in this
  or another AIP.

### Long-running create

Some resources take longer to create a resource than is reasonable for a
regular API request. In this situation, the API **should** use a long-running
operation (AIP-151) instead:

```proto
rpc CreateBook(CreateBookRequest) returns (google.longrunning.Operation) {
  option (google.api.http) = {
    post: "/v1/{parent=publishers/*}/books"
  };
  option (google.longrunning.operation_info) = {
    response_type: "Book"
    metadata_type: "OperationMetadata"
  };
}
```

- The response type **must** be set to the resource (what the return type would
  be if the RPC was not long-running).
- Both the `response_type` and `metadata_type` fields **must** be specified.

**Important:** Declarative-friendly resources (AIP-128) **should** use
long-running operations. The service **may** return an LRO that is already set
to done if the request is effectively immediate.

### User-specified IDs

Sometimes, an API needs to allow a client to specify the ID component of a
resource (the last segment of the resource name) on creation. This is common if
users are allowed to choose that portion of their resource names.

For example:

```
// Using user-specified IDs.
publishers/lacroix/books/les-miserables

// Using system-generated IDs.
publishers/012345678-abcd-cdef/books/12341234-5678-abcd
```

Create RPCs **may** support this behavior by providing a `string {resource}_id`
field on the request message:

```proto
message CreateBookRequest {
  // The parent resource where this book will be created.
  // Format: publishers/{publisher}
  string parent = 1 [
    (google.api.field_behavior) = REQUIRED,
    (google.api.resource_reference) = {
      child_type: "library.googleapis.com/Book"
    }];

  // The book to create.
  Book book = 2 [(google.api.field_behavior) = REQUIRED];

  // The ID to use for the book, which will become the final component of
  // the book's resource name.
  //
  // This value should be 4-63 characters, and valid characters
  // are /[a-z][0-9]-/.
  string book_id = 3;
}
```

- The `{resource}_id` field **must** exist on the request message, not the
  resource itself.
  - The field **may** be required or optional. If it is required, it **should**
    include the corresponding annotation.
- The `name` field on the resource **must** be ignored.
- There **should** be exactly one `google.api.method_signature` annotation on
  the RPC, with a value of `"parent,{resource},{resource}_id"` if the resource
  being created is not a top-level resource, or with a value of
  `"{resource},{resource}_id"` if the resource being created is a top-level
  resource.
- The documentation **should** explain what the acceptable format is, and the
  format **should** follow the guidance for resource name formatting in
  [AIP-122][].
- If a user tries to create a resource with an ID that would result in a
  duplicate resource name, the service **must** error with `ALREADY_EXISTS`.
  - However, if the user making the call does not have permission to see the
    duplicate resource, the service **must** error with `PERMISSION_DENIED`
    instead.

**Important:** Declarative-friendly resources (AIP-128) **must** support
user-specified IDs.
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
- `page` field **must** be included on all list request messages.
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
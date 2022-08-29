# Pagination

> Based on: https://google.aip.dev/158

APIs often need to provide collections of data, most commonly in the List standard method. However, collections can often be arbitrarily sized, and also often grow over time, increasing lookup time as well as the size of the responses being sent over the wire. Therefore, it is important that collections be paginated.

### Request message (Query params)

Paginated methods implement a common request message pattern:

```
message ListBooksRequest {
  page: int // [1, maxPages]
  pageSize: int, 
  ... 
}
```

* The comment above the `page_size` field **should** document the maximum allowed value, as well as the default value if the field is omitted (or set to `0`). If preferred, the API **may** state that the server will use a sensible default. This default **may** change over time.
* If a user provides a value greater than the maximum allowed value, the API **should** coerce the value to the maximum allowed.
* If a user provides a negative or other invalid value, the API **must** send an `INVALID_ARGUMENT` error.
* `page` field **must** be included on all list request messages
* `page` field **must** have values from \[1, n]

### Response message

Paginated methods implement a common response message pattern:

```
message ListBooksResponse {
  Data[]: data, 
  int64: nextPage,
  int64: totalSize
}
```

* The response message **must** include one repeated field (a list) corresponding to the resources being returned, and **should not** include any other repeated fields unless described in another AIP (for example, AIP-217).
* The `nextPage` field, which supports pagination, **must** be included on all list response messages. It **must** be set if there are subsequent pages, and **must not** be set if the response represents the final page. For more information, see AIP-158.
* The message **may** include a `int32 totalSize` (or `int64 totalSize`) field with the number of items in the collection.
  * The value **may** be an estimate (the field **should** clearly document this if so).
  * If filtering is used, the `totalSize` field **should** reflect the size of the collection _after_ the filter is applied.

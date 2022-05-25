---
layout: default
title: Method - Create
parent: Standard methods
grand_parent: API guidelines
nav_order: 4
---

# Standard methods: Update

In REST APIs, it is customary to make a `PATCH` or `PUT` request to a
resource's URI (for example, `/v1/publishers/{publisher}/books/{book}`) in
order to update that resource.

Resource-oriented design ([AIP-121][]) honors this pattern through the `Update`
method (which mirrors the REST `PATCH` behavior). These RPCs accept the URI
representing that resource and return the resource.

## Guidance

Update methods are specified using the following pattern:

```
UpdateBook(UpdateBookRequest) returns (Book) 
```

- The RPC's name **must** begin with the word `Update`. The remainder of the
  RPC name **should** be the singular form of the resource's message name.
- The request message **must** match the RPC name, with a `-Request` suffix.
- The response message **must** be the resource itself. (There is no
  `UpdateBookResponse`.)
  - The response **should** include the fully-populated resource, and **must**
    include any fields that were sent and included in the update mask unless
    they are input only (see AIP-203).
- The method **should** support partial resource update, and the HTTP verb
  **should** be `PATCH`.
  - If the method will only ever support full resource replacement, then the
    HTTP verb **may** be `PUT`. However, this is strongly discouraged because
    it becomes a backwards-incompatible change to add fields to the resource.


### Request message

Update methods implement a common request message pattern:

```proto
message UpdateBookRequest {
  // The book to update.
  //
  // The book's `name` field is used to identify the book to update.
  // Format: publishers/{publisher}/books/{book}
  Book book = 1 [(google.api.field_behavior) = REQUIRED];

  updateMask = [ list of fields to update ]
}
```

- The request message **must** contain a field for the resource.
  - The field **must** map to the `PATCH` body.
  - The field **should** be [annotated as required][aip-203].
  - A `name` field **must** be included in the resource message. It **should**
    be called `name`.
- A field mask **should** be included in order to support partial update. It **should** be called
  `updateMask`.
  - The fields used in the field mask correspond to the resource being updated
    (not the request message).
  - The field **may** be required or optional. If it is required, it **must**
    include the corresponding annotation. If optional, the service **must**
    treat an omitted field mask as an implied field mask equivalent to all
    fields that are populated (have a non-empty value).
  - Update masks **must** support a special value `*`, meaning full replacement
    (the equivalent of `PUT`).
- The request message **must not** contain any other required fields, and
  **should not** contain other optional fields except those described in this
  or another AIP.

### Side effects

In general, update methods are intended to update the data within the resource.
Update methods **should not** trigger other side effects. Instead, side effects
**should** be triggered by custom methods.

In particular, this entails that [state fields][] **must not** be directly
writable in update methods.

### PATCH and PUT

**TL;DR:** Google APIs generally use the `PATCH` HTTP verb only, and do not
support `PUT` requests.

We standardize on `PATCH` because Google updates stable APIs in place with
backwards-compatible improvements. It is often necessary to add a new field to
an existing resource, but this becomes a breaking change when using `PUT`.

To illustrate this, consider a `PUT` request to a `Book` resource:

    PUT /v1/publishers/123/books/456

    {"title": "Mary Poppins", "author": "P.L. Travers"}

Next consider that the resource is later augmented with a new field (here we
add `rating`):

```proto
message Book {
  string title = 1;
  string author = 2;

  // Subsequently added to v1 in place...
  int32 rating = 3;
}
```

If a rating is set on a book and the existing `PUT` request was executed, it
would wipe out the book's rating. In essence, a `PUT` request unintentionally
wiped out data because the previous version did not know about it.


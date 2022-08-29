---
layout: default
title: Method - Create
parent: Standard methods
grand_parent: API guidelines
nav_order: 3
---

# Standard methods: Create

In REST APIs, it is customary to make a `POST` request to a collection's URI (for example, `/v1/publishers/{publisher}/books`) in order to create a new resource within that collection.

Resource-oriented design honors this pattern through the `Create` method. These RPCs accept the parent collection and the resource to create (and potentially some other parameters), and return the created resource.

The purpose of the create method is to create
a new resource in an already-existing collection.

Create methods are specified using the following pattern:

```
CreateBook(CreateBookRequest) returns (Book) 
```

- The RPC's name **must** begin with the word `Create`. The remainder of the RPC name **should** be the singular form of the resource being created.
- The request message **must** match the RPC name, with a `-Request` suffix.
- The response message **must** be the resource itself. There is no `CreateBookResponse`.
  - The response **should** include the fully-populated resource, and **must**
    include any fields that were provided unless they are input only (see
    [AIP-203][]).
- The HTTP verb **must** be `POST`.

### Request message

Create methods implement a common request message pattern:

```
message CreateBookRequest {
  .. fields ...
```

- The request message **must not** contain any other required fields and
  **should not** contain other optional fields except those described in this
  or another AIP.


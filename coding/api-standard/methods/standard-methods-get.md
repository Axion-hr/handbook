# Standard methods: Get

In REST APIs, it is customary to make a `GET` request to a resource's URI (for
example, `/v1/publishers/{publisher}/books/{book}`) in order to retrieve that
resource.

The purpose of the get method is to return data from a **single resource**.

Get methods are specified using the following pattern:

```
GetBook(GetBookRequest) returns Book 
```

- The RPC's name **must** begin with the word `Get`. The remainder of the RPC
  name **should** be the singular form of the resource's message name.
- The request message **must** match the RPC name, with a `-Request` suffix.
- The response message **must** be the resource itself. (There is no
  `GetBookResponse`.)
  - The response **should** usually include the fully-populated resource unless
    there is a reason to return a partial response (see [AIP-157][]).
- The HTTP verb **must** be `GET`.
- The URI **should** contain a single variable field corresponding to the
  resource name.
- The `name` field **should** be the only variable in the URI path. All
    remaining parameters **should** map to URI query parameters.


### Request message and Query parameters

TODO

- kako slati komplekse parametre, mape itd

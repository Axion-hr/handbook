---
layout: default
title: API standard (archive)
parent: API guidelines
nav_order: 3
---

# Axion API Standard - WIP

{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---


## What is Axion API Standard?
The Axion API Standard is a set of guidelines on how to design and API when building an AMI (Axion Micro Service).

It is loosely based on [OpenAPI Specification](https://spec.openapis.org/oas/latest.html) so feel free to read that, but this document should be much shorter to get you up to speed and coding faster. This document will skip all the common sense remarks and try to answer questions you actually could have. (example of a common-sense remark: The value for these path parameters MUST NOT contain any unescaped “generic syntax” characters described by [RFC3986](https://spec.openapis.org/oas/latest.html#bib-RFC3986): forward slashes (/), question marks (?), or hashes (#).)

## Media types
We recommend using application/json as a default media type for request bodies and responses.

## Versions
API versioning should be done by using a `major.minor.patch` versionins sheme. Bug fixes, new releases that don't change the feature set should increment the `patch` version. New features should increase the `minor` version and breaking changes should never be introduced. So `major` version should not be increased without scheduling an all-hands meeting and making a small presentation of why are we making breaking changes, how to implement them when using your API and there is a 90% chance you'll be instructed not to do it. Good luck.

Suggested approach for method versioning is to start with the version:

`GET /v1/poi?page=0&size=100`

This makes it easier to introduce `/v2/` wihtout any breaking changes. Just leave `/v1/` alive and impelement cool new features on `/v2/`.


## HTTP Methods
We'll be using HTTP methods for CRUD operation in a REST services. The primary or most-commonly-used HTTP verbs (or methods, as they are properly called) are POST, GET, PUT, PATCH, and DELETE. These correspond to create, read, update, and delete (or CRUD) operations, respectively. (Most of the text taken from [here](https://www.restapitutorial.com/lessons/httpmethods.html))

### POST
The POST verb is most-often utilized to **create** new resources. In particular, it's used to create subordinate resources. That is, subordinate to some other (e.g. parent) resource. In other words, when creating a new resource, POST to the parent and the service takes care of associating the new resource with the parent, assigning an ID (new resource URI), etc.

On successful creation, return HTTP status 201, returning a Location header with a link to the newly-created resource with the 201 HTTP status.

POST is neither safe nor idempotent. It is therefore recommended for non-idempotent resource requests. Making two identical POST requests will most-likely result in two resources containing the same information.

Examples:

`POST http://www.example.com/customers`

`POST http://www.example.com/customers/12345/orders`

### GET
The HTTP GET method is used to **read** (or retrieve) a representation of a resource. In the “happy” (or non-error) path, GET returns a JSON representation of the requsted object and an HTTP response code of 200 (OK). In an error case, it most often returns a 404 (NOT FOUND) or 400 (BAD REQUEST).

According to the design of the HTTP specification, GET (along with HEAD) requests are used only to read data and not change it. Therefore, when used this way, they are considered safe. That is, they can be called without risk of data modification or corruption—calling it once has the same effect as calling it 10 times, or none at all. Additionally, GET (and HEAD) is idempotent, which means that making multiple identical requests ends up having the same result as a single request.

Do not expose unsafe operations via GET—it should never modify any resources on the server.

Examples:

`GET http://www.example.com/customers/12345`

`GET http://www.example.com/customers/12345/orders`

`GET http://www.example.com/buckets/sample`

Getting one object

`GET <url>/<pluralNoun>/{id}`

Returns one JSON object with response code 200, or an empty 404 reponse.

Getting several objects

`GET <url>/<pluralNoun>?skip=0&take=500&<optionalFilter>=500`

When retreiving a list of objects you alway need to define skip and take or the defualts will be used. 

### DELETE

Delete should look like this:

`DELETE /v1/poi/{id}?force=false`

When possible deletion should be "soft", i.e. it should set the status of an object to DELETED or PENDING_DELETION.

`force=false` should delete the element only if it has no children, fail and warn if there are.
`force=true` should delete the element and all it's children.

### PUT 

⚠️ We'll avoid implementing PUT in our BFF services because it becaomes destructive once a new field is added and not all clients update the version.

As a general rule unless there is a need for a PUT from a partner (3rd party service) don't implement it.

```
Put looks like this:
PUT /v1/poi/{id}
and completley replaces the element with id `{id}` with the element in the request body.
```

### PATCH

There are 3 approaches for the PATCH... 

- Option 1:

something same as PUT just replaces all the values with new ones

- Option 2: 

Something like [this](https://www.baeldung.com/spring-rest-json-patch) :

`PATCH /v1/poi/{id}`

Body:
```
[
{
    "op":"replace",
    "path":"/telephone",
    "value":"001-555-5678"
},{
    "op":"move",
    "from":"/favorites/0",
    "path":"/favorites/-"
},{
    "op":"copy",
    "from":"/favorites/0",
    "path":"/favorites/-"
},{
    "op":"test", 
    "path":"/telephone",
    "value":"001-555-5678"
}
]
```

- Option 3:

Try to be like Google:

```
Google APIs generally use the PATCH HTTP verb only, and do not support PUT requests.
```

and use the "fieldmask" approach.

Object example:

```
{
  "id":"126",
  "firstName":"Max",
  "lastName":"Smith",
  "language":"de"
}
```

If you wish to update the first name and keep everything else:

```
PATCH /v1/user/126
{
  "firstName":"Maximilian",
  "fieldMask":["firstName"]
}
```

If you wish to update the first name and delete the last name:
```
PATCH /v1/user/126
{
  "firstName":"Maximilian",
  "fieldMask":["firstName", "lastName"]
}
```


Updating the language:
```
PATCH /v1/user/126
{
  "language":"en",
  "fieldMask":["language"]
}
```

The implementation of any API method which has a fieldMask type field in the request should verify the included field paths, and return an `INVALID_ARGUMENT` error if any path is unmappable.


## Paging

We'll often need to break large list into pages so the frotends can get smaller chunks easily.
The suggested approach is described by [Spring](https://www.bezkoder.com/spring-boot-pagination-filter-jpa-pageable/) in detail.
Using `page=<int>` and `size=<int>` path parameters so the frontend can define what it want's to see and returning a structure like this:

`GET /v1/poi?page=1&size=3`
```
{
    "totalItems": 8,
    "pois": [...],
    "totalPages": 3,
    "currentPage": 1
}
```

## Error reponses

Productuion errors exceptions should look like this:
```
{
  "timestamp": "2022-03-22T15:08:42.804+00:00",
  "status": 500,
  "error": "Internal Server Error",
  "message": "This is where the message is",
  "path": "/security/unauthorized/demo/illegalargument"
}
```

Errors in developement environment should look like this:

```
{
  "timestamp": "2022-03-22T15:08:42.804+00:00",
  "status": 500,
  "error": "Internal Server Error",
  "trace": "java.lang.IllegalArgumentException: This is where the message is\n\tat hr.axion.cnf.api.cnfapigateway.web.controller.UnauthorizedDemoController.getIllegalArgument(UnauthorizedDemoController.java:27)\n\tat java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)\n\tat java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:77)\n\tat java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)\n\tat java.base/java.lang.reflect.Method.invoke(Method.java:568)\n\tat org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:205)\n\tat org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:150)\n\tat org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:117)\n\tat org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:895)\n\tat org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:808)\n\tat org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87)\n\tat org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1067)\n\tat org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:963)\n\tat org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1006)\n\tat org.springframework.web.servlet.FrameworkServlet.doGet(FrameworkServlet.java:898)\n\tat javax.servlet.http.HttpServlet.service(HttpServlet.java:655)\n\tat org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:883)\n\tat javax.servlet.http.HttpServlet.service(HttpServlet.java:764)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:227)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:162)\n\tat org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:53)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:189)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:162)\n\tat org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:327)\n\tat org.springframework.security.web.access.intercept.FilterSecurityInterceptor.invoke(FilterSecurityInterceptor.java:115)\n\tat org.springframework.security.web.access.intercept.FilterSecurityInterceptor.doFilter(FilterSecurityInterceptor.java:81)\n\tat org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:336)\n\tat org.springframework.security.web.access.ExceptionTranslationFilter.doFilter(ExceptionTranslationFilter.java:122)\n\tat org.springframework.security.web.access.ExceptionTranslationFilter.doFilter(ExceptionTranslationFilter.java:116)\n\tat org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:336)\n\tat org.springframework.security.web.session.SessionManagementFilter.doFilter(SessionManagementFilter.java:126)\n\tat org.springframework.security.web.session.SessionManagementFilter.doFilter(SessionManagementFilter.java:81)\n\tat org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:336)\n\tat org.springframework.security.web.authentication.AnonymousAuthenticationFilter.doFilter(AnonymousAuthenticationFilter.java:109)\n\tat org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:336)\n\tat org.springframework.security.web.servletapi.SecurityContextHolderAwareRequestFilter.doFilter(SecurityContextHolderAwareRequestFilter.java:149)\n\tat org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:336)\n\tat org.springframework.security.web.savedrequest.RequestCacheAwareFilter.doFilter(RequestCacheAwareFilter.java:63)\n\tat org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:336)\n\tat org.springframework.security.oauth2.server.resource.web.BearerTokenAuthenticationFilter.doFilterInternal(BearerTokenAuthenticationFilter.java:121)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:117)\n\tat org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:336)\n\tat org.springframework.security.web.authentication.logout.LogoutFilter.doFilter(LogoutFilter.java:103)\n\tat org.springframework.security.web.authentication.logout.LogoutFilter.doFilter(LogoutFilter.java:89)\n\tat org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:336)\n\tat org.springframework.web.filter.CorsFilter.doFilterInternal(CorsFilter.java:91)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:117)\n\tat org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:336)\n\tat org.springframework.security.web.header.HeaderWriterFilter.doHeadersAfter(HeaderWriterFilter.java:90)\n\tat org.springframework.security.web.header.HeaderWriterFilter.doFilterInternal(HeaderWriterFilter.java:75)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:117)\n\tat org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:336)\n\tat org.springframework.security.web.context.SecurityContextPersistenceFilter.doFilter(SecurityContextPersistenceFilter.java:110)\n\tat org.springframework.security.web.context.SecurityContextPersistenceFilter.doFilter(SecurityContextPersistenceFilter.java:80)\n\tat org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:336)\n\tat org.springframework.security.web.context.request.async.WebAsyncManagerIntegrationFilter.doFilterInternal(WebAsyncManagerIntegrationFilter.java:55)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:117)\n\tat org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:336)\n\tat org.springframework.security.web.FilterChainProxy.doFilterInternal(FilterChainProxy.java:211)\n\tat org.springframework.security.web.FilterChainProxy.doFilter(FilterChainProxy.java:183)\n\tat org.springframework.web.filter.DelegatingFilterProxy.invokeDelegate(DelegatingFilterProxy.java:354)\n\tat org.springframework.web.filter.DelegatingFilterProxy.doFilter(DelegatingFilterProxy.java:267)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:189)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:162)\n\tat org.springframework.web.filter.RequestContextFilter.doFilterInternal(RequestContextFilter.java:100)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:117)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:189)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:162)\n\tat org.springframework.web.filter.FormContentFilter.doFilterInternal(FormContentFilter.java:93)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:117)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:189)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:162)\n\tat org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:201)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:117)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:189)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:162)\n\tat org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:197)\n\tat org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:97)\n\tat org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:540)\n\tat org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:135)\n\tat org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:92)\n\tat org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:78)\n\tat org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:359)\n\tat org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:399)\n\tat org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:65)\n\tat org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:889)\n\tat org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1735)\n\tat org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49)\n\tat org.apache.tomcat.util.threads.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1191)\n\tat org.apache.tomcat.util.threads.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:659)\n\tat org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)\n\tat java.base/java.lang.Thread.run(Thread.java:833)\n",
  "message": "This is where the message is",
  "path": "/security/unauthorized/demo/illegalargument"
}
```

---
layout: default
title: Resource oriented design
parent: Axion API guidelines
nav_order: 1
---

## Project structure

There is no universally set guideline how to name and structure packages in the project, 
this can of course vary case to case due to multiple reasons ranging from project size, function of the project, 
weighted complexity and any other reason. But there are two common approaches when building 
services to take:
- domain separation - basically bundling multiple layers of the application based on the domain 
that they are serving
- functional separation - bundling files based on a logical layer and the function that they are performing

For the default structure we have chosen the functional separation as we are mainly in the business of 
building microservices, and they tend to be rather small in footprint having multiple layers of the system 
around the same domain. Of course this is not set in stone so if you feel that there is a use-case where we 
need to another approach please consult your architect :D

Depending on the components the functional separation would be something like

```
src
- main
-- java
--- hr.axion.project
---- controller
---- dto
---- exception
---- model
---- provider
---- repository
---- security
---- service
---- util
-- resources
- test
-- java
```

## Libraries 

### Lombok
One of the biggest complaints against Java is how much noise can be found in a single class. Project Lombok saw this as a problem and aims to reduce the noise of some of the worst offenders by replacing them with a simple set of annotations.

Most used annotations
- @Getter/@Setter - adds getter / setter
- @ToString - adds toString 
- @EqualsAndHashCode - adds equals and hashCode
- @Data - adds everything
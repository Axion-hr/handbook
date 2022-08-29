# Spring Boot guidelines

In order to structure the code that we deliver to our partners, and to make our 
lives easier we have agreed on a set of guidelines on coding practices, libraries, 
configurations. This will enable that we collaborate both externally and internally 
between the projects.

All the guidelines are promoted from the proposals, and will be maintained in order
to follow all the best practices. 



# Project structure

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
---- client
---- controller
---- dto
---- entity
---- exception
---- repository
---- security
---- service
---- util
-- resources
- test
-- java
```

# Proposals
[proposals][]



[proposals]: ./proposals.md
# Authorization and authentication

## 1. Using industry standard technologies

User access is a standard problem that has many verified solutions on the market. We should always use standard solutions from verified vendors that have a good reputation. Some of the acceptable solutions include:

* AWS Cognito
* RedHat SSO (KeyCloak)
* Auth0
* Microsoft Identity Provider

In addition to that most of the more popular solutions also provide a proprietary API and SDKs, using them is acceptable but for foundational services and infrastructure components we aim to use standard protocols e.g. OAuth.



## 2. Auditable user profile data and access permission changes

Changes to the user roles and rights needs to be auditable, the audit process can be defined per technology used and the customer internal processes and requirements.

Changes to the user information e.g. email, password, name, PII data needs to be auditable, the audit process will be defined for each implementation separately depending on the project and customer requirements and processes.



## 3. Least privilege access to resources and data

Every user action needs to be verified both for level of access for a specific resource and entity. The validation must happen on the server side with the finest granular access control on the service that is responsible for the domain.

In a complex enterprise microservice environment authorization should already happen on the network entry (e.g., API gateway) preventing non-identified users from accessing any system. A passthrough service may check authorization of a user for a certain client, and the domain service must check access for a specific resource (action) and access for an entity if applicable. In this context we refer to entity an exact object (object tree) where the user has specific set of rights.

It is recommended to use standard OAuth protocol together with roles and claims (or similar).



## 4. Internet available web applications should use ‘double opt-in’ procedure

It is highly recommended that any application that is available and can be used over the Internet implements double opt-in procedure for registration and forget password procedure. If the application uses email as an identifier it needs to send a confirmation link to the new users email with the unique link that has a short time to live before confirming the new users’ account.

In the case where the system doesn’t use any easily verifiable information, we can implement Captcha test. Captcha test will mitigate automatic attacks as they can distinguish between machines and human operators with relatively high accuracy. &#x20;



## 5. MFA on sensitive applications

Applications that contain and process sensitive data or that have extensive rights should if feasible implement and enforce Multi Factor Authentication. The first factor is usually the user’s password (something that the user knows) and the second factor is mostly something that the user owns. The most common example of second factor is OTP on a mobile phone like Google Authenticator.

In choosing the right ‘implementation’ we need to take a variety of factors into the account from the ‘relevance’ of the protected resource to the usability. The standard approach is to use software MFA, but to protect critical resources it is recommended to also explore hardware tokens e.g., YubiKey



## 6. Re-authentication of user for critical actions

When a user needs to perform a critical action, the application should perform the re-authentication before action execution. Critical actions could be changing of password, changing of email, perhaps even changing payment methods. The critical actions should be defined by the product team.

&#x20;This is done to ensure that the attacker didn’t hijack the session or taken control of the victim device.



## 7. Registration, login and forget password enumeration

Events where an application user can request actions based on real world identifications of a person are subject to a variety of enumeration attacks.

For example, forget password action could be used to verify if a person is registered on the application. When there is a request for a forget password, the application must give an ambiguous reply not reviling if the user is existing or not (e.g., if an email is registered, we will send a verification link).

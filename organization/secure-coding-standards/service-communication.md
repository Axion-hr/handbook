# Service communication

## 1. Server application must validate all data transferred by the client <a href="#validating-input-on-the-server" id="validating-input-on-the-server"></a>

In this process, all the data must be checked regarding data type, length, acceptable characters, format, value range, encoding and coding.

“Rich input” (e.g., XML, HTML, etc.) shall be checked for both correct structure and validity of the data contained in the structure.

Validation must implement validation whitelisting principle, basically checking the validation against the permitted characters, types, formats. Any non-whitelisted character, type, format is automatically rejected. This is in the contrast to blacklisting where we would enumerate not allowed characters, types, format but in this option, there is a high possibility that the case is not defined, and it would pass the validation even if it shouldn’t.

This applies to all data that a user has entered, for instance URL and POST parameters with the relevant data. However, this also applies expressly for data that the user does not influence during normal use, e.g., cookies, data from local storages, hidden fields, variable names, HTTP header fields, etc.

The same principle also applies for applications that are called from different backend applications or ingest media provided by different applications (e.g. reading from file). Each service must enforce data validity and prevent any malformed or malicious data to be processed and stored

_Note_: for a good UX applications may also validate user input on the client side but this is not to be trusted as it can easily be exploited by the attacker.



## 2.  Server application must prevent SQL / NoSQL injection

This applies both to SQL injection in the case of SQL databases and to, for example, CQL or JavaScript injection in the case of NoSQL databases.

For standard implementations services must use industry standard ORMs and not build-up SQL statements from requests. Properly using the ORM will prevent the SQL injection attack as the code maps the params to the object and ORM takes care to have bind variables when creating SQL / NoSQL commands.

For SQL and CQL requests, all variables need to be bound in prepared statements. If stored procedures are used, these must also be used with bind variables. Dynamically combined requests are not to be used.



## 3. Server application must reject invalid requests

Any request that is invalid for any reason like invalid input or authorization needs to rejected. The validation and rejection must happen before any request processing is done. If any processing happened before rejection all the changes need to be rolled back and the event should be logged to track any suspicious behaviour.



## 4.  Service API resources must be protected by default

Service APIs must be designed with security in mind and must be protected by adequate authentication/authorization mechanism by default. Public methods should be made as an exception after careful analysis by the security expert and solutions architect.

In addition to that if possible, resources and responses should be protected to return only the data that is permitted for the user accessing the resource.

For instance; if we need to have an API endpoint for retrieving user details, the API should receive a token from that user and return only the data for the caller regardless of the input parameters. In a case that a user tries to cross foundries and access other user data the service must block such a call.



## 5. Service boundaries

Service solutions must be build in a way that services don’t cross other service boundaries in regards to resource access. Service must encapsulate all access and modifications for its own domain. Any cross boundary interaction needs to happen over agreed interfaces.

For instance, a service must have its own database (or database schema), where it is responsible for structure and all the data in its data store. In the case it needs information from the other service it needs to fetch it over the agreed interfaces e.g. APIs.



## 6. Preventing code and command injections

Service must not use any user input data to create shell commands or commands for the active program. This applies on both the server and client applications.

For instance, with the code injection an attacker could send the executable code via API. The service could execute the input directly on the server and thus potentially give an attacker control over the server and access to the data.



## 7. Protection of sending email and SMS without user authentication

Often client-side applications have the possibility to send out the email/SMS to customer prior to authenticating and validating the user. This functionality can be found in contact forms where the email is send to the specific email address, or even worse when validating the user email address.

In this case we need to implement a range of security measures to prevent the exploit and its implementation requires to be validated with solution architect and security officer.

When implementing such a solution we need to:

* Implement a reasonable number of retires before blocking the function to customer
* Within retries implement a feature to disable bots e.g. captcha
* Implement cool down periods between retires
* Implement a measure to prevent mass sending to multiple addresses by limiting a number of different target locations (emails or phone numbers). The number should be limited to 2-3 targets



## 8. Prevent enumeration attacks

Enumeration attacks come in many different forms, but this attack consists of enumerating through a series of potential results and using an API to validate the results or find the correct one.

The possibilities imply:

* Sending incrementing identifiers – e.g., the application can send the invoice number using incremented primary key, it is easy for the attacker to guess that there is an (n-1) invoice if they received it under number n. The attack can be mitigated using uuid as an identifier
* Trying to use “forget password” with a list of emails – attacker is trying to confirm which of the emails are used on the site. The attack can be mitigated by giving ambiguous message as “If we find the requested account we will send you an email”
* Brute forcing the password – the attacker can try to send any number of user/password combinations. The attack can be mitigated by blocking a request or creating an artificial time buffer between retries after a certain number of attempts



## 9. Implement robust Error/Exception handling

Services by default are often designed to give clients access to an exposed API via HTTP or similar. In the case of raised Exception most of industry leading frameworks with production profile hide the exception details to the caller, but not all of the Exceptions are handled by default. If exposed the attacker could use the Error/Exception details especially details listed in the stack trace.

Attackers can attempt to generate these stack traces by tampering with the input to the service with malformed HTTP requests or other input data. If stack traces are exposed they can be used in further attacks and they can reveal internal structure and framework used for the service.

OWASP defines this under: [WSTG-ERRH-01](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web\_Application\_Security\_Testing/08-Testing\_for\_Error\_Handling/01-Testing\_For\_Improper\_Error\_Handling)


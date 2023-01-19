# Additional standards

## 1.  Handling application secrets

Services must not store application secrets in config files that are stored in version control, but rather they must receive it on deployment or when run. This can easily be achieved that the service read environment variables, env variables can only be read if a person has a direct access to the system

Mobile applications and such must store application secrets in OS vaults, which are encrypted and can’t be read by users or other applications.

Web applications in general should not store secrets as they need to be transmitted to a client device and can be accessed by the attacker. If there is a need to store secrets like API keys or similar, they should be kept in .env file instead of written in the code.



## 2.  Insecure deserialization

Deserialization. Is a process of taking the structured data from some format, and rebuilding it into the object e.g., JSON, XML.

For deserialization it is strongly recommended that the native framework capabilities are used instead of trying to parse the data manually or by self-built functions. Each of the framework additionally offers a safer way to handle deserializations either by sandboxing the execution or using functions that are limited in functionality and access to the rest of the system.

OWASP offers a good overview of the attacks and options how to properly implement in popular frameworks: [https://cheatsheetseries.owasp.org/cheatsheets/Deserialization\_Cheat\_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Deserialization\_Cheat\_Sheet.html)



## 3. Vulnerable dependency management

Most of the projects use third-party dependencies to delegate handling of different kind of operations, e.g. HTTP communication, logging, data access, etc.

This is a good approach as the development team needs to focus on delivering on the real application code supporting the expected business feature. The dependency brings forth an expected downside with potential security vulnerabilities.

It is critical that we ensure that all third-party dependencies are clean of known CVV and that the dependencies are updated regularly. It is also recommended that we have an automated analysis of the dependencies and their known vulnerabilities.

OWASP reference: [https://cheatsheetseries.owasp.org/cheatsheets/Vulnerable\_Dependency\_Management\_Cheat\_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Vulnerable\_Dependency\_Management\_Cheat\_Sheet.html)



## 4.  Logging of business critical and security events

All applications should log relevant events in a machine-readable format with ‘connected’ logging information based on events. Machine readable events are usually based on the formats like logfmt or json that is easily parsed by industry standard log aggregation systems.

Application logs are invaluable data for:

* Identifying security incidents
* Monitoring user behaviour
* Monitoring system behaviour
* Identifying errors and edge cases
* Driving business decisions based on monitoring reports
* Driving infrastructure decisions

Application logs are the foundation of system observability and must be implemented in all applications, network equipment, servers and all other component that supports the running system.

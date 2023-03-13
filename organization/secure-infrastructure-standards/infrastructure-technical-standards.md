# Infrastructure technical standards 

# Separation of environments 

Axion advises to build and run multiple environments for all the projects, the target separation dictates at least 3 different environments. The separation is necessary in order to properly secure the data and ensure controlled promotion of changes in the system both functional and configurations.  

Preferably environments should be defined using infrastructure as code principle. This allows that all changes are properly tested before promotion on higher environments and that all environments have at least possible drifts from the target structure.  

We advise to create totally separate: 

* Development environment that is used for development. 
* Test that is built as a copy of the production environment and used to verify that the introduced changes implement a desired outcome and don’t introduce a negative side effect. In physical deployments QA should use the same equipment (both vendors, models and years) as production so it provides a proper testing foundation for the changes.
* Production environment serving end customer requests.

Different environments must implement different access controls and monitoring but should always follow least access principle.  

 

## Reduction of external attack surface 

Direct access to system components especially administrative access must be blocked off from the internet. This entails direct access to the database, storage servers, application servers, networking equipment. In practice it would require that all incoming traffic on all ports is blocked on entry except of HTTPS access to relay servers and secure connection for jump or VPN server. 

It is critical that all communication is encrypted and that all externally available systems are hardened and regularly patched.  

## External access to infrastructure resources 

In order to access infrastructure resources, we utilize a jump server, in a production environment we have a dedicated jump server, other environment can share a single jump server.  


All jump servers need to minimally comply with: 
* Use a CIS v1 hardened image 
* Enforce strict protocol access 
  * SSH access (use non-standard port), 
  * RDC behind VPN
  * any other communication must be encrypted and pass a security clearance 

 * If feasible firewall should implement IP Geofencing for access 
 * Regularly patched and upgraded 
 * If feasible limit the countries for incoming traffic 

Jump servers must be properly monitored and logs should be sent to an external tool. Minimal monitoring must cover: 
* Access logs 
* Failed access attempts 
* Changes to the system files 
* System metrics (CPU, memory, network) to detect anomalies 
* Changes in users (adding, removing, change of password) 

## Monitoring 

In order to have insights to the running systems, both in production and all other environments it is crucial to have aggregated structured logs and metrics. When multiple data points can be correlated, we can extract valuable system behavioral data and detect anomalies.  

To gain insights we must ensure: 
* Central log aggregation – all the logs with structured data need to be aggregated in the single tool 
* Central metrics aggregation – metrics should be added to the same system that aggregates the logs. Correlated metrics can contribute insights on the effects on the system resources 
* Infrastructural monitoring – infrastructure components should be monitored to ensure proper functioning of physical components 

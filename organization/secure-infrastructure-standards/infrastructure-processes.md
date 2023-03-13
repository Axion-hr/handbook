# Infrastructure processes

## User access

Limiting access to production system and application is a significant aspect of security. Unrestricted and unmonitored access to production systems could adversely impact company business and financials. 

Access to production must by regularly reviewed and given to only employees that need to run the system. In the case of employee role change (especially on termination) the access needs to be reviewed immediately.  

User access need to implement 
* MFA (preferably hardware) 
* Strict password policy (use long passwords with minimum od 128 bits of entropy) 
* Disable any password hints 
* Verification of user in the events of password resets, provisioning tokens and such 

All access given to employees must be temporary (where possible).

# Emergency access 

In order to cope and enable restoration regular operations during major incident we must ensure a process and responsible people who can execute emergency access. The incident can vary from technical degradation of service to operational personnel incident.  

Emergency access should: 
* relay on least possible depended systems (e.g. not rely on AD connection but implement local user accounts.) 
* be hosted independently from corporate infrastructure, keeping the operational documentation and access credentials either as a hard copy or on a separate system 
* where feasible be limited also by physical parameters not only technical. For example, be available only from a certain IP address, or use hardware tokens  
* be fully auditable to prevent any misuse (as emergency access gives full access to the system it could be misused) 

# Restrict physical access 

Implementation of the security policy will greatly vary on the infrastructure setup. The purpose of the requirement is to disable anyone from gaining unauthorized access to critical systems and data using physical means. Technical implementation can vary from disk encryptions, locked assets to having all infrastructure in reputable data center, where access is logged and allowed to a predefined set of employees.

# Backup policy and backups 

Axion advises to implement a backup guideline for production systems (both internal and customer facing). The backup policy should define backup classes of different assets including RPO, RTO times and retention policies.  

We recommend implementing 3-2-1 backup strategy, having: 
* A local online backup of the latest version, used to quickly restore the asset 
* A remote version rolling online backup, used to fetch previous versions or in a case of incident can be used to restore the system operations. Access to this resource must be separated and ideally given to different people then production administrative access 
* Offline backup – used as the last resource 

In addition to backup policy, it is recommended to create restoration guideline for the critical systems and practice different incident scenarios with the team.

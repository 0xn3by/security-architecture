### Security Requirement

security requirements are connot be written until the system boundaries are 
understood.

# Security context diagram

for a context diagram, consisting:
* external actors
* the saas platform
* external  systems

# Data asserts transfer from 

SAAS <<<>>> idp (two way)
> these are the three main thing a user attain with a successfull login
- id_token (ID token): contains user details like who is the user for authentication.
- user identity: contains user name, email, pfp from 0auth.
- access_token (Access token): for authorization what access does the user has. 

SAAS <<<>>> SIEM (one way)
the most important log/event to look for is 
- auth events/logs: if a user becomes/grants admin access then the category of the event 
called as role/permission change is what the risk is logged in the audit.
- Role/permission changes
- User activity events : mainly responsible for unauthorized access to another tenant's reasources.
- Security alerts
- System health/monitoring events

Users/ Org Members <<<>>> SAAS (two way)

# Data Flow

# Threat Modeling




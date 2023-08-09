# Sesco VPN

This document is to provide information about the Sesco VPN (Virtual Private 
Network).


This is a quick look at the steps and logic for a VPN connection. This shows a
successful connection. For example the user/pass is not valid the request would
be stopped at kilowatt and returned to the VPN Client.
```
+----------+           +------+       +--------+        +-----+         +--------+
|VPN Client|           |Meraki|       |kilowatt|        |Azure|         |User MFA|
|   User   |           |router|       | RADIUS |        | AD  |         |        |
+----------+           +------+       +--------+        +-----+         +--------+
     |                    |               |                |                |     
     |Start VPN connection|               |                |                |     
     |------------------->|               |                |                |     
     |                    |Check user/pass|                |                |     
     |                    |-------------->|                |                |
     |                    |               |Check M365 rules|                |     
     |                    |               | Cert required  |                |     
     |                    |               |--------------->|                |
     |                    |               |                |Send MFA request|     
     |                    |               |                |--------------->|
     |                    |               |                |  MFA response  |     
     |                    |               |                |<---------------|     
     |                    |               |   Check MFA    |                |     
     |                    |               |<---------------|                |
     |                    |  Allow/Deny   |                |                |     
     |                    |  connection   |                |                |     
     |                    |<--------------|                |                |
     |     Allow/Deny     |               |                |                |     
     |     connection     |               |                |                |     
     |<-------------------|               |                |                |
+----------+           +------+       +--------+        +-----+         +--------+
|VPN Client|           |Meraki|       |kilowatt|        |Azure|         |User MFA|
+----------+           +------+       +--------+        +-----+         +--------+

```



## Meraki VPN edge
---
  
The Meraki routers are configured for client VPN connections. This configuration
is on the Meraki page under "Security & SD-WAN" -> "Client VPN". Once here you 
will be able to see, or change, the IPSec Settings or AnyConnect Settings. This 
is where you will configure the RADIUS server such as the shared secret.

## RADIUS
---

Quick Info
- Server: kilowatt.sesco.com

The RADIUS server that we are running is on Windows Network Policy Server. This
connects with Azure AD Conditional Access and Multi-factor authentication.

### Windows Network Policy Server (NPS) Links
[AD VPN with Azure MFA using NPS](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-nps-extension-vpn)

[Azure AD MFA Getting Started](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-getstarted)

[Azure AD MFA NPS extension install](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-nps-extension#download-and-install-the-nps-extension-for-azure-ad-mfa)

### Update NPS MFA to Azure Cert

The kilowatt to Azure connection uses a self signed cert. This cert has a max 
life of two years. So every year on the first weekday of June this cert will 
need to be updated.

### NPS Logs and Events

Logs for RADIUS NPS:
- C:\Windows\System32\LogFiles

In the Windows Event Viewer see this location for logs from AzureMfa.
- Event Viewer -> Applications and Services Logs -> Microsoft -> AzureMfa -> AuthZ

## Azure Portal

Quick Info
- [Link](https://portal.azure.com/)


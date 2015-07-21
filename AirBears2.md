# Connecting to the AirBears2 Wireless

1. Create an AirBears2 key following [these instructions](http://ist.berkeley.edu/airbears)
1. Under network manager, select "Add" and create new wireless connection
1. Use the following settings under "Wi-Fi Security"
  - Security: WPA & WPA2 Enterprise
  - Authentication: Protected EAP (PEAP)
  - Anonymous identity: your CalNet ID
  - CA certificate: AddTrust_External_Root.pem (in `/etc/ssl/certs/AddTrust_External_Root.pem` in Ubuntu)
  - PEAP version: Automatic
  - Inner authentication: MSCHAPv2
  - Username: Calnet ID
  - Password: Token created in step 1.
  
## Connecting to `eduroam`

In principle, the same connection settings should work on `eduroam` (found across many academic campuses), with 
the only modification being the use of `@berkeley.edu` after the CalNet ID.  **However, this is not working for me at this time**

----

## System configuration notes 

These instructions based on Ubuntu 15.04 using default network manager app to manage
wireless networks.

## Alternate advice

See [alternate instructions](http://w.astro.berkeley.edu/~domagalski/linux/) based on KDE/wicd from Rachel Domagalski 

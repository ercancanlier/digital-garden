---
title: API Connect Troubleshooting
linktitle: API Connect Troubleshooting
description: All about API Connect Trouleshooting
tags: ["troubleshooting", "apiconnect"]
---
## APIC is not up and components should be up in order!

1. backrest and postgres

2. pgbouncer

3. lur

4. juhu

5. ldap

6. nats

7. stan

8. taskmaager

9. mgmt-ui

10. portal proxy analyt proxy client dowload server

11. websocket proxy 

12. apiconnect

13. turnstile

14. hub

## API Protection error while publishing APIs on 10.0.5.4

1. Check api_protection is enabled for webhook: apicops debug:info for all domains.

2. Reregister gateways and apply the steps below:

https://www.ibm.com/docs/en/api-connect/10.0.5.x_lts?topic=acgs-dynamically-re-registering-reconfiguring-gateway-service-in-kubernetes-deployment

3. Edit GW Services summary to send an update and test management-gateway communication.

4. Check tasks in API Manager Catalog Settings. There should not be any queued tasks.
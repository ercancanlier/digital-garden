---
title: API Connect Form Factor Migration
description: API Connect Form Factor Migration
tags: ["apiconnect"]
---

# Migrating from v10 to v10 on a different form factor

## Reference

https://www.ibm.com/docs/en/api-connect/10.0.5.x_lts?topic=connect-migrating-from-v10-v10-different-form-factor

## Prerequisites

1. Install OCP.

2. Create secret with entitlement key.

3. Install  pyyaml

```bash
pip3 install pyyaml.
```

4. Install apic cli.

5. Ensure no connectivity between Production and DR sites.

## Form Factor Migration Steps:

1. Prepare source system: 

```bash
python3 save_v10_source_configuration.py -u admin -p  -s cpd-cp4i.apps.cp4int.example.com/integration/apis/apic/apic -r admin/default-idp-1 -n apic 
```

2. Create secrets in target:

```bash
python3 create_secrets_in_target.py -n apic
```

3. Prepare top level CR:

```bash
oc get apiconnectcluster apic --show-managed-fields=false -o yaml
```

**Top Level CR**

```yaml
apiVersion: apiconnect.ibm.com/v1beta1
kind: APIConnectCluster
metadata:
  labels:
    app.kubernetes.io/instance: apiconnect
    app.kubernetes.io/managed-by: ibm-apiconnect
    app.kubernetes.io/name: apiconnect-production
  name: apic
  namespace: apic
spec:
  analytics:
    storage:
      enabled: true
      type: unique
  license:
    accept: true
    license: L-RJON-CEBLEH
    metric: VIRTUAL_PROCESSOR_CORE
    use: production
  imagePullSecrets:
    - ibm-entitlement-key
  gateway:
    adminUser: {}
    apicGatewayPeeringTLS: {}
    apicGatewayServiceTLS: {}
    gatewayEndpoint:
      hosts:
        - name: apidr.example.com
          secretName: apigwcustom-cert
    gatewayManagerEndpoint: {}
  imageRegistry: cp.icr.io/cp/apic
  management:
    customApplicationCredentials:
    - name: atm-cred
      secretName: apic-mgmt-atm-cred
    - name: ccli-cred
      secretName: apic-mgmt-ccli-cred
    - name: cui-cred
      secretName: apic-mgmt-cui-cred
    - name: dsgr-cred
      secretName: apic-mgmt-dsgr-cred
    - name: juhu-cred
      secretName: apic-mgmt-juhu-cred
    - name: cli-cred
      secretName: apic-mgmt-cli-cred
    - name: ui-cred
      secretName: apic-mgmt-ui-cred
    databaseBackup:
      credentials: mgmt-backup-secret
      host: sftp.example.com
      path: /
      port: 22
      protocol: sftp
      restartDB:
        accept: false
    encryptionSecret:
      secretName: apic-mgmt-enc-key
    name: apic-mgmt
  portal:
    adminClientSubjectDN: ''
    portalAdminEndpoint: {}
    portalUIEndpoint:
      hosts:
        - name: devportaldr.example.com
          secretName: portalcustom-cert
    encryptionSecret:
      secretName: apic-ptl-enc-key
    portalBackup:
      credentials: mgmt-backup-secret
      host: sftp.example.com
      path: /
      port: 22
      protocol: sftp
  profile: n3xc14.m48
  storageClassName: ocs-storagecluster-ceph-rbd
  version: 10.0.5.0

```
 
4. Create apigwcustom-cert certificate in OCP.

5. Create portalcustom-cert certificate in OCP.
 
6. Install target system with python script:

```bash 
python3 install_apic_on_ocp.py 
-license L-RJON-CEBLEH
-n apic 
-name apic 
-storageclass_apic ocs-storagecluster-ceph-rbd 
-cp4i
-storageclass_pn ocs-storagecluster-ceph-fs 
-license_pn L-RJON-CD3JKX 
-production 
-profile n3xc14.m48
```
 
7. Wait for operands to be installed.
 
8. Login to Cloud Manager and crete organization.
 
9. Ensure that apic is installed on path
 
10. Accept the licence and check apic login before restoring the target system:

```bash
apic login --realm admin/default-idp-1 --username admin --server apic-mgmt-platform-api-apic.apps.cp4intdr.example.com --password 
```

11. Restore target system:

```bash
python3 restore_management_db.py -n apic -s apic-mgmt-platform-api-apic.apps.cp4intdr.example.com
```

12. Change route settings in OCP:

apic-gw-gateway-apic.apps.cp4intdr.example.com -> apidr.example.com
 
apic-gw-gateway-apic.apps.cp4intdr.example.com -> devportal.example.com
 
13. Prepare gateway_portal_mappings.yaml:

```yaml
analytics_mapping:
  analytics-service:
    https://apic-a7s-ai-endpoint-apic.apps.cp4int.example.com: https://apic-a7s-ai-endpoint-apic.apps.cp4intdr.example.com
gateway_mapping:
  api-gateway-service:
    https://api.example.com/: https://apidr.example.com/
    https://apic-gw-gateway-manager-apic.apps.cp4int.example.com: https://apic-gw-gateway-manager-apic.apps.cp4intdr.example.com
portal_mapping:
  portal-service:
    https://apic-ptl-portal-director-apic.apps.cp4int.example.com: https://apic-ptl-portal-director-apic.apps.cp4intdr.example.com
```
 
14. Prepare provider organizations credentials.yaml

```yaml
provider_org_credentials:
  apiManagerHostName: https://apic-mgmt-platform-api-apic.apps.cp4intdr.example.com
  api:
    password: 
    realm: provider/default-idp-2
    username: admin
  useSameCredentialsForAllProviderOrgs: true
``` 
 
15. Check connectivity between Portal Admin Endpoint and UI Endpoint and APIM pods.
 
16. Run register_gateway_portal script:
 
 ```bash
python3 register_gateway_portals_in_target.py -n apic -u admin -p -s apic-mgmt-platform-api-apic.apps.cp4intdr.example.com -r admin/default-idp-1 -silent
```
 
17. Login to Cloud Manager and check new portal and gw services or check them with apic cli:
 
```bash
apic login --server apic-mgmt-platform-api-apic.apps.cp4intdr.example.com --realm admin/default-idp-1 --username admin -p
```
 
```bash
apic orgs:list --org_type=admin --fields id,name --server apic-mgmt-platform-api-apic.apps.cp4intdr.example.com
```

```bash
apic portal-services:list --fields name,endpoint --server apic-mgmt-platform-api-apic.apps.cp4intdr.example.com --org admin --availability-zone availability-zone-default
```

```bash
apic gateway-services:list --fields name,endpoint --server apic-mgmt-platform-api-apic.apps.cp4intdr.example.com --org admin --availability-zone availability-zone-default
```
 
18. Install dnsmasq to the server which is accessible from DR cluster.

```bash
yum install dnsmasq
```

**Important**: New version of python scripts have --force to overcome this. DNSMasq might not be needed.
 
19. Add below lines to dnsmasq

```yaml
address=/cpd-cp4i.apps.cp4int.example.com/10.1.36.121
address=/cp-console.apps.cp4int.example.com/10.1.36.121
address=/cp-proxy.apps.cp4int.example.com/10.1.36.121
address=/apic-a7s-ai-endpoint-apic.apps.cp4int.example.com/10.1.36.121
address=/apic-mgmt-api-manager-apic.apps.cp4int.example.com/10.1.36.121
address=/apic-mgmt-admin-apic.apps.cp4int.example.com/10.1.36.121
address=/apic-mgmt-consumer-api-apic.apps.cp4int.example.com/10.1.36.121
address=/apic-mgmt-platform-api-apic.apps.cp4int.example.com/10.1.36.121
address=/apic-ptl-portal-director-apic.apps.cp4int.example.com/10.1.36.121
address=/devportal.example.com/10.1.36.121
address=/api.example.com/10.1.36.121
address=/apic-gw-gateway-manager-apic.apps.cp4int.example.com/10.1.36.121
```
 
20. Test dnsmasq configuration from the server which has dnsmasq.

 ```bash
dig @127.0.0.1 cpd-cp4i.apps.cp4int.example.com
```
 
21. Get eth ip to set listen_address from the dnsmasq server.

```bash
ifconfig
```

```yaml
listen-address=10.1.36.21
```
22. Test dnsmasq configuration. 

dig @10.1.36.21 cpd-cp4i.apps.cp4int.example.com
 
23. List DNS operator in OCP:

```bash
oc get DNS.operator default -o yaml
```
 
24. Add below lines to dns-config.yaml

```yaml
apiVersion: operator.openshift.io/v1
kind: DNS
metadata:
  name: default
spec:
  servers:
  - name: temp-dns
    zones: 
    - cpd-cp4i.apps.cp4int.example.com
    - cp-console.apps.cp4int.example.com
    - cp-proxy.apps.cp4int.example.com
    - apic-a7s-ai-endpoint-apic.apps.cp4int.example.com
    - apic-mgmt-api-manager-apic.apps.cp4int.example.com
    - apic-mgmt-admin-apic.apps.cp4int.example.com
    - apic-mgmt-admin-apic.apps.cp4int.example.com
    - apic-mgmt-consumer-api-apic.apps.cp4int.example.com
    - apic-mgmt-platform-api-apic.apps.cp4int.example.com
    - apic-ptl-portal-director-apic.apps.cp4int.example.com
    - devportal.example.com
    - api.example.com  
    - apic-gw-gateway-manager-apic.apps.cp4int.example.com
    forwardPlugin:
      policy: Random 
      upstreams: 
      - 10.1.36.21
 ```

25. Apply YAML file.

```bash
oc apply -f dns-config.yaml
```

26. Restart dns-operator pod.
 
27. Run update_to_new_portals.py

```bash
python3 update_to_new_portals.py -n apic -u admin -p -s apic-mgmt-platform-api-apic.apps.cp4intdr.example.com -r admin/default-idp-1 -silent -api_manager_hostname  apic-mgmt-platform-api-apic.apps.cp4intdr.example.com
```
28. Run update_to_new_gateways.py

```bash
  python3 update_to_new_gateways.py -n apic -u admin -p -s apic-mgmt-platform-api-apic.apps.cp4intdr.example.com -r admin/default-idp-1 -silent -api_manager_hostname  apic-mgmt-platform-api-apic.apps.cp4intdr.example.com
```

## Troubleshooting

1. OCP Error: https://www.ibm.com/support/pages/cluster-unstable-and-zen-metastoredb-restarting-repeatedly

2. NTP server check:

```bash
curl -v telnet://ntp.example.com:123  
```
**Check chrony services as ODF is not running without NTP**

3. Running some of the python scripts might require --force. (catalog-settings-update)
 

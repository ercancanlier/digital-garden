---
title: OpenShift Audit Logs
linktitle: OpenShift Audit Logs
description: Audit log filtering
tags: ["openshift"]
---

# OpenShift Audit Logs 

Audit logging records event, event time, responsible user and the impacted entity etc. of your software system or service.

OpenShift Audit Logs capture following information for the events:

- auditId

- requestURI

- verb

- user

- sourceIps

- objectRef

- responseStatus

- timestamps

If you have already set audit logging in OpenShift, you may start filtering them to get more meaningful info about the events.


## Examples and some common use cases by filtering jq:

1. List control plane node's audit logs:

```bash
oc adm node-logs --role=master --path=openshift-apiserver
```
* Change the path according to your requirements.

2. Print out specific audit log file:
```bash
oc adm node-logs <node-name> --path=openshift-apiserver/audit.log
````

3. List Kubernetes API Server audit logs:
```bash
oc adm node-logs --role=master --path=kube-apiserver
```
4. Print out specific node's Kubernetes API Server audit log:
```bash
oc adm node-logs <node-name> --path=kube-apiserver/audit.log
````

5. List OpenShift OAuth API Server audit logs:
```bash
oc adm node-logs --role=master --path=oauth-apiserver
```

6. Print out specific node's OpenShift OAuth API Server audit log:
```bash
oc adm node-logs <node-name> --path=oauth-apiserver/audit.log
````

7. List OpenShift OAuth Server audit logs:
```bash
oc adm node-logs --role=master --path=oauth-server
```

8. Print out specific node's OpenShift OAuth Server audit log:
```bash
oc adm node-logs <node-name> --path=oauth-server/audit.log
```

9. Filter OpenShift API Server audit logs related to delete requests:
```bash
oc adm node-logs <node-name> --path=openshift-apiserver/audit.log | jq 'select(.verb == "delete")'
````

10. Filter OpenShift API Server audit logs related to routes:
```bash
oc adm node-logs <node-name> --path=openshift-apiserver/audit.log | jq 'select(.verb == "get" and .objectRef.resource=="routes")'
````

11. Filter all successfull login attempts related to user:
```bash
oc adm node-logs <node-name> --path=oauth-server/audit.log 
| jq 'select(.verb=="get" and .annotations["authentication.openshift.io/decision"]=="allow" and .annotations["authentication.openshift.io/username"]=="<username>")'
````

12. Filter all user creation audit logs:
```bash
oc adm node-logs <node-name> --path=openshift-apiserver/audit.log | jq 'select(.verb == "create" and .objectRef.resource=="users")'
````

13. Filter all service account creation and deletion audit logs:
```bash
oc adm node-logs <node-name> --path=openshift-apiserver/audit.log | jq 'select((.verb == "delete" or .verb == "create") and .objectRef.resource=="serviceaccounts")'
````

14. Filter all pod creation and deletion audit logs:
```bash
oc adm node-logs <node-name> --path=kube-apiserver/audit.log  | jq -r 'select((.verb == "delete" or .verb == "create") and .objectRef.resource=="pods")'
```

15. Filter all failed login attempts:
```bash
oc adm node-logs <node-name> --path=oauth-server/audit.log | jq 'select(.verb=="get" and .annotations["authentication.openshift.io/decision"]=="deny")'
````

## Example Audit Log Event:
```json
{
  "kind": "Event",
  "apiVersion": "audit.k8s.io/v1",
  "level": "Metadata",
  "auditID": "27a46379-4855-4315-adaf-94493b6888b3",
  "stage": "ResponseComplete",
  "requestURI": "/apis/route.openshift.io/v1/namespaces/openshift-monitoring/routes/alertmanager-main",
  "verb": "get",
  "user": {
    "username": "system:serviceaccount:openshift-monitoring:cluster-monitoring-operator",
    "groups": [
      "system:serviceaccounts",
      "system:serviceaccounts:openshift-monitoring",
      "system:authenticated"
    ],
    "extra": {
      "authentication.kubernetes.io/pod-name": [
        "cluster-monitoring-operator-6dd9848789-4j4f9"
      ],
      "authentication.kubernetes.io/pod-uid": [
        "7fa68c60-0d32-43dc-a4ba-25f82d21bb34"
      ]
    }
  },
  "sourceIPs": [
    "x.x.x.x",
    "x.x.x.x"
  ],
  "userAgent": "operator/v0.0.0 (linux/amd64) kubernetes/$Format",
  "objectRef": {
    "resource": "routes",
    "namespace": "openshift-monitoring",
    "name": "alertmanager-main",
    "apiGroup": "route.openshift.io",
    "apiVersion": "v1"
  },
  "responseStatus": {
    "metadata": {},
    "code": 200
  },
  "requestReceivedTimestamp": "2023-09-26T19:05:26.595182Z",
  "stageTimestamp": "2023-09-26T19:05:26.600763Z",
  "annotations": {
    "authorization.k8s.io/decision": "allow",
    "authorization.k8s.io/reason": "RBAC: allowed by RoleBinding \"cluster-monitoring-operator/openshift-monitoring\" of ClusterRole \"cluster-monitoring-operator-namespaced\" to ServiceAccount \"cluster-monitoring-operator/openshift-monitoring\""
  }
}
```

API Index: https://docs.openshift.com/container-platform/4.12/rest_api/index.html
---
date: 2024-11-29
categories:
  - OpenShift
---

# Hello world!
# OpenShift Audit Logging Guide

Audit logging in OpenShift captures detailed information about events in the system, including the event type, timestamp, responsible user, and impacted entity. This blog post explains how to access, filter, and analyze audit logs in OpenShift.

## **Captured Audit Log Fields**

OpenShift Audit Logs include the following fields:

- `auditId`: Unique identifier for the event.

- `requestURI`: The API endpoint accessed.

- `verb`: HTTP verb used (e.g., `get`, `create`, `delete`).

- `user`: Information about the user making the request.

- `sourceIps`: Source IP addresses of the request.

- `objectRef`: The impacted object/resource.

- `responseStatus`: Status of the response (e.g., HTTP code).

- `timestamps`: Event timestamps for request and response.

---

## **Viewing Audit Logs**

You can retrieve audit logs using the `oc adm node-logs` command. Below are common use cases:

### **1. Control Plane Node Audit Logs**
```bash
oc adm node-logs --role=master --path=openshift-apiserver




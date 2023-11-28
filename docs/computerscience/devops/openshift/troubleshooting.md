## The openshift installation failed with errors
```log
E1011 08:07:56.183305       1 auth.go:231] error contacting auth provider time="2023-11-07T17:50:00+03:00" level=error msg="Cluster operator authentication Degraded is True with IngressStateEndpoints_MissingSubsets::OAuthClientsController_SyncError::OAuthServerDeployment_PreconditionNotFulfilled::OAuthServerRouteEndpointAccessibleController_SyncError::OAuthServerServiceEndpointAccessibleController_SyncError::OAuthServerServiceEndpointsEndpointAccessibleController_SyncError::ProxyConfigController_SyncError::WellKnownReadyController_SyncError: IngressStateEndpointsDegraded: No subsets found for the endpoints of oauth-server\nOAuthClientsControllerDegraded: no ingress for host oauth-openshift.apps.ocpactest.example.com in route oauth-openshift in namespace openshift-authentication\nOAuthServerDeploymentDegraded: waiting for the oauth-openshift route to contain an admitted ingress: no admitted ingress for route oauth-openshift in namespace openshift-authentication\nOAuthServerDeploymentDegraded: \nOAuthServerRouteEndpointAccessibleControllerDegraded: route \"openshift-authentication/oauth-openshift\": status does not have a valid host address\nOAuthServerServiceEndpointAccessibleControllerDegraded: Get \"https://10.132.220.152:443/healthz\": dial tcp 10.132.220.152:443: connect: connection refused\nOAuthServerServiceEndpointsEndpointAccessibleControllerDegraded: oauth service endpoints are not ready\nProxyConfigControllerDegraded: no admitted ingress for route oauth-openshift in namespace openshift-authentication\nWellKnownReadyControllerDegraded: failed to get oauth metadata from openshift-config-managed/oauth-openshift ConfigMap: configmap \"oauth-openshift\" not found (check authentication operator, it is supposed to create this)"
time="2023-11-07T17:50:00+03:00" level=error msg="Cluster operator authentication Available is False with OAuthServerDeployment_PreconditionNotFulfilled::OAuthServerServiceEndpointAccessibleController_EndpointUnavailable::OAuthServerServiceEndpointsEndpointAccessibleController_ResourceNotFound::ReadyIngressNodes_NoReadyIngressNodes::WellKnown_NotReady: OAuthServerServiceEndpointAccessibleControllerAvailable: Get \"https://10.132.220.152:443/healthz\": dial tcp 10.132.220.152:443: connect: connection refused\nOAuthServerServiceEndpointsEndpointAccessibleControllerAvailable: endpoints \"oauth-openshift\" not found\nReadyIngressNodesAvailable: Authentication requires functional ingress which requires at least one schedulable and ready node. Got 0 worker nodes, 2 master nodes, 0 custom target nodes (none are schedulable or ready for ingress pods).\nWellKnownAvailable: The well-known endpoint is not yet available: failed to get oauth metadata from openshift-config-managed/oauth-openshift ConfigMap: configmap \"oauth-openshift\" not found (check authentication operator, it is supposed to create this)"
```

**Issue**: 2 of the master nodes available, requires 3 for etcd cluster.

**Solution**: Check machines, machinesets and nodes :
```bash
oc --kubeconfig=${INSTALL_DIR}/auth/kubeconfig get clusterversion
oc --kubeconfig=${INSTALL_DIR}/auth/kubeconfig get nodes
oc --kubeconfig=${INSTALL_DIR}/auth/kubeconfig get machines
oc --kubeconfig=${INSTALL_DIR}/auth/kubeconfig get machinesets
oc --kubeconfig=${INSTALL_DIR}/auth/kubeconfig get csr | grep -i pending
oc --kubeconfig=${INSTALL_DIR}/auth/kubeconfig get clusterversion

for i in `oc get csr --no-headers \
| grep -i pending \ 
| awk '{ print $1 }'`;  do oc adm certificate approve $i; 
done
```
## ODF Operator is not able to upgrade

**Issue**:  Upgrading ODF operator is not working as expected.

**Solution**: Check OLM components:

* Backup install-plan and delete.
* Backup csv and subscription and delete.
* Apply subscription again and wait for csv and install-plan to be created.
* Approve the operator upgrade manually if approve type is manual.
* Set operator approval type to automatic.

## OCP Upgrade is stucked for some of the cluster operators

**Issue**: Some of the cluster operators are not able to be upgrade due to the bad conditions.

**Solution**: Check cluster operators and related namespaces:
```bash
oc get co
```
* Check Administration->Cluster Settings
* Check related namespaces to the operators like openshift-sdn, openshift-storage whether or not the pods have underlying issues.
* Check network connectivity especially if there is cluster-wide proxy.
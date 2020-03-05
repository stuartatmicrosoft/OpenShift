# Create an ARO 4.3 Private Cluster

## Overview

This document will walk you thorugh the steps to deploy a fully private Azure Red Hat OpenShift 4.3 cluster during the *preview of ARO 4.3* which includes:

- Bring your own private Azure VNET
- Private API Server
- Private Router
- Custom DNS forarded to on-premises DNS Server
- Azure Active Directory Integration
- Restricted User Access and RBAC for project creation

TODO:
- Custom DNS registration for the cluster API
- Registering SSL certificates for Custom DNS
- **Known Issue:** You must setup [Pull Secret](https://blog.openshift.com/building-rhel-based-containers-on-azure-red-hat-openshift/) in order to use the Red Hat container catalog
- **Known Issue:** AAD Pod Identity is not currently working.

## Creating an ARO Cluster 

### Prerequisites

ARO 4.3 is currently in *preview* and as such is subject to change.  During the preview you will need to follow the documented [installing the extension](https://github.com/Azure/ARO-RP/blob/master/docs/using-az-aro.md#installing-the-extension) steps.

Please follow the remaining steps on this [page](https://github.com/Azure/ARO-RP/blob/master/docs/using-az-aro.md) to create your own VNET and create an ARO cluster in that vnet, however you will need to add two parameters to step 1 of "Using the extension":

```bash
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet "dev-vnet" \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --apiserver-visibility Private \ 
  --ingress-visibility Private
```

## Connecting to the ARO Cluster 

1. Get the API server address:
```bash
KUBEAPI=$(az aro show -n $CLUSTER -g $RESOURCEGROUP -o tsv --query apiserverProfile.url)
```

2. Get the kubeadmin password:
```bash
KUBEPWD=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o tsv --query kubeadminPassword)
```

3. Install the 4.x oc cli:
```bash
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/4.3.1/openshift-client-linux-4.3.1.tar.gz
tar -xf openshift-client-linux-4.3.1.tar.gz
``` 

4. Login to the cluster using kubeadmin with the oc cli:
```bash 
./oc login $KUBEAPI -u kubeadmin -p $KUBEPWD
```

5. (Optional) Show the URL of the cluster web console:
```bash 
az aro show -n $CLUSTER -g $RESOURCEGROUP -o tsv --query consoleProfile.url
```

## Next Steps

- [Configure DNS Forwarding](DNSForwarding.md)
- [AAD Integration](AADIntegration.md)
- [Limit Project Creation](LimitSelfProvisioning.md)

## Code of conduct

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

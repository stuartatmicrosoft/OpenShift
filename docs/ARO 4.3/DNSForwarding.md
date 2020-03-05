# Configure DNS Forwarding on an ARO 4.3 Cluster

## Overview

This document will walk you thorugh the steps to deploy a fully private Azure Red Hat OpenShift 4.3 cluster during the *preview of ARO 4.3* which includes:

You will need to modify the DNS Operator in ARO 4.3 in order for your application pods running inside the ARO 4.3 cluster to resolve names hosted on a private DNS server outside the cluster.  These steps are documented for OpenShift 4.3 here: 
https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html

For example, if you want to forward all DNS requests for *.jasondel.com to be resolved by a DNS server 192.168.100.10, you can edit the operator configuration by running:
 
oc edit dns.operator/default
 
This will launch an editor and you can replace spec: {} with:
 
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: jasondel-dns
    zones:
    - jasondel.com

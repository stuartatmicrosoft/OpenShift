# Restrict Project Creation
 
By default, all users who have access to ARO 4.3 will be able to create projects.  In order to limit this, you must run the following command to remove the "self-provisioner" role from all oauth authenticated users:
 
oc adm policy \ remove-cluster-role-from-group self-provisioner system:authenticated:oauth
 
To enable a specific user or OpenShift group using OpenShift RBAC, you will need to grant that RBAC role:
 
oc adm policy add-cluster-role-to-user self-provisioner user@domain.com

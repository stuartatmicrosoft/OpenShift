# Configure AAD Integration for an ARO 4.3 Cluster

## Overview

A) You will need to start with creating, or using an existing Service Principal in your AD Tenant that has the following API permissions:
 
o	Azure Active Directory Graph
	Directory.ReadAll
	User.ReadAll
o	Microsoft.Graph
	User.Read
 
Ensure that you click "Grant admin consent…"
 
 
 
Click on Token configuration (preview) in the SP and enable identities for email, family_name, and upn by clicking "Add optional claim" of type ID for any missing:
 
 
 
 
B) Configure the Authentication redirect
 
Get the URL of the oauth end point:
 
echo "https://oauth-openshift.apps.$(az aro show -n $CLUSTER -g $RESOURCEGROUP -o tsv
--query clusterProfile.domain)"
 
Click on the "Authentication" tab and enter the Redirect URI returned from the command above:
 
 
 
 
[OPTIONAL]
If you want to restrict ONLY certain users or AD GROUPS to be able to login to your cluster, check "User assignment required" and add those users/groups.  The complete documentation on how to configure this is here: https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users
 
 
 
 
 
 
 
 
 
C) In ARO 4.3 you will need to configure the OpenID Connect provider.  
 
Step 1: Obtain the console URL for the cluster and navigate to it in a browser:
 
echo "https://console-openshift-console.apps.$(az aro show -n $CLUSTER -g $RESOURCEGROUP -o tsv
--query clusterProfile.domain)"
 
Use this URL in a browser and login with the kubeadmin credentials you received in the prior steps.
 
Navigate to User Management in the left hand pane:
 
 
 
Click to Add an Identity Provider and choose "OpenID Connect"
 
 
 
 
Set the name to “AAD”.  
 
Use the ClientID and Secret from the Service Principal in your AD tenant configured earlier.  
 
The Issuer URL is formatted as such: https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx where the xx's represent the format of the GUID of your AD tenant ID that the Service Principal reference above resides.
 
Configure preferred username to be “upn”.
 
 
 
Step 2 – Set name to “family_name” (Ideally this should be both “given_name family_name” however I am not sure if you put two values if they are concatenated together. This example will only insert the user’s last name). Set email to “email” and choose add.
 
 
 
 

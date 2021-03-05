# TrilioVault for Kubernetes UI Permissions

The TrilioVault for Kubernetes UI offers some great features. 

  * Discovery of K8s applications
  * Intuitive management of Backup & Restore Plans in real-time across clouds
  * Simple workflows for managing application consistent backups & restores
  * Ability to migrate application data and metadata to other clouds
  * Monitoring of overall health & performance metrics


<img src="./pics/ui.png" width="600"> 


## Synopsis

Bringing Dev and Ops together is an important part of Kubernetes with dev and ops teams working on the same tools and infrastructure. Trilio integrates into K8s based roles and permissions rather than having to use a separate RBAC policy framework. Each user can perform TVK operations based on their specific RBAC within the K8s system.

In this blog we will demonstrate how to enable permissions on your cluster that will allow RBAC for the TrilioVault for Kubernetes UI. When you install TrilioVault, a service account is created with permissions to access TVK and the UI.  

You may want to configure an Oauth identity provider, such as GitHub, on your cluster to validate usernames and passwords.  If that is the case, some extra steps are required.  For instance, you will need to set up a ClusterRole, and a ClusterRoleBinding. To set up permission for the 'system:authenticated:oauth' group.  This blog details the process for setting up that configuration.  




# Scenario
Bob the cluster admin wants to be the sole creator of backup targets (since he is responsible for storage resources and management within his org), while John the developer or app admin will want to consume that backup target for storing his backups. Here are the permissions that Bob would set for himself and for John. Now when john logs in - he won’t be able to create the target, but will be able to choose the targets created by Bob to backup applications into.


## Cluster Permissions
To access the UI and these feature you will need to create a ClusterRole and a ClusterRoleBinding.

The TrilioVault for Kubernetes UI leverages existing cluster permissions to access the UI. At a minimum users logging into 
the UI need to have read permissions for the TrilioVault group resource. The following ClusterRole shows the minimum level 
of permissions required to access the TVK management console.

  **Example 1:**
  ```
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRole
     metadata:
       name: svcs-role
    rules:
     - apiGroups: ["triliovault.trilio.io"]
       resources: ["*"]
      verbs: ["get", "list"]
     - apiGroups: ["triliovault.trilio.io"]
       resources: ["policies"]
      verbs: ["create"]
  ```
  
 **You may want to expand these permissions to include some or all of the resources.**
 
 <img src="./pics/crd.png" width="450"> 
 
Verbs give users and groups permission to execute actions associated with a resource.  

Verbs: 
 ``` 
create
delete
deletecollection
get
list
patch
update
watch
```
Resources:
```
backupplans                        
backups                            
hooks                                
licenses                         
policies                           
restores                            
targets                             
```
 A list of resources can be gathered by running command:
 ```
oc get crd | grep -i trilio
 ```
 

 **Example 2 - Bob, Cluster Admin:**
 Bob needs **create** and **delete** permissions for **policies** to log into the Trilio UI, and **targets** and **licenses** because he is responsible for storage resources and management.  
 
   ```
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRole
     metadata:
       name: svcs-role
    rules:
     - apiGroups: ["triliovault.trilio.io"]
       resources: ["*"]
      verbs: ["get", "list"]
     - apiGroups: ["triliovault.trilio.io"]
       resources: ["policies", "targets", "licenses"]
      verbs: ["create", "delete"]
  ```
  
  **Example 3 - Jon, Lead Software Developer:**  
  
```
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRole
     metadata:
       name: svcs-role
    rules:
      - apiGroups: ["triliovault.trilio.io"]
       resources: ["*"]
      verbs: ["get", "list"]
     - apiGroups: ["triliovault.trilio.io"]
       resources: ["policies", "backups", "restores", "backupplans", "hooks", ]
      verbs: ["create", "delete"]
 ```
  
  **Example 4 - Full Permissions:**
  
  ```
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRole
     metadata:
       name: svcs-role
    rules:
     - apiGroups: ["triliovault.trilio.io"]
       resources: ["*"]
      verbs: ["*"]
  ```
 

## Steps to create your ClusterRole in the OCP UI


  **Navigate to User Management -> Roles**
  
  
  Create a new **<em>ClusterRole</em>** for all namespaces
  
  

 
      
## Create a Cluster-wide RoleBinding for your newly created ClusterRole


This cluster uses an oath provider and so we used the **<em>system:authenticated:oauth</em>** group.  In the case that you are using a different provider, find the group based on your provider.

```
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: svcs-binding
subjects:
  - kind: Group
    apiGroup: rbac.authorization.k8s.io
    name: 'system:authenticated:oauth'
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: svcs-role
  ```
  
## Log into OCP with user account (non-admin)
  When logging in with the CLI for the first time, OpenShift creates a **<em>~/.kube/config</em>** file if one does not already exist.
  
  Access kubeconfig file with command: 
    
  ```
  cat ~/.kube/config
  ```
  Copy kubeconfig file contents into file on your local machine
  
  With your new ClusterRole and RoleBindings in place, that kubeconfig file should now have the correct permissions to be used with the TVK UI
  
  
  
  
  <img src="./pics/tvk-login.png" width="600"> 

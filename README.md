# ClusterRoleBinding and Kubeconfig setup 

In this blog we would like to show you how to enable permissions on your cluster that will allow RBAC for the TrilioVault for Kubernetes UI.

The TrilioVault for Kubernetes UI offers some great features. 

  * Discovery of K8s applications
  * Intuitive management of Backup & Restore Plans in real-time across clouds
  * Simple workflows for managing application consistent backups & restores
  * Ability to migrate application data and metadata to other clouds
  * Monitoring of overall health & performance metrics
  
To access the UI and these feature you will need to create a ClusterRole and a ClusterRoleBinding.

<img src="./pics/ui.png" width="450"> 

## Cluster Permissions

The TrilioVault for Kubernetes UI leverages existing cluster permissions to access the UI. At a minimum users logging into 
the UI need to have read permissions for the TrilioVault group resource. The following ClusterRole shows the minimum level 
of permissions required to access the TVK management console.

  ```
     Example:
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
 
 ```
 api-resources
 
create
delete
deletecollection
get
list
patch
update
watch
```
 

 
   ```
     Example:
        apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRole
     metadata:
       name: svcs-role
    rules:
     - apiGroups: ["triliovault.trilio.io"]
       resources: ["*"]
      verbs: ["get", "list"]
     - apiGroups: ["triliovault.trilio.io"]
       resources: ["policies", "targets", "restore", "backups", "backupplans"]
      verbs: ["create"]
  ```
  
  **For delete permissions you could create it like this...**  
  
  
```
     - apiGroups: ["triliovault.trilio.io"]
       resources: ["policies", "backups", "backupplans"]
       verbs: ["delete"]
 ```
  
  **For full permissions you could create it like this...**
  
  ```
     Example:
        apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRole
     metadata:
       name: svcs-role
    rules:
     - apiGroups: ["triliovault.trilio.io"]
       resources: ["*"]
      verbs: ["get", "list", "create", "delete"]
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
  
  With your new ClusterRole and RoleBindings in place, that kubeconfig file should now have the correct permissions to be used with the TVK UI
  
  
  
  <img src="./pics/tvk-login.png" width="450"> 

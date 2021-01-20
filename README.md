# ClusterRoleBinding and Kubeconfig setup 

## 1. Log into OCP as Admin

## 2. Cluster Permissions

The TrilioVault for Kubernetes UI leverages existing cluster permissions to access the UI. No special RBAC need to be created for accessing the UI. 
At a minimum users logging into the UI need to have read permissions for the TrilioVault group resource.
The following ClusterRole shows the minimum level of permissions required to access the TVK management console.

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
 
 **CRDs:
 Backup
 Restore
 Target
 Policy
 Hook
 BackupPlan**
 
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
       resources: ["policies", "targets", "restore", "backup", "restore"]
      verbs: ["create"]
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
      verbs: ["get", "list", "create"]
  ```
 

## 3. Steps to create your ClusterRole in the OCP UI


  a. Navigate to User Management -> Roles
  b. Create a new ClusterRole for all namespaces
  
  

 
      
## 4. Create a Cluster-wide RoleBinding for your newly created ClusterRole


**Here we use an oath provider and so we used the system:authenticated:oauth group.  In the case that you are using a different provider, find the group based on your provider. 

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
  
## 5. Log into OCP with user account (non-admin)
  a. When logging in with the CLI for the first time, OpenShift creates a ~/.kube/config file if one does not already exist.
  b. With your new ClusterRole and RoleBindings in place, that kubeconfig file should now have the correct permissions to be used with the TVK UI
  c. Everyday you'll need to update the token in your kubeconfig file.  Copy token from OCP login.  
  
  <img src="./pics/tvk-login.png" width="450"> 

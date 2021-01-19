# ClusterRoleBinding and Kubeconfig setup 

1. Log into OCP as Admin
2. Role Create
  a. Navigate to User Management -> Roles
  b. Create a new ClusterRole for all namespaces
  c. The TVK UI needs certain permissions, we are providing those permissions with this ClusterRole below:
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
      
3. Create a Cluster-wide RoleBinding for your created ClusterRole
  a. Here we use an oath provider and so we used the system:authenticated:oauth group.  You can use a different group based on your provider. 

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
  
4. Log into OCP with user account (non-admin)
  a. When logging in with the CLI for the first time, OpenShift creates a ~/.kube/config file if one does not already exist.
  b. With your new ClusterRole and RoleBindings in place, that kubeconfig file should now have the correct permissions to be used with the TVK UI
  

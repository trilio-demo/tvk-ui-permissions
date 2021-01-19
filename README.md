# ClusterRoleBinding and Kubeconfig setup 

1. Log into OCP as Admin
2. Role Create
  a. Navigate to User Management -> Roles
  b. Create a new ClusterRole for all namespaces, outline apiGroups
  
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
      
3. Create a Cluster-wide RoleBinding of your created ClusterRole to an oath group in the "default" namespace 
  

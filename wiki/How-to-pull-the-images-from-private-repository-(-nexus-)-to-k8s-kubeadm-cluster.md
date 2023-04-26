
create the secret in k8s cluster with proper details ( below command can help )

```
kubectl create secret docker-registry registry-secret --docker-server=nexus_machine_ip_only:8083 --docker-username=admin --docker-password=admin --docker-email=not-needed@example.com
```

once its done refer the same in k8s manifest files  as below 

```
apiVersion: v1
kind: Pod
metadata:
  name: foo
spec:
  containers:
    - name: foo
      image: nginx
  imagePullSecrets:
    - name: registry-secret
```
source: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

# Add
```fish
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```
<br>

# Setup user
source: https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md

```fish
kubectl apply -f kubernetes/install_extras/dashboard.yaml
```

Getting a bearer token:
```fish
kubectl -n kubernetes-dashboard create token admin-user
```

Getting long lived token:
```fish
kubectl get secret admin-user -n kubernetes-dashboard -o jsonpath={".data.token"} | base64 -d
```
Dashboard will be available at:
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/.

```fish
kubectl proxy
```

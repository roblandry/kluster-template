source: https://github.com/kubernetes-csi/csi-driver-nfs

# Install
```fish
helm repo add csi-driver-nfs https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/charts
```

```fish
helm install csi-driver-nfs csi-driver-nfs/csi-driver-nfs --namespace kube-system --version v4.6.0 --set controller.replicas=2
```
| extra | param
|--|--|
| debug: | ```--wait -v=5 --debug``` |
| not working: | ```--set controller.runOnControlPlane=true``` |
<br>

# Create

```fish
kubectl create -f kubernetes/storage/unraid.pv.yaml
```
```fish
kubectl create -f kubernetes/storage/unraid.pvc.yaml
```
<br>

# Info
```fish
kubectl --namespace=kube-system get pods --selector="app.kubernetes.io/instance=csi-driver-nfs" --watch
```

```fish
kubectl -n kube-system get pod -o wide -l app=csi-nfs-node
```

```fish
kubectl -n kube-system get pod -o wide -l app=csi-nfs-controller
```

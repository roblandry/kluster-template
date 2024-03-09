
source: https://ranchermanager.docs.rancher.com/getting-started/installation-and-upgrade/install-upgrade-on-a-kubernetes-cluster


# 1. Add the Helm chart repository
```fish
helm repo add rancher-stable https://releases.rancher.com/server-charts/stable
```
<br>

# 2. Create a namespace for Rancher
```fish
kubectl create namespace cattle-system
```
<br>

# 3. Choose your SSL configuration
| | Configuration	| Helm Chart Option	| Requires cert-manager
|-|---------------|-------------------|----------------------
| &#x2610; | Rancher Generated Certificates (Default) | ingress.tls.source=rancher |	yes
| &#x2612; | Let’s Encrypt | ingress.tls.source=letsEncrypt | yes
| &#x2610; |Certificates from Files | ingress.tls.source=secret | no
<br>

# Install cert-manager (unless you are bringing your own certificates, or TLS will be terminated on a load balancer)
Not required. Verify with:
```fish
kubectl get pods --namespace cert-manager
```
output:
```fish
NAME                                       READY   STATUS    RESTARTS      AGE
cert-manager-5b956d675f-ddxlz              1/1     Running   2 (16h ago)   3d
cert-manager-cainjector-6976895488-z2l8g   1/1     Running   2 (16h ago)   17h
cert-manager-webhook-fcf48cc54-29nf2       1/1     Running   2 (16h ago)   17h
```
<br>


# Install Rancher with Helm and your chosen certificate option
Using letsencrypt:
```fish
helm install rancher rancher-stable/rancher \
  --namespace cattle-system \
  --set hostname=rancher.CHANGE.ME \
  --set bootstrapPassword=CHANGEME \
  --set ingress.tls.source=letsEncrypt \
  --set letsEncrypt.email=CHANGEME \
  --set letsEncrypt.ingress.class=nginx
```
<br>

# Verify that the Rancher server is successfully deployed
```fish
kubectl -n cattle-system rollout status deploy/rancher
```
<br>

# Save your options
Make sure you save the ```--set``` options you used. You will need to use the same options when you upgrade Rancher to new versions with Helm.


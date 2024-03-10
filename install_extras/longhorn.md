source: https://longhorn.io/docs/1.6.0/deploy/install/install-with-helm/

# Helm install:
```fish
helm repo add longhorn https://charts.longhorn.io
```

```fish
helm repo update
```

```fish
helm install longhorn longhorn/longhorn --namespace longhorn-system --create-namespace --version 1.6.0
```
<br>

# Web access
Get the longhorn frontend ip
```fish
kubectl -n longhorn-system get svc
```

Create a basic auth file auth. It’s important the file generated is named auth (actually - that the secret has a key data.auth), otherwise the Ingress returns a 503.
```fish
USER=<USERNAME_HERE>; PASSWORD=<PASSWORD_HERE>; echo "${USER}:$(openssl passwd -stdin -apr1 <<< ${PASSWORD})" >> auth
```
Modified command. No ability to install openssl in devcontainer. Had to perform outside container.
```fish
echo "USER-CHANGEME:$(openssl passwd -stdin -apr1 <<< 'PASSWORD-CHANGEME')" >> auth
```

Create a secret:
```fish
kubectl -n longhorn-system create secret generic basic-auth --from-file=auth
```

Create an Ingress manifest longhorn-ingress.yml:
```fish
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: longhorn-ingress
  namespace: longhorn-system
  annotations:
    # type of authentication
    nginx.ingress.kubernetes.io/auth-type: basic
    # prevent the controller from redirecting (308) to HTTPS
    nginx.ingress.kubernetes.io/ssl-redirect: 'false'
    # name of the secret that contains the user/password definitions
    nginx.ingress.kubernetes.io/auth-secret: basic-auth
    # message to display with an appropriate context why the authentication is required
    nginx.ingress.kubernetes.io/auth-realm: 'Authentication Required '
    # custom max body size for file uploading like backing image uploading
    nginx.ingress.kubernetes.io/proxy-body-size: 10000m
spec:
  rules:
  - http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: longhorn-frontend
            port:
              number: 80
```

Create the Ingress:

```fish
kubectl -n longhorn-system apply -f longhorn-ingress.yml
```

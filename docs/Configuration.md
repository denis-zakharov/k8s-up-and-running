# ConfigMaps and Secrets

Create runtime environment for your image.

```
k create configmap my-config --from-file=my-config.txt --from-literal=extra-param=extra-val --from-literal=another-param=another-val
k get configmaps my-config -o yaml
```

Secrets from files.

```
k create secret generic kuard-tls --from-file=kuard.crt --from-file=kuard.key
k describe secrets kuard-tls

kubectl create secret docker-registry my-image-pull-secret \
--docker-username=<username> \
--docker-password=<password> \
--docker-email=<email-address>
```

Consuming secrets.

- Calling K. API (less preferable)
- Secrets volume

```
k apply -f kuard-tls.yaml
```

Secrets/ConfigMap CRUD

```
k get secrets
k get configmaps
k describe configmaps my-config

k create secret docker-registry|generic|tls
--from-file=<file>
--from-file=<key>=<file>
--from-file=<dir>
--from-literal=<key>=<value>

# recreate
k create ... --dry-run -o yaml | k replace -f -

k edit configmap my-config
```

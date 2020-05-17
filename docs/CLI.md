[Docker Cheat Sheet](https://www.docker.com/sites/default/files/d8/2019-09/docker-cheat-sheet.pdf)

[Kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#viewing-finding-resources)

## port forwarding

Access pod port 8080 via port 8080 on your local machine (where k is run)

`k port-forward kuard 8080:8080`

## logging

`k logs -f kuard`

Check a previous instance (e.g. after restart)

`k logs --previous kuard`

## exec in a container

```
k exec kuard date
k exec -it kuard bash
```

## upload and download

Emergency fix usecase, otherwise is an antipattern.

```
k cp file <pod>:/file
k cp <pod>:/file file
```
## selector usages
### filter
```sh
k run alpaca-prod \
--image=gcr.io/kuar-demo/kuard-amd64:blue \
--replicas=2 \
--labels="ver=1,app=alpaca,env=prod"

k get pods --selector="app=bandicoot,ver!=2"
k get deployment.apps --selector="!canary"
k get pods --selector="app notin (alpaca,bandicoot)"
k get deployments.apps -L canary
```

### add/remove
Add/remove a new label to deployment (but not to an underlying pods).
```sh
k label deployments.apps alpaca-test "canary=true"
k label deployments.apps alpaca-test "canary-"

```

### show lables
```sh
k get deployments.apps --show-labels
k get pods --show-labels
```

# cleanup

```
k delete deployments.app --all
k delete deployments.app --selector="ver=2"
```

# Objects

Required fields:

- apiVersion
- kind
- metadata (data that helps uniquely identify the object, including name, UID, optional namespace)
- spec

# Namespaces

```sh
k config set-context --current --namespace="my-ns"
k config view --minify | grep "namespace:"
```

# Lables

kv-pairs that are attached to objects. They specify attributes that are meaningful to users.

```yaml
metadata:
  labels:
    release: stable
    environment: prod
    partition: customerA
    app: kuard
```

Combination of lables can produce selectors.
`k get pods -l <selector>`

## Equality-based selectors

`environment=prod,tier!=frontend`

## Set-based selectors

```
environment in (dev,qa)
tier notin (frontend,backend)
partition // existance checking
```

# Annotations

Arbitrary non-indentifying metadata to objects.

# Pods

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

## health checks

- `livenessProbe`: httpGet, tcpSocker, exec.
- `readinessProbe`: can be used for a graceful shutdown also.

## resources

Resources are requested per container in a pod. There two types:

- requests: minimal guaranteed requirement. More than that is provided on a best-effort.
- limits: consumption cannot exceed the limit even if there are free resources on a node.

## volumes

Volumes defined on a host machine should be applied explicitly to each container in a pod.

Usage patterns:

- communication/sync
- cache
- persistent data (remote network storage volumes)

# labels and annotations

Labels (k-v pairs) provide the foundation for grouping objects: organizing infrastructure from user's point of view, manage relations for internal K objects. Lables can be attached to K. objects (eg pods and replicasets).

Usage example:

```sh
k run alpaca-prod \
--image=gcr.io/kuar-demo/kuard-amd64:blue \
--replicas=2 \
--labels="ver=1,app=alpaca,env=prod"
```

Add/remove a new label to deployment (but not to an underlying objects: pods and repolicas).

```sh
k label deployments.apps alpaca-test "canary=true"
k label deployments.apps alpaca-test "canary-"

```

Show lables.

```sh
k get deployments.apps --show-labels
k get pods --show-labels
k get pods --selector="app=bandicoot,ver!=2"
k get deployment.apps --selector="!canary"
k get pods --selector="app notin (alpaca,bandicoot)"
k get deployments.apps -L canary
```

Selector for API.

```yaml
# app=alpaca,ver in (1, 2)
selector:
  matchLabels:
    app: alpaca
  matchExpressions:
    - { key: ver, operator: In, values: [1, 2] }
```

Annotations (k-v pairs) designed to hold nonidentifying info that can be leveraged by tools and libraries (eg where an object came from, how to use it, or policy around the object; somewhat similar to Java annotations).

# cleanup

```
k delete deployments.app --all
k delete deployments.app --selector="ver=2"
```

# service discovery

A Service object is a way to create a named label selector over Pods.

```
k expose deployment alpaca-prod
```

Cluster IP with a port for balancing across the pods identified by a service.

The IP is virtual and stable thus we can give it a DNS name. Withing a namespace, it is easy to just use the service name to connect to one of the Pods identified by a service.

K.DNS cluster IP resolving
`alpaca-prod.default.svc.cluster.local. 30 IN A 10.96.90.255`

- alpaca-prod: the name of the service.
- default: the namespace this service is in.
- svc: a service record mark.
- `cluster.local.`: the base domain name for the cluster (default, can be configured by admins).

## show service endpoints

`k get endpoints alpaca-prod --watch`

## service NodePort

Forwards traffic from specified port on any K. node to a service.

```yaml
spec.type: NodePort # default ClusterIP
```

`k describe service alpaca-prod` to see a node port.

## service LoadBalancer (Cloud provided)

```yaml
spec.type: LoadBalancer
```

## service endpoints

This is to be able to use list of IP directly without a cluster IP.

```
k describe endpoints alpaca-prod
k get endpoints alpaca-prod --watch
```

Client can watch the endpoints object and be notified on a change.

Basic manual discovery example

```
k get pods -o wide --selector='app=alpaca,env=prod'
```

Old approach of using env vars. Requires a special deployment order: service then pods to inject env vars.

```
ALPACA_PROD_SERVICE_HOST	10.96.223.201
ALPACA_PROD_SERVICE_PORT	8080
```

## external evironments

Outbound: define a selector-less K. service with a manually assigned IP that is outside of the cluster.

Inbound:

- fixed IP LB (discovered by traditional DNS) that can deliver external traffic in your virual private network.
- kube-proxy on external machine (for on-premises envs)
- Hashicorp Consul discovery

# HTTP Load Balancing with Ingress

Reverse http-proxy virtual hosting pattern.

# ReplicaSets

Usage:

- redundancy
- scale
- sharding

RS acts as a cluster-wide Pod manager, ensuring the right types and number of Pods are running at all times.

Blacklist a misbehaving Pod via labels.

RS is designed for (nearly) stateless services.

Get Pods of a RS `k get pods -l app=kuard,version=2`

Scale `k scale replicaset kuard --replicas=4`

## Horizontal Pod Autoscaling

In response to CPU, memory usage or other metric.

Requires a `heapster` Pod in the kube-system namespace.

```
k autoscale rs kuard --min=2 --max=5 --cpu-percent=80
```

Cleanup only RS itself `k delete rs kuard --cascade=false`

# Deployment

Provides a rolling update workload.

```
k apply -f kuard-deployment.yaml
k get replicasets.apps --selector=run=kuard
k scale deployment kuard --replicas=2  # scales underlying RS also
```

Control RS over deployment otherwise deployment state overrule your changes.

```
k get deployments.apps kuard -o yaml > kuard-deployment-exported.yaml
k replace -f kuard-deployment-exported.yaml --save-config
```

Deployments

```
k rollout history deployments kuard [--revision=2]
k rollout status deployments kuard
k rollout pause deployments kuard
k rollout resume deployments kuard
k rollout undo deployment kuard [--to-revision=3]
```

Strategies:

- Recreate: update deployment state, teminate all pods, sync (use only for test environments)
- Rolling update

RollingUpdateStrategy: 25% max unavailable, 25% max surge (extra resources).

minReadySeconds: 60 before updating the next pod. The timeout is reset for each pods creation phase.

# DaemonSets

Similar to ReplicaSet but bounds each Pod to a node (all nodes by default).

# Jobs

Run a pod once until a successful completion.

```yaml
completions: 1
parallelism: 1
kind: Job,CronJob
```

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

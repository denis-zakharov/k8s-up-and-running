It is recommended to use Deployments instead of directly using ReplicaSets, unless you require custom update orchestration or don’t require updates at all.

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

## Cleanup only RS itself
`k delete rs kuard --cascade=false`

## Describe
`k describe rs/kuard`
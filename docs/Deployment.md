Deployment provides a rolling update workload (declarative updates for Pods and ReplicaSets).

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

- Recreate: update deployment state, terminate all pods, sync (use only for test environments)
- Rolling update

RollingUpdateStrategy: 25% max unavailable, 25% max surge (extra resources).

minReadySeconds: 60 before updating the next pod. The timeout is reset for each pods creation phase.

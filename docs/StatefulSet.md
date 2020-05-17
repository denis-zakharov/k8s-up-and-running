StatefulSet is the workload API object used to manage stateful applications.

Manages the deployment and scaling of a set of Pods, and provides _guarantees about the ordering and uniqueness_ of these Pods.

StatefulSets are valuable for applications that require one or more of the following.

- Stable, unique network identifiers.
- Stable, persistent storage.
- Ordered, graceful deployment and scaling.
- Ordered, automated rolling updates.

The identity sticks to the Pod, regardless of which node it’s (re)scheduled on.

For example, see web-statefulset.yaml. It creates the 'nginx' Service and attaches it to the 'web' StatefulSet.

Each pod has a stable identity `web-N.nginx.default.svc.cluster.local`.

In general, `<StatefulSet.name>-<idx>.<ServiceName>.<Namespace>.svc.cluster.local`. 
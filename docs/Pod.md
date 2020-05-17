A cohesive unit of deployment with containers.
- Optional init containers.
- App containers.

Pods provide two kinds of shared resources for containers in it:
- networking (shared IP and ports - network namespace in general)
- storage (temporary or persistent volumes)
- process namespace (IPC and shared memory)

# Lifecycle
- Pending (accepted)
- Running
- Succeeded (won't be restarted)
- Failed (non-zero exit or forcefully terminated by the system)
- Unknown

# Container probes
httpGet, tcpSocker, exec.

- livenessProbe - is the container running
- readinessProbe - is the app ready to service requests
- startupProbe - if provided other probes are disabled; whether the app within the container is started; initialDelaySeconds + failureThreshold × periodSeconds

## resources
Resources are requested per container in a pod. There two types:

- requests: minimal guaranteed requirement. More than that is provided on a best-effort.
- limits: consumption cannot exceed the limit even if there are free resources on a node.

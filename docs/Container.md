# K8s Container Environment
- a filesystem: an image and one or more volumes
- gethostname
- cluster info (FOO_SERVICE_{HOST,PORT})

# Container Lifecycle Hooks
- PostStart (however, no guarantee to run before ENTRYPOINT)
- PreStop

# Hook Handlers
- Exec (command or script)
- HTTP (against a specific endpoint on a container)

# Examples
```yaml
containers:
- name: lifecycle-demo-container
image: nginx
lifecycle:
  postStart:
    exec:
      command: ["/bin/sh", "-c", "echo Hello from the postStart handler > /usr/share/message"]
  preStop:
    exec:
      command: ["/bin/sh","-c","nginx -s quit; while killall -0 nginx; do sleep 1; done"]
```
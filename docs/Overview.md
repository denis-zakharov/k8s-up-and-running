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
    # recommended
    app.kubernetes.io/name: mysql
    app.kubernetes.io/instance: wordpress-abcxzy
    app.kubernetes.io/version: "5.7.21"
    app.kubernetes.io/component: database
    app.kubernetes.io/part-of: wordpress
    app.kubernetes.io/managed-by: helm
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

## Selector for API

```yaml
# app=alpaca,ver in (1, 2)
selector:
  matchLabels:
    app: alpaca
  matchExpressions:
    - { key: ver, operator: In, values: [1, 2] }
```

# Annotations

Arbitrary non-identifying metadata to objects, usually processed by tools and libraries.

```yaml
metadata:
  annotations:
    pr: "1234"
    repo: "https://github.com"
    imageregistry: "https://hub.docker.com/"
```

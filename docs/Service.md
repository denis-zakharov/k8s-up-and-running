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

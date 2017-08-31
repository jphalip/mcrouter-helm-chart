# Mcrouter Helm Chart

Helm chart for [Mcrouter](https://github.com/facebook/mcrouter), a Memcached protocol router for scaling memcached deployments.

## Configuration

The following tables lists the configurable parameters of the consul chart and their default values.

| Parameter                     | Description                            | Default                                         |
| ----------------------------- | -------------------------------------- | ----------------------------------------------- |
| `image`                         | Container's image                      | `jamescarr/mcrouter` <br> Note: Third-party image as indicated in the [official documentation](https://github.com/facebook/mcrouter/wiki/mcrouter-installation) <br> It is recommended to build a new, up-to-date image based on the [official Dockerfile](https://github.com/facebook/mcrouter/blob/master/mcrouter/scripts/docker/Dockerfile) |
| `controller` | Controller used for deploying the Mcrouter pods. Possible values: `daemonset` or `statefulset` | `daemonset` |
| `daemonset.hostPort` | Host port used by the DaemonSet controller | `5000` |
| `mcrouterCommandParams.port`       | Port(s) to listen on (comma separated) | `5000`   |
| `mcrouterCommandParams.configFile` | (Optional) The config file to use      | No value |
| `memcachedService.serviceName`  | Memcached service's name         | `memcached` |
| `memcachedService.replicaCount` | Number of Memcached pod replicas | `3`         |
| `memcachedService.port`         | Memcached service's port         | `11211`     |
| `memcachedService.namespace`    | Memcached service's namespace    | `default`   |
| `resources.requests.cpu`    | CPU resource requests    | `100m`  |
| `resources.limits.cpu`      | CPU resource limits      | `256m`  |
| `resources.requests.memory` | Memory resource requests | `128Mi` |
| `resources.limits.memory`   | Memory resource limits   | `512Mi` |
| `statefulset.replicas` | Number of pod replicas used by the StatefulSet controller | `1` |
| `statefulset.antiAffinity` | Pod anti-affinity logic used by the StatefulSet controller. Possible values: `hard`, `soft` | `hard` |

## Testing

Install the Memcached chart:

    helm install stable/memcached --name mycache --set replicaCount=3

Install the Mcrouter chart:

    helm install mcrouter-helm-chart/mcrouter --name=myproxy --set memcachedService.serviceName="mycache-memcached" --set memcachedService.replicaCount=3

Connect to one of the Mcrouter pods:

    MCROUTER_POD_IP=$(kubectl get pods -l app=myproxy-mcrouter -o jsonpath="{.items[0].status.podIP}")
    
    kubectl run -it --rm alpine --image=alpine --restart=Never telnet $MCROUTER_POD_IP 5000
    
    > set mykey 0 0 5
    > hello
    STORED
    
    > get mykey
    VALUE mykey 0 5
    hello
    END
    
    > quit

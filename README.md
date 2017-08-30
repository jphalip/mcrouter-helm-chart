# Mcrouter Helm Chart

## Configuration

The following tables lists the configurable parameters of the consul chart and their default values.

| Parameter                     | Description                            | Default                                         |
| ----------------------------- | -------------------------------------- | ----------------------------------------------- |
| image                         | Container's image                      | `jamescarr/mcrouter` <br> Note: Third-party image as indicated in the [official documentation](https://github.com/facebook/mcrouter/wiki/mcrouter-installation) <br> It is recommended to build a new, up-to-date image based on the [official Dockerfile](https://github.com/facebook/mcrouter/blob/master/mcrouter/scripts/docker/Dockerfile) |
| mcrouterCommandParams.port       | Port(s) to listen on (comma separated) | `5000` |
| mcrouterCommandParams.configFile | (Optional) The config file to use | No value |

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

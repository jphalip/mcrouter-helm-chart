Install the memcached chart:

    helm install stable/memcached --name mycache --set replicaCount=3

Install the mcrouter chart:

    helm install mcrouter-helm-chart/mcrouter --name=myproxy --set memcachedService.serviceName="mycache-memcached" --set memcachedService.replicaCount=3

Testing:

    kubectl run -it --rm alpine --image=alpine --restart=Never telnet myproxy-mcrouter-0.myproxy-mcrouter.default.svc.cluster.local 5000
    
    > set anotherkey 0 60 5
    > hello
    STORED
    
    > get anotherkey
    VALUE mykey 0 5
    hello
    END
    
    > quit

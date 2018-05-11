---
title: Opening ports in Openshift
date: 2018-05-11 12:06:17
tags:
---

I've been trying to open ports on Openshift for maintenance. I needed one for remote Java debugging and one for remote profiling (VisualVM/jconsole to JMX).

I still haven't been successful this far in setting up JMX to talk through just one port, even on Java 8 with [`port` and `rmi.port`](https://stackoverflow.com/a/42394586/9296017). This probably has to do with the hostname parameter, although none of "127.0.0.1", "0.0.0.0" or the node's hostname have helped.

Obviously, such maintenance services should not require a route as they are not part of the application and not necessarily HTTP. In addition, they have to go directly to the pod instead of going through the load balancer. Finally, multiple ports may be needed in order to work some services, and ideally they would be grouped together.

So how do you do it? With [nodeports](https://docs.openshift.com/container-platform/3.6/dev_guide/expose_service/expose_internal_ip_nodeport.html).

The idea is that you use a selector to forward traffic from the ports of your worker nodes towards one (or multiple) pods.

I don't think Openshift's current documentation to be intuitive, so here are the steps:

- Make sure the port is open on your container: this is defined in the deployment config and you can then see on each pod.

- Make a new service as follows:

    ```YAML
    apiVersion: v1
    kind: Service
    metadata:
    name: [the service name]
    labels:
        name: [some label]
    spec:
    type: NodePort
    ports:
        - port: [the target port]
        nodePort: [pick some port to expose on the worker nodes]
        name: [a unique name for the service on the target port]
    selector:
        [a selector for the targeted pods, e.g "debug: myapp"]
    ```

- Go to your target pod(s)'s YAML and give it the same selector as in the last line of the service YAML above. Make sure the service has now detected that pod.

That's it, you can now connect to the pod's ports through any of your worker nodes - **not through your public Openshift domain!**

Node that using this method, you can add several ports at once, which is another advantage over public routes.
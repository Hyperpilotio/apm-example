# Evaluation of possible ways to derive application metrics

## Objectives

To explore potential approaches to derive application metrics

***Both Datadog and Wavefront's solution support statsD protocol to collect metrics from users' applications.
In the flowing blocks, I only use datadog's solution as representative.***

## Scenarios

### A. Only datadog agent is running in k8s cluster / VMs.
    Possible solutions:
    1. To start a proxy server that receives metrics from applications and forward a copy of metrics to our own metric collector (eg: statsD + snap-relay + snap).
        Then, send the metrics to datadog agent(s). (***A solution that haven't been explored.***)
    2. To modify the source code of applications that send metrics to datadog agent and our collector. (***This is not an option.***)

### B. Only statsD is running in k8s cluster / VMs.
    1. To modify the configuration file of statsD that can send copies of metrics to our collectors (snap-relay + snap) in the same cluster. (Has been proven)
        [Link](https://github.com/Hyperpilotio/resource-worker-service/tree/master/snap-k8s)

#### Where is the configuration file of statsD ?

    It depends on where the ops place the file. In the [README.md](https://github.com/etsy/statsd#installation-and-configuration) file of statsD documents how to start a statsD instance:
    `node stats.js /path/to/config`

   In [Hyperpilotio/resource-woker-service](https://github.com/Hyperpilotio/resource-worker-service), we use [Hyperpilotio/docker-statsd](https://github.com/Hyperpilotio/docker-statsd), a docker image, to launch a statsD instance in k8s cluster.
   The configuration file is placed in [`/etc/default/statsd.js`](https://github.com/Hyperpilotio/docker-statsd/blob/master/Dockerfile#L5).

#### What to modify statsD configuration?
    Take [Hyperpilotio/docker-statsd] as an example. The statsD is deployed as an independent container in kubernetes cluster.
    Modify the [`config.js`](https://github.com/Hyperpilotio/docker-statsd/blob/master/etc/config.js#L112-L116) file in the repository.
    ```{json}
        <...>
        backends: ["./backends/repeater", "./backends/console"],
        repeater: [{
            "host": "relay.default",
            "port": 6126
        }],
        <...>
    ```
    Next, build the docker image and push the image to our registry.

### C. Both datadog agent and self-hosted statsD are running in k8s cluster / VMs.
    1. To modify the configuration file of statsD that can send copies of metrics to our collectors (snap-relay + snap) in the same cluster.
        Check the steps documented in the above section and repositories of [resource-worker-service](https://github.com/Hyperpilotio/resource-worker-service) and [docker-statsd](https://github.com/Hyperpilotio/docker-statsd) for more information.


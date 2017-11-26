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

### C. Both datadog agent and self-hosted statsD are running in k8s cluster / VMs.
    1. To modify the configuration file of statsD that can send copies of metrics to our collectors (snap-relay + snap) in the same cluster.


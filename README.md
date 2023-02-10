# Cilium labs

You want to play / test with Cilium functionality, but don't known where to start, look no further, here's a easy path!

## Prerequisite

* Linux kernel 4.8.0+ (tested with 6.1.8)
* Docker engine (tested with 23.0.0)
* [Kind (tested with v0.17.0)](https://github.com/kubernetes-sigs/kind/tree/v0.17.0)
* [Cilium 1.12.6](https://github.com/cilium/cilium/tree/v1.12.6)
* [Hubble 0.11.1](https://github.com/cilium/hubble/tree/v0.11.1)

## Install local Kubernetes cluster with Kind

Retrieve your local IP address (assuming only 1 NIC with IP configured):

```bash
$ hostname -I | awk {'print $1'}
```

Configure Kind to use your IP address (edit kind-cilium.yaml). This is needed for our kube-proxy less Cilium installation. Cilium can replace kube-proxy, so we fully use all the functionality.

>:warning: This will bind the API server to your local IP, and is a security risk.

```bash
$ kind create cluster --config=kind-cilium.yaml
$ docker pull quay.io/cilium/cilium:v1.12.6
$ kind load docker-image quay.io/cilium/cilium:v1.12.6
```

This will create a cluster of 4 nodes:

* 1 control-plane
* 3 workers

## Install Cilium

We use helm to install Cilium:

```bash
$ helm repo add cilium https://helm.cilium.io/
$ helm upgrade --install cilium cilium/cilium --version 1.12.6  --namespace kube-system -f helm-values.yaml
```

Testing Cilium installation

```bash
$ cilium connectivity test
ℹ️  Monitor aggregation detected, will skip some flow validation steps
✨ [kind-kind] Creating namespace cilium-test for connectivity check...
✨ [kind-kind] Deploying echo-same-node service...
✨ [kind-kind] Deploying DNS test server configmap...
✨ [kind-kind] Deploying same-node deployment...
✨ [kind-kind] Deploying client deployment...
✨ [kind-kind] Deploying client2 deployment...
✨ [kind-kind] Deploying echo-other-node service...
✨ [kind-kind] Deploying other-node deployment...
[...]
```

Testing Hubble installation

Hubble is enabled in the helm values, you can test with:

```bash
$ cilium hubble port-forward &
$ hubble status
Healthcheck (via localhost:4245): Ok
Current/Max Flows: 14,896/16,380 (90.94%)
Flows/s: 40.94
Connected Nodes: 4/4
```

## Playground

You are now ready to play with Cilium. A good introduction to Cilium / Hubble is via the demo here: <https://docs.cilium.io/en/latest/gettingstarted/demo/>

Have a good day!

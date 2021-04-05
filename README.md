# First Keptn Demo

**SOURCE:** <https://dev.to/gabrieltanner/modern-continuous-delivery-on-kubernetes-for-developers-5chf>

## Prerequisites

- A K8s cluster (Kind cluster - <https://istio.io/latest/docs/setup/platform-setup/kind/>).
- ISTIO version >= 1.9.2.
  - For ISTIO installation works, you need to set Docker CPU/Memory a bit like this: 6 CPU and 16Gi (50% of my Macbook capacity)

## Installation

### Create KIND cluster

```sh
kind create cluster --config kind-istio.yaml
```

### Install ISTIO with Istio Operator

What is [ISTIO Operator](https://github.com/istio/istio/tree/master/operator)

<https://istio.io/latest/docs/ops/diagnostic-tools/istioctl/#install-hahahugoshortcode-s2-hbhb>

[IstioOperator Options](https://istio.io/latest/docs/reference/config/istio.operator.v1alpha1)


```sh
curl -sL https://istio.io/downloadIstioctl | sh -

export PATH=$HOME/.istioctl/bin:$PATH
```

Install Operator:

```sh
istioctl operator init
```

Get *default* profile:

```sh
./istio-1.9.2/bin/istioctl profile dump > default.yaml
```

#### Install IngressGateway as NodePort Service so that we can access the service from localhost 

Create the [*example*](manifests/example.yaml) profile with IngressGateway is `NodePort`.

- Here, we map the Service's *NodePort* to KIND's *containerPort*, so we can route "hostPort -> containerPort -> NodePort -> targetPort (pods)). **XXX:** this means we should have set KIND PortMappings before this step, if not, reset KIND by destroy/create the cluster.

- We also use *overlays* to patch the PodDisruptionBudget's `spec.minAvailable = 0` because we only have single Pod for IngressGW.

- Verify the *example* profile:
  ```sh
  istioctl manifest generate -f manifests/example.yaml
  ```

Start to install the *example* profile:

```sh
kubectl create ns istio-system

kubectl apply -f manifests/example.yaml
```

You need to wait while IstioOperator creating all the pods. 

Verify the Istio installation.

```sh
```

### Install with ISTIOCTL

```sh
curl -L https://istio.io/downloadIstio | ISTIO_VERSION=1.9.2 sh -

./istio-1.9.2/bin/istioctl install
```

Install Keptn v0.8.1

```sh
curl -sL https://get.keptn.sh | KEPTN_VERSION=0.8.1 bash
```

In this case you will install Keptn by using the continuous-delivery use-case.

```sh
keptn install --endpoint-service-type=ClusterIP --use-case=continuous-delivery
```

Configure Keptn with ISTIO:

```sh
curl -o configure-istio.sh https://raw.githubusercontent.com/keptn/examples/release-0.8.1/istio-configuration/configure-istio.sh

chmod +x configure-istio.sh
./configure-istio.sh
```

## Thanks to..

- <https://www.danielstechblog.io/running-istio-on-kind-kubernetes-in-docker/>
- [Modern continuous delivery on Kubernetes for developers](https://dev.to/gabrieltanner/modern-continuous-delivery-on-kubernetes-for-developers-5chf)

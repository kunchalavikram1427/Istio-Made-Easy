# Istio

## Introduction
Istio is an open-source service mesh platform that provides a way to manage and secure communication between microservices in a distributed application. It acts as a layer on top of the network infrastructure, offering advanced traffic management, security, and observability features without requiring changes to the application code.

Key features of Istio include:

1. **Traffic Management**: Istio allows fine-grained control over traffic behavior, including routing, load balancing, and failover. It supports advanced policies like A/B testing, canary releases, and circuit breaking.

2. **Security**: Istio provides robust security features such as mutual TLS (mTLS) for secure service-to-service communication, authentication, and authorization. It helps enforce policies to protect services from vulnerabilities.

3. **Observability**: Istio offers tools for monitoring and observing the health of microservices, including metrics, distributed tracing, and logging. This helps in diagnosing issues and understanding system behavior.

4. **Policy Enforcement**: Istio enables the definition and enforcement of policies for access control, rate limiting, and quotas, helping ensure compliance and resource management.

5. **Service Discovery and Load Balancing**: Istio integrates with service discovery mechanisms to route traffic based on service availability and health, improving system resilience and reliability.

Istio achieves these capabilities by deploying sidecar proxies (usually Envoy) alongside each service in the mesh. These proxies intercept and manage all network traffic between services, applying the desired policies and configurations.

## Links
- Architecture v1.4: https://istio.io/v1.4/docs/ops/deployment/architecture
- Distributed tracing: https://istio.io/latest/docs/tasks/observability/distributed-tracing/overview/
- Gateway API: https://gateway-api.sigs.k8s.io/
- Ambient mode: https://www.cncf.io/blog/2024/03/19/istio-announces-the-beta-release-of-ambient-mode/

## Installation and setup

### Cluster Setup
Start minikube cluster
```sh
minikube start --driver=docker --memory=4096 --cpus=4
```
### (Optional) Install Metrics server and Skooner dashboard
Install metrics server and Skooner dashboard
```sh
kubectl apply -f kubernetes-dashboard/metrics-server.yaml
kubectl apply -f kubernetes-dashboard/skooner_dashboard.yml
```
Get service account token to access skooner dashboard
```sh
kubectl create token skooner-sa -n default
```

### Install Istio
Istio can be downloaded from release page or via `curl` commands
- https://istio.io/latest/about/faq/#install-method-selection
- https://istio.io/latest/docs/setup/getting-started/#download

### Test setup
Install Istio demo profile without gateways and enable sidecar injection
```sh
istioctl install -f samples/bookinfo/demo-profile-no-gateways.yaml -y
kubectl label namespace default istio-injection=enabled
```
Install the Kubernetes [Gateway API](https://gateway-api.sigs.k8s.io/) CRDs
```sh
kubectl get crd gateways.gateway.networking.k8s.io &> /dev/null || \
{ kubectl kustomize "github.com/kubernetes-sigs/gateway-api/config/crd?ref=v1.1.0" | kubectl apply -f -; }
```
Check CRDs
```sh
$ kubectl get crds
NAME                                        CREATED AT
authorizationpolicies.security.istio.io     2024-08-03T14:21:56Z
destinationrules.networking.istio.io        2024-08-03T14:21:56Z
envoyfilters.networking.istio.io            2024-08-03T14:21:56Z
gatewayclasses.gateway.networking.k8s.io    2024-08-03T14:24:22Z
gateways.gateway.networking.k8s.io          2024-08-03T14:24:22Z
gateways.networking.istio.io                2024-08-03T14:21:56Z
grpcroutes.gateway.networking.k8s.io        2024-08-03T14:24:22Z
httproutes.gateway.networking.k8s.io        2024-08-03T14:24:23Z
peerauthentications.security.istio.io       2024-08-03T14:21:56Z
proxyconfigs.networking.istio.io            2024-08-03T14:21:56Z
referencegrants.gateway.networking.k8s.io   2024-08-03T14:24:23Z
requestauthentications.security.istio.io    2024-08-03T14:21:56Z
serviceentries.networking.istio.io          2024-08-03T14:21:56Z
sidecars.networking.istio.io                2024-08-03T14:21:56Z
telemetries.telemetry.istio.io              2024-08-03T14:21:56Z
virtualservices.networking.istio.io         2024-08-03T14:21:56Z
wasmplugins.extensions.istio.io             2024-08-03T14:21:56Z
workloadentries.networking.istio.io         2024-08-03T14:21:56Z
workloadgroups.networking.istio.io          2024-08-03T14:21:56Z
```
Deploy the sample application
```sh
kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
kubectl get po,svc,sa
```
Validate the application
```
kubectl exec "$(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -c ratings -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"
```

### Istio Profiles
```
istioctl profile list
istioctl profile dump default > default_profile.yml 
kubectl apply -f default_profile.yml
istioctl apply -f default_profile.yml
```
```sh
istioctl install 
istioctl install --set profile=demo -y
istioctl verify-install
```
```sh
istioctl manifest generate --set profile=demo > istio-installation.yaml
kubectl apply -f istio-installation.yaml
istioctl verify-install -f istio-installation.yaml – Verify installation
kubectl get all -n istio-system – Check deployed objects
istioctl x uninstall --purge
```
```sh
istioctl analyze
```

### Deploy Kiali, Jaeger

### Enable Sidecar Injection
```sh
Label Namespace: kubectl label namespace default istio-injection=enabled

kubectl label namespace default istio-injection-
kubectl label namespace default istio-injection=disabled
```
---
marp: false
inlineSVG: true
header: 'Kubernetes Workshop part 2'
footer: 'Ben Mathews - January 2021'
backgroundColor: #B9C9A9
---

# Kubernetes Workshop part 1

## The basics from a users perspective

https://github.com/benmathews/KubernetesWorkshop

---

# Prerequisites

To complete this workshop you need some setup first.

- Install [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/). And although not required, it is very helpful to have autocomplete configured.

``` bash
# bash-completion package should be installed first.
source <(kubectl completion bash) 
echo "source <(kubectl completion bash)" >> ~/.bashrc 
```

Clone this repo to get a sample app, Dockerfile, and kube config.

``` bash
git clone git@github.com:benmathews/KubernetesWorkshop.git
```

---

# Prerequisites - Kubectl Config

Set up the the configuration that will connect kubectl to your cluster with

``` bash
cp ~/.kube/config ~/.kube/config.bak
cp <location of repo>/k8sconfig ~/.kube/config
```

Before attending the workshop, you should be able to run this command and get a response.

``` bash
$ kubectl version
Client Version: version.Info{Major:"1", ...
Server Version: version.Info{Major:"1", ...
```
---
# Prerequisets - /etc/hosts

Add this to your `/etc/hosts` file.

```
10.1.31.199 workshop-k8s
```
---

# Review - Pod

Shared storage and network resources

![Pod diagram](out/diagrams/pod/pod.png)

---

# Review - Deployment

Scaling and rollout

![Deployment diagram](out/diagrams/deployment/deployment.png)

---

# Review - Service

Stable network address

![Service diagram](out/diagrams/service/service.png)

---

# Review - Service

![Service diagram](out/diagrams/serviceall/serviceall.png)

---

# Ingress

![Ingress diagram](out/diagrams/ingress/ingress.png)

---

# Starting point for workshop

- Create a namespace.
- Set the namespace to be default in your `~/.kube/config`.
- Apply the startup yaml.
  - A deployment with two pods
  - A service referencing it

``` bash
kubectl apply -f Workshop2Yaml
```

---

# Ingress example

``` yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: demodeploy
spec:
  rules:
  - host: hostname.com
    http:
      paths:
      - backend:
          serviceName: someservice
          servicePort: 8888
        path: /<namespace>/
```

---

# Create ingress to expose your service

- Start from example on previous page.
- Modify the serviceName, servicePort, and path.

## Useful commands

```
kubectl apply -f <filename>
kubectl describe ingress demodeploy
stern -n ingress -s1s .
```

---

# Rollout / Rollback practice

Practice modifying your deployment.

``` bash
kubectl edit deployment demodeploy
kubectl get replicaset
k rollout history deployment demodeploy
```

---
# Rollout demo

Demo only everyone running will exceed the capacity of the cluster.

``` bash
k scale deploy demodeploy --replicas=50
watch 'kubectl get deployment;kubectl get pods'
# Change container name to trigger rollout
kubectl rollout status deployment demodeploy
```

---

# Rollout demo continued

<!-- Explain. Default is to aggressive, edit to more conservitive and trigger new rollout. -->
``` yaml
strategy:
  rollingUpdate:
    maxSurge: 25%
    maxUnavailable: 25%
```

---

# Configmap and Secret 

## Configmap

An API object used to store non-confidential data in key-value pairs. Pods can consume ConfigMaps as environment variables, command-line arguments, or as configuration files in a volume.

## Secret 

 Let you store and manage sensitive information, such as passwords, OAuth tokens, and ssh keys. Storing confidential information in a Secret is safer and more flexible than putting it verbatim in a Pod definition or in a container image. 

---

# Configmap / Secret example

`kubectl create help secret sample --from-literal=foo=bar`
``` yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sample
data:
  foo: bar
```
`kubectl create secret generic sample --from-literal=foo=bar`
``` yaml
apiVersion: v1
kind: Secret
metadata:
  name: sample
data:
  foo: YmFy
```
---
Explain the ability to use as environmental variable, runtime variable, volume.
Explain volumes
---
 -Add a configmap and secret and reference them in the deployment
 -Use an environmental variable in the demo app
 -Add a liveness probe to the deployment
	 -Observe it failing w/ k describe
	 -Implement the probe and see it rollout

 -Delete  the deployment, service, ingress, and namespace
	 -Confirm they are gone
	resources request/limits


https://www.digitalocean.com/community/tutorials/webinar-series-a-closer-look-at-kubernetes
https://kubernetes.io/docs/tutorials/kubernetes-basics/

./pasted_image.png
https://www.reddit.com/r/kubernetes/comments/k8kqdr/overview_of_kubernetes_architecture/

./pasted_image001.png
https://www.reddit.com/r/kubernetes/comments/k26je7/overview_of_builtin_kubernetes_workload_resources/


 -https://www.digitalocean.com/community/tutorials/how-to-create-a-kubernetes-cluster-using-kubeadm-on-ubuntu-18-04#step-7-%E2%80%94-running-an-application-on-the-cluster

 Kubectl Debug Graduates to Beta
The kubectl alpha debug features graduates to beta in 1.20, becoming kubectl debug. The feature provides support for common debugging workflows directly from kubectl. Troubleshooting scenarios supported in this release of kubectl include:

Troubleshoot workloads that crash on startup by creating a copy of the pod that uses a different container image or command.
Troubleshoot distroless containers by adding a new container with debugging tools, either in a new copy of the pod or using an ephemeral container. (Ephemeral containers are an alpha feature that are not enabled by default.)
Troubleshoot on a node by creating a container running in the host namespaces and with access to the host’s filesystem. Note that as a new built-in command, kubectl debug takes priority over any kubectl plugin named “debug”. You must rename the affected plugin.

---

![bg](questions.jpg)

---

# Assignment

- Modify the sample node program to return "Hello Universe".
- Create a new version of the docker image and push it to your local registry.
- Create a new deployment to serve the new version.
- Modify the service so that it routes traffic to both deployments

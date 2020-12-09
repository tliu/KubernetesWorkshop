# Kubernetes Workshop

## Prerequisites

### Kubernetes Cluster

To complete this workshop you need a working kubernetes cluster and registry running on your laptop. Your options include

- [Minikube](https://minikube.sigs.k8s.io/docs/) - mature, but perhaps a little heavy. Officially supported
- [MicroK8S](https://microk8s.io/) - lightweight, from Canonical
- [K3S](https://k3s.io/) - super lightweight, from Rancher
- [Docker Desktop](https://www.docker.com/products/docker-desktop) - built into Windows and Mac docker install

I strongly recommend MicroK8S (and some instructions will be MicroK8S specific). But any of these should work.

### Kubectl

You also need [kubectl installed](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

And although not required, it is very helpful to have autocomplete configured.

```
# bash-completion package should be installed first.
source <(kubectl completion bash) 
echo "source <(kubectl completion bash)" >> ~/.bashrc 
```

### Configuration

Set up the the configuration that will connect kubectl to your cluster with 

```
# backup your existing config first
microk8s config > ~/.kube/config
```

Before attending the workshop, you should be able to run this command and get a response.

```
$ kubectl version
Client Version: version.Info{Major:"1", Minor:"18", ...
Server Version: version.Info{Major:"1", Minor:"17", ...
```
### Registration

Set up a registry 

```
microk8s enable registry
```

Add to /etc/docker/daemon.json
```
{
  "insecure-registries" : ["localhost:32000"]
}
```

```
sudo systemctl restart docker
```

### Other tools

- Install [stern](https://github.com/wercker/stern).

Clone this repo to get a sample app and container

``` bash
git clone git@github.com:benmathews/KubernetesWorkshop.git
```

## Marp

Presentation is written using marp

https://github.com/marp-team/marp-cli/

### Install

```
npm install -g @marp-team/marp-cli
```
### Start

```
npx @marp-team/marp-cli -sw .
```

### Render PDF

```
npx @marp-team/marp-cli codefreshPresentation.md --pdf --allow-local-files
```

## TODO


 -Create Ingress
	 -View nginx via ingress
 -Modify service to ClusterIP
	 -Observe that no longer available directly
 -Modify the deployment and watch rollout
 -Rollback
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


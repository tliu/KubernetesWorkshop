# Kubernetes Workshop

## Prerequisites

To complete this workshop you need some setup first.

- Install [stern](https://github.com/wercker/stern).
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

### Kubectl Config

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

### Registry Config

#### Linux

Enable access to the registry on Linux

Add to /etc/docker/daemon.json

``` json
{
  "insecure-registries" : ["10.1.31.199:32000"]
}
```

``` bash
sudo systemctl restart docker
```

#### MacOS

Add this to the Docker configuration

``` json
{
  "insecure-registries" : ["10.1.31.199:32000"]
}
```

Add the above json to the `Docker Engine` configuration in `Preferences`

- From the Mac status bar, go to `Docker | Preferences`
- Navigate to the `Docker Engine` section
- Integrate the above json into the displayed json
- Click `Apply & Restart`

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

# Docker images

* 10.1.31.199:32000/demowebapp:v1.2.1 - glob on path
* 10.1.31.199:32000/demowebapp:v1.2.2 - FRIENDS env variable

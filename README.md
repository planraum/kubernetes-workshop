# kubernetes-workshop

The Planraum Kubernetes workshop is a 2h intro to Kubernetes. It includes a short "What is Kubernetes", but then goes straight to the hands-on part. This requires a few tools to be installed. So before starting this please execute the pre-flight check in the prerequitits section.

## Prerequisits

The workshop requires a UNIX style operation system. Basically something with a terminal. So Linux, Mac or probably even Windows with WSL (not recommended!) should work. The following tools should be installed as they are frequently used. Follow the links for instructions.

* [docker](https://docs.docker.com/get-docker/) (including a running daemon to build contianers)
* [Vagrant](https://www.vagrantup.com/docs/installation/)
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
* [helm](https://helm.sh/docs/intro/install/)

### pre-flight check

Follow the following steps to verify everything is working. Start in the `pre-flight` subfolder.

```sh
cd pre-flight
```

Start a vm in VirtualBox with vagrant:

```sh
$ vagrant up
==> vagrant: A new version of Vagrant is available: 2.2.7 (installed version: 2.2.6)!
==> vagrant: To upgrade visit: https://www.vagrantup.com/downloads.html

Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'centos/7'...
[...]
$ # You may now ssh into the machine
$ vagrant ssh
[vagrant@localhost ~]$ # press ctrl + d to exit
```

Check whether docker is working using my [super awesome troubleshooting container ;)](https://github.com/iptizer/swiss):

```sh
docker run -it --rm iptizer/swiss swiss -- /bin/bash
```

Check kubectl & helm:

```sh
$ kubectl version
Client Version: version.Info{Major:"1", Minor:"18", GitVersion:"v1.18.1", GitCommit:"7879fc12a63337efff607952a323df90cdc7a335", GitTreeState:"clean", BuildDate:"2020-04-10T21:53:58Z", GoVersion:"go1.14.2", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"17", GitVersion:"v1.17.4", GitCommit:"8d8aa39598534325ad77120c120a22b3a990b5ea", GitTreeState:"clean", BuildDate:"2020-03-12T20:55:23Z", GoVersion:"go1.13.8", Compiler:"gc", Platform:"linux/amd64"}
$ helm version
version.BuildInfo{Version:"v3.1.2", GitCommit:"d878d4d45863e42fd5cff6743294a11d28a9abce", GitTreeState:"clean", GoVersion:"go1.13.8"}
```

## Kubernetes explained

Short intro to Kubernetes objects //TODO.

## Hands-on cluster

Few things with basic Kubernetes objects. //TODO

## Hands-on Helm

The power of helm using the [Helm hub](https://hub.helm.sh). //TODO

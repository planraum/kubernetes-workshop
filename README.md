# kubernetes-workshop

The Planraum Kubernetes workshop is a 2h intro to Kubernetes. It includes a short "What is Kubernetes", but then goes straight to the hands-on part. This requires a few tools to be installed. So before starting this please execute the pre-flight check in the prerequitits section.

## Prerequisits

The workshop requires a UNIX style operation system. Basically something with a terminal. So Linux, Mac or probably even Windows with WSL (not recommended!) should work. The following tools should be installed as they are frequently used. Follow the links for instructions.

* [docker](https://docs.docker.com/get-docker/) (including a running daemon to build contianers)
* [Vagrant](https://www.vagrantup.com/docs/installation/)
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
* [helm](https://helm.sh/docs/intro/install/)
* [kind](https://kind.sigs.k8s.io/docs/user/quick-start/)

### aliases

The following aliases are recommended:

```sh
alias k=kubectl
alias kc="kubectl config set-context --current --namespace"
source <(kubectl completion zsh)  # setup autocomplete in zsh into the current shell
```

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
$ vagrant destroy -f # delete the vagrant box
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

Short intro to Kubernetes. We will start [here](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/) and work our way through the documentation.

## Create a cluster

Two ways of creating a local k8s cluster.

### Kind

Kind installs a Kubernetes Cluster inside locally running Docker containers. At first [install kind](https://kind.sigs.k8s.io/docs/user/quick-start/).

Then create the cluster with:

```sh
kind create cluster
```

This will take some time as the images will have to be pulled for the first time. After that, everything should be configured and is ready to go. You should be able to query the clsuter and execute a few commands:

```sh
kubectl get ns
kubectl get pods -A
```

You may now proceed to our hands-on part. Execute the follwoing command to delete the cluster:

```sh
kind delete cluster
```

### vagrant-troubleshoot

*Note: This is a more advanced and more realistic approach as real nodes are created. If you just want to try out, use kind.*

For this approach we use a project targetted for training and solving problems. This project uses vagrant to spin up the cluster. Start in the repositories root folder, I added the files to *.gitignore*.

```sh
git clone https://github.com/wuestkamp/cka-example-environments
cd cka-example-environments/cluster1/ && vagrant up
# takes a few minutes
vagrant ssh cluster1-master1
sudo su - root # kubectl is only configured for root user
```

When you are ready the following training questions may be accomplished:

* for the [**Certified Kubernetes Application Developer**](https://codeburst.io/kubernetes-ckad-weekly-challenges-overview-and-tips-7282b36a2681)
* for the [Certified Kubernetes Administrator](https://codeburst.io/kubernetes-ckad-weekly-challenges-overview-and-tips-7282b36a2681)

## Hands-on

Now we get on actually doing something. First with plain k8s, after that a little bit higher layer using helm.

### Kubernetes

You may start [my troubleshooting container](https://github.com/iptizer/swiss) in your cluster (kind or vagrant deosn't matter):

```sh
kubectl run -it --restart=Never --rm --generator=run-pod/v1 --image=iptizer/swiss swiss -- /bin/bash
env # check the env variables
```

Up until no we only started a container in a very fancy and complicated way. Let's approach the usage of Kubernetes by starting a nginx deployment.

```sh
kubectl create ns planraum
# using the alias from above
kc planraum
kubectl apply -f examples/nginx.yml
kubectl get pods -w # watch the creation of the pod
kubectl get svc nginx -o yaml
```

With a correct routing in place we could theoretically access the service directly. It may not work due to a setup with kind or other routing issues.

```sh
k get svc nginx -o jsonpath="{.spec.clusterIP }{':'}{.spec.ports[0].nodePort}"
```

To create a port-fiorwarding we can execute the following:

```sh
kubectl port-forward svc/nginx 8080:80
```

Use this link to access the test page: [http://127.0.0.1:8080/](http://127.0.0.1:8080/)

### helm

Now we are getting even toa higher layer. Using helm. Check [https://hub.helm.sh/](https://hub.helm.sh/) to find an application you find useful. i.e. for *"wordpress"*.

We are using the bitnami helm chart for this example.

```sh
k create ns wordpress
kc wordpress # with the alias from above to deploy to planraum namespace
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm install planraum-wordpress bitnami/wordpress -f examples/wordpress.yml
```

Values that should work if propper routing is in place are printed to cms. Otherwise we can of course use port forwarding. We use the pod here as the svc brings some kind of failure in my tests.

```sh
kubectl get secret --namespace wordpress planraum-wordpress -o jsonpath="{.data.wordpress-password}" | base64 --decode # get credentials
k get po | grep -v mariadb
# use this pod to forward
kubectl port-forward --namespace wordpress po/INSERT_PODNAME_HERE 8080:8080
```

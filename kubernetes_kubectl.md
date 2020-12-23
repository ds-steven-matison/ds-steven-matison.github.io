---
layout: default
title: Kubectl
description: A blog all about kubectl
permalink: /kubernetes/kubectl/
---

# What is Kubectl?

The kubectl, pronounced "kube-cuddle", is the command line tool to control your Kubernetes clusters.  Be sure to study the official [kubernetes.io](https://kubernetes.io/docs/home/) documentation for kubectl [here](https://kubernetes.io/docs/reference/kubectl/overview/).

First lets learn to read the manual:
```js 
kubectl
kubectl controls the Kubernetes cluster manager.

 Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
  create        Create a resource from a file or from stdin.
  expose        Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
  run           Run a particular image on the cluster
  set           Set specific features on objects

Basic Commands (Intermediate):
  explain       Documentation of resources
  get           Display one or many resources
  edit          Edit a resource on the server
  delete        Delete resources by filenames, stdin, resources and names, or by resources and label selector

Deploy Commands:
  rollout       Manage the rollout of a resource
  scale         Set a new size for a Deployment, ReplicaSet or Replication Controller
  autoscale     Auto-scale a Deployment, ReplicaSet, or ReplicationController

Cluster Management Commands:
  certificate   Modify certificate resources.
  cluster-info  Display cluster info
  top           Display Resource (CPU/Memory/Storage) usage.
  cordon        Mark node as unschedulable
  uncordon      Mark node as schedulable
  drain         Drain node in preparation for maintenance
  taint         Update the taints on one or more nodes

Troubleshooting and Debugging Commands:
  describe      Show details of a specific resource or group of resources
  logs          Print the logs for a container in a pod
  attach        Attach to a running container
  exec          Execute a command in a container
  port-forward  Forward one or more local ports to a pod
  proxy         Run a proxy to the Kubernetes API server
  cp            Copy files and directories to and from containers.
  auth          Inspect authorization

Advanced Commands:
  diff          Diff live version against would-be applied version
  apply         Apply a configuration to a resource by filename or stdin
  patch         Update field(s) of a resource using strategic merge patch
  replace       Replace a resource by filename or stdin
  wait          Experimental: Wait for a specific condition on one or many resources.
  convert       Convert config files between different API versions
  kustomize     Build a kustomization target from a directory or a remote url.

Settings Commands:
  label         Update the labels on a resource
  annotate      Update the annotations on a resource
  completion    Output shell completion code for the specified shell (bash or zsh)

Other Commands:
  alpha         Commands for features in alpha
  api-resources Print the supported API resources on the server
  api-versions  Print the supported API versions on the server, in the form of "group/version"
  config        Modify kubeconfig files
  plugin        Provides utilities for interacting with plugins.
  version       Print the client and server version information

Usage:
  kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).
```

# How Do I Use kubectl?

I am working with kubectl as I learn more about [kubernetes](/kubernetes/cass-operator/), [k8ssandra](/k8ssandra/), and [Astra Service Broker](/astra/service/broker/).  Normally I keep a personal document of commands I can reference quickly, but now with this blog, I can use this page for those future references.  Now so can you!

Check out some commands I have used recently:

```js
kubectl version

kubectl get pods --all-namespaces

kubectl -n <namespace> get pods

kubectl get nodes -o wide

kubectl get secrets -n cass-operator

kubectl get secrets devdb -o yaml

kubectl get serviceinstances

kubectl get serviceinstances devdb

kubectl get servicebindings

kubectl get events

kubectl -n cass-operator get events

kubectl get pvc

kubectl -n cass-operator get pvc

kubectl get storageclasses

kubectl get sc

kubectl get deployments --namespace=monitoring

kubectl -n cass-operator get pods --selector name=cass-operator -o yaml

kubectl -n cass-operator get pods

kubectl -n cass-operator exec cluster1-dc1-default-sts-2 -- keytool -list -keystore keystore.jks -storepass dc1

kubectl logs -n <namespace> <pod>

kubectl -n cass-operator logs cluster1-dc1-rack1-sts-0 server-system-logger

kubectl -n <namespace> logs <pod> server-system-logger

kubectl logs -n cass-operator cluster1-dc1-default-sts-0 -c server-config-init

kubectl logs -n cass-operator cluster1-dc1-default-sts-0 -c server-system-logger

kubectl logs -n cass-operator cluster1-dc1-default-sts-0 -c cassandra

kubectl -n <namespace> exec <pod> -- keytool -list -keystore keystore.jks -storepass dc1

kubectl get serviceinstances devdb

kubectl apply -f astra-service-binding.yaml

kubectl create -f astra-service-broker.yaml

kubectl delete -f astra-service-broker.yaml

kubectl delete servicebinding devdb-azure

kubectl delete serviceinstance devdb-azure

kubectl exec -n cass-operator -i -t -c cassandra cluster1-dc1-default-sts-0 -- cqlsh -u cluster1-superuser -p <password>

kubectl port-forward prometheus-deployment-54686956bd-nhz2s 8080:9090 -n monitoring

kubectl -n cass-operator exec --stdin cluster1-dc1-default-sts-0 -- /bin/bash

```
# What's Next?

Have some commands I am missing?  I would love to see what you got.  Reach out and lets talk kubectl!

{% include kubernetes_help.html %}
---
layout: post
title: "Kubernetes Cookbook"
date: "2019-02-20 12:35"
categories:
    - docker
    - kubernetes
tags:
    - devops
comments: true
---

Kubernetes coordinates a highly available cluster of computers that are connected to work as a single unit.

Kubernetes automates the distribution and scheduling of application containers across a cluster in a more efficient way.

A Kubernetes cluster consists of two types of resources:
- The Master coordinates the cluster
- Nodes are the workers that run applications

The Master is responsible for managing the cluster.

A node is a VM or a physical computer that serves as a worker machine in a Kubernetes cluster.

The nodes communicate with the master using the Kubernetes API, which the master exposes.

### Comment ...

- [Afficher la version de Minikube](#afficher-la-version-de-minikube)
- [Lancer une VM contenant un cluster Kubernetes](#lancer-une-vm-contenant-un-cluster-kubernetes)
- [Ouvrir le dashboard Kubernetes dans un navigateur](#ouvrir-le-dashboard-kubernetes-dans-un-navigateur)
- [Afficher la version de kubectl](#afficher-la-version-de-kubectl)
- [Afficher la configuration de kubectl](#afficher-la-configuration-de-kubectl)
- [Afficher les informations du cluster Kubernetes](#afficher-les-informations-du-cluster-kubernetes)
- [Afficher les noeuds du cluster Kubernetes](#afficher-les-noeuds-du-cluster-kubernetes)
- [Créer un Deployment permettant d'exécuter son application dans un cluster Kubernetes](#cr%C3%A9er-un-deployment-permettant-dex%C3%A9cuter-son-application-dans-un-cluster-kubernetes)
- [Afficher les Déploiements](#afficher-les-d%C3%A9ploiements)
- [Afficher les Pods](#afficher-les-pods)
- [Créer Kubernetes Service (exposer le container d'un Pod à l'extérieur du cluster)](#cr%C3%A9er-kubernetes-service-exposer-le-container-dun-pod-%C3%A0-lext%C3%A9rieur-du-cluster)
- [Afficher les Kubernetes Services](#afficher-les-kubernetes-services)
- [Utiliser le Kubernetes Service via Minikube](#utiliser-le-kubernetes-service-via-minikube)
- [Afficher URL des services exposés par Minikube](#afficher-url-des-services-expos%C3%A9s-par-minikube)
- [Supprimer un Kubernetes Service](#supprimer-un-kubernetes-service)
- [Supprimer un Deployment](#supprimer-un-deployment)
- [Références](#r%C3%A9f%C3%A9rences)

#### Afficher la version de Minikube

```bash
minikube version
```

#### Lancer une VM contenant un cluster Kubernetes

```bash
minikube start
```

#### Ouvrir le dashboard Kubernetes dans un navigateur

```bash
minikube dashboard
```

#### Afficher la version de kubectl

```bash
$ kubectl version
Client Version: version.Info{Major:"1", Minor:"10", GitVersion:"v1.10.7", GitCommit:"0c38c362511b20a098d7cd855f1314dad92c2780", GitTreeState:"clean", BuildDate:"2018-08-20T10:09:03Z", GoVersion:"go1.9.3", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"13", GitVersion:"v1.13.3", GitCommit:"721bfa751924da8d1680787490c54b9179b1fed0", GitTreeState:"clean", BuildDate:"2019-02-01T20:00:57Z", GoVersion:"go1.11.5", Compiler:"gc", Platform:"linux/amd64"}
```

`kubectl` est un CLI permettant d'interagir avec le cluster Kubernetes via Kubernetes API.

#### Afficher la configuration de kubectl

```bash
kubectl config view
```

#### Afficher les informations du cluster Kubernetes

```bash
$ kubectl cluster-info
Kubernetes master is running at https://192.168.99.100:8443
KubeDNS is running at https://192.168.99.100:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
```

#### Afficher les noeuds du cluster Kubernetes

```bash
$ kubectl get nodes
NAME       STATUS    ROLES     AGE       VERSION
minikube   Ready     master    5m        v1.13.3
```

Cette commande affiche tous les noeuds pouvant être utilisés pour héberger nos applications.
Le statut `Ready` indique que notre noeud est prêt à accepter des déploiements.

#### Créer un Deployment permettant d'exécuter son application dans un cluster Kubernetes

Afin de créer un Deployment, il faut spécifier l'image du container de notre application et le nombre de Replicas à exécuter.
Le nombre de Replicas peut être modifié par la suite.

Un Pod est un groupe de 1 ou plusieurs Containers, liés ensemble afin de faciliter leur administration et leur communication.
Un Kubernetes Deployment vérifie la santé du Pod et redémarre le container du Pod si il se termine. Les Deployments sont le moyens recommandés de gérer la création et la scablabilité des Pods.

```bash
kubectl create deploy <deployment-name> --image=<image-name>
kubectl create deploy hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

#### Afficher les Déploiements

```bash
kubectl get deployments
```

#### Afficher les Pods

```bash
kubectl get pods
```

#### Exporter le nom du Pod dans une variable d'environnement

```bash
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
```

#### Afficher les logs d'un Pod

```bash
kubectl logs <pod-name>
kubectl logs $POD_NAME
```

#### Executer une commande directement dans un container d'un Pod

```bash
kubectl exec <pod-name> [-c <container-name>] <command>

# afficher les variables d'environnement du container
kubectl exec $POD_NAME env

# lancer une session bash dans le container
kubectl exec -ti $POD_NAME bash
```

Note: le nom du container peut être omis si il n'y a qu'un seul container dans le Pod

#### Créer un serveur proxy entre localhost et le Kubernetes API Server

```bash
kubectl proxy
```


#### Créer Kubernetes Service (exposer le container d'un Pod à l'extérieur du cluster)

Par défaut, un Pod n'est accessible que par son IP interne dans un cluster Kubernetes. Afin de rendre un container accessible à l'extérieur du réseau virtual Kubernetes, il faut exposer le Pod en tant que Kubernetes Service.

```bash
kubectl expose deployment <deployment-name> --type=LoadBalancer --port=8080 --name=<service-name>
kubectl expose deployment hello-node --type=LoadBalancer --port=8080 --name=hello-node-service

# Pour plus d'informations ou d'examples
kubectl expose --help
```

Le flag `--type=LoadBalancer` indique qu'on souhaite exposer le service à l'extérieur du cluster.
Le nom du service est optionnel. Il prendra le nom du déploiement par défaut si non renseigné.

#### Afficher les Kubernetes Services

```bash
kubectl get services
```

#### Utiliser le Kubernetes Service via Minikube

```bash
minikube service <service-name>
minikube service hello-node-service
```

#### Afficher URL des services exposés par Minikube

```bash
minikube service list
```

#### Supprimer un Kubernetes Service

```bash
kubectl delete service <service-name>
kubectl delete service hello-node-service
```

#### Supprimer un Deployment

```bash
kubectl delete deployment <deployment-name>
kubectl delete deployment hello-node
```

#### Afficher les informations d'une ressource

```bash
kubectl describe <resource-name>
kubectl describe all
kubectl describe -h
```

Par exemple pour voir, entre autres, les containers	qu'il y a dans un Pod:

```bash
kubectl describe pods
```

### Références
- [https://kubernetes.io/docs/tutorials/kubernetes-basics/](https://kubernetes.io/docs/tutorials/kubernetes-basics/){:target="_blank"}
- [https://kubernetes.io/docs/reference/kubectl/overview/](kubectl overview){:target="_blank"}

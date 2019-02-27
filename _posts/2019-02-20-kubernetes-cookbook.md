---
layout: post
title: "Kubernetes Cookbook"
date: "2019-02-20 12:35"
categories:
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

- [Comment ...](#comment)
	- [Créer un cluster](#cr%C3%A9er-un-cluster)
		- [Afficher la version de Minikube](#afficher-la-version-de-minikube)
		- [Lancer une VM contenant un cluster Kubernetes](#lancer-une-vm-contenant-un-cluster-kubernetes)
		- [Ouvrir le dashboard Kubernetes dans un navigateur](#ouvrir-le-dashboard-kubernetes-dans-un-navigateur)
		- [Afficher la version de kubectl](#afficher-la-version-de-kubectl)
		- [Afficher la configuration de kubectl](#afficher-la-configuration-de-kubectl)
		- [Afficher les informations du cluster Kubernetes](#afficher-les-informations-du-cluster-kubernetes)
	- [Déployer une application](#d%C3%A9ployer-une-application)
		- [Afficher les noeuds du cluster Kubernetes](#afficher-les-noeuds-du-cluster-kubernetes)
		- [Créer un Deployment permettant d'exécuter son application dans un cluster Kubernetes](#cr%C3%A9er-un-deployment-permettant-dex%C3%A9cuter-son-application-dans-un-cluster-kubernetes)
		- [Afficher les Déploiements](#afficher-les-d%C3%A9ploiements)
		- [Créer un serveur proxy entre localhost et le Kubernetes API Server](#cr%C3%A9er-un-serveur-proxy-entre-localhost-et-le-kubernetes-api-server)
	- [Explorer l'application](#explorer-lapplication)
		- [Afficher les Pods](#afficher-les-pods)
		- [Exporter le nom du Pod dans une variable d'environnement](#exporter-le-nom-du-pod-dans-une-variable-denvironnement)
		- [Afficher les logs d'un Pod](#afficher-les-logs-dun-pod)
		- [Afficher les informations d'une ressource](#afficher-les-informations-dune-ressource)
		- [Executer une commande directement dans un container d'un Pod](#executer-une-commande-directement-dans-un-container-dun-pod)
	- [Exposer l'application](#exposer-lapplication)
		- [Créer un Service (exposer le container d'un Pod à l'extérieur du cluster via un Kubernetes Service)](#cr%C3%A9er-un-service-exposer-le-container-dun-pod-%C3%A0-lext%C3%A9rieur-du-cluster-via-un-kubernetes-service)
		- [Exporter port exposé par le Service dans une variable d'environnement](#exporter-port-expos%C3%A9-par-le-service-dans-une-variable-denvironnement)
		- [Ping une application exposée](#ping-une-application-expos%C3%A9e)
		- [Afficher les Kubernetes Services](#afficher-les-kubernetes-services)
		- [Utiliser le Kubernetes Service via Minikube](#utiliser-le-kubernetes-service-via-minikube)
		- [Afficher URL des services exposés par Minikube](#afficher-url-des-services-expos%C3%A9s-par-minikube)
		- [Afficher les ressources (e.g. Pods/Services/Deployments) ayant un label particulier](#afficher-les-ressources-eg-podsservicesdeployments-ayant-un-label-particulier)
		- [Appliquer un label à une ressource](#appliquer-un-label-%C3%A0-une-ressource)
	- [Scale l'application](#scale-lapplication)
		- [Scaler up/down un Déploiement avec des Replicas](#scaler-updown-un-d%C3%A9ploiement-avec-des-replicas)
	- [Nettoyer des ressources](#nettoyer-des-ressources)
		- [Supprimer un Kubernetes Service](#supprimer-un-kubernetes-service)
		- [Supprimer un Deployment](#supprimer-un-deployment)
		- [Stopper la VM Minikube](#stopper-la-vm-minikube)
		- [Supprimer la VM Minikube](#supprimer-la-vm-minikube)
- [Références](#r%C3%A9f%C3%A9rences)

#### Créer un cluster

##### Afficher la version de Minikube

```bash
minikube version
```

##### Lancer une VM contenant un cluster Kubernetes

```bash
minikube start
```

##### Ouvrir le dashboard Kubernetes dans un navigateur

```bash
minikube dashboard
```

##### Afficher la version de kubectl

```bash
$ kubectl version
Client Version: version.Info{Major:"1", Minor:"10", GitVersion:"v1.10.7", GitCommit:"0c38c362511b20a098d7cd855f1314dad92c2780", GitTreeState:"clean", BuildDate:"2018-08-20T10:09:03Z", GoVersion:"go1.9.3", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"13", GitVersion:"v1.13.3", GitCommit:"721bfa751924da8d1680787490c54b9179b1fed0", GitTreeState:"clean", BuildDate:"2019-02-01T20:00:57Z", GoVersion:"go1.11.5", Compiler:"gc", Platform:"linux/amd64"}
```

`kubectl` est un CLI permettant d'interagir avec le cluster Kubernetes via Kubernetes API.

##### Afficher la configuration de kubectl

```bash
kubectl config view
```

##### Afficher les informations du cluster Kubernetes

```bash
$ kubectl cluster-info
Kubernetes master is running at https://192.168.99.100:8443
KubeDNS is running at https://192.168.99.100:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
```



#### Déployer une application

##### Afficher les noeuds du cluster Kubernetes

```bash
$ kubectl get nodes
NAME       STATUS    ROLES     AGE       VERSION
minikube   Ready     master    5m        v1.13.3
```

Cette commande affiche tous les noeuds pouvant être utilisés pour héberger nos applications.
Le statut `Ready` indique que notre noeud est prêt à accepter des déploiements.

##### Créer un Deployment permettant d'exécuter son application dans un cluster Kubernetes

Afin de créer un Deployment, il faut spécifier l'image du container de notre application et le nombre de Replicas à exécuter.
Le nombre de Replicas peut être modifié par la suite.

Un Pod est un groupe de 1 ou plusieurs Containers, liés ensemble afin de faciliter leur administration et leur communication.
Un Kubernetes Deployment vérifie la santé du Pod et redémarre le container du Pod si il se termine. Les Deployments sont le moyens recommandés de gérer la création et la scablabilité des Pods.

```bash
kubectl create deploy <deployment-name> --image=<image-name>
kubectl create deploy hello-node --image=gcr.io/hello-minikube-zero-install/hello-node

# possibilité d'utiliser la commande `run` pour créer un déploiement
kubectl run kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1 --port=8080
```

##### Afficher les Déploiements

```bash
kubectl get deployments
```

##### Créer un serveur proxy entre localhost et le Kubernetes API Server

```bash
kubectl proxy
```


#### Explorer l'application

##### Afficher les Pods

```bash
kubectl get pods

# voir + d'informations, comme par exemple le nom du noeud dans lequel le Pod a été crée
kubectl get pods -o wide
```

##### Exporter le nom du Pod dans une variable d'environnement

```bash
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
```

##### Afficher les logs d'un Pod

```bash
kubectl logs <pod-name>
kubectl logs $POD_NAME
```

##### Afficher les informations d'une ressource

```bash
kubectl describe <resource-name>
kubectl describe all
kubectl describe -h
```

Par exemple pour voir, entre autres, les containers	qu'il y a dans un Pod:

```bash
kubectl describe pods
```

##### Executer une commande directement dans un container d'un Pod

```bash
kubectl exec <pod-name> [-c <container-name>] <command>

# afficher les variables d'environnement du container du Pod
kubectl exec $POD_NAME env

# lancer une session bash dans le container
kubectl exec -ti $POD_NAME bash

# vérifier qu'une app est bien exécutée dans le container
kubectl exec -ti $POD_NAME curl localhost:8080
```

Note: le nom du container peut être omis si il n'y a qu'un seul container dans le Pod

#### Exposer l'application

##### Créer un Service (exposer le container d'un Pod à l'extérieur du cluster via un Kubernetes Service)

Par défaut, un Pod n'est accessible que par son IP interne dans un cluster Kubernetes. Afin de rendre un container accessible à l'extérieur du réseau virtual Kubernetes, il faut exposer le Pod via un Kubernetes Service.

```bash
kubectl expose deployment <deployment-name> --type=LoadBalancer --port=8080 --name=<service-name>
kubectl expose deployment hello-node --type=LoadBalancer --port=8080 --name=hello-node-service

# Pour plus d'informations ou d'examples
kubectl expose --help
```

Le flag `--type=LoadBalancer` indique qu'on souhaite exposer le service à l'extérieur du cluster.
Le nom du service est optionnel. Il prendra le nom du déploiement par défaut si non renseigné.

- ClusterIP (default) - Exposes the Service on an internal IP in the cluster. This type makes the Service only reachable from within the cluster.
- NodePort - Exposes the Service on the same port of each selected Node in the cluster using NAT. Makes a Service accessible from outside the cluster using <NodeIP>:<NodePort>. Superset of ClusterIP.
- LoadBalancer - Creates an external load balancer in the current cloud (if supported) and assigns a fixed, external IP to the Service. Superset of NodePort.
- ExternalName - Exposes the Service using an arbitrary name (specified by externalName in the spec) by returning a CNAME record with the name. No proxy is used. This type requires v1.7 or higher of kube-dns.

##### Exporter port exposé par le Service dans une variable d'environnement

```bash
export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
echo NODE_PORT=$NODE_PORT
```

##### Ping une application exposée

```bash
curl $(minikube ip):$NODE_PORT
```

##### Afficher les Kubernetes Services

```bash
kubectl get services
```

##### Utiliser le Kubernetes Service via Minikube

```bash
minikube service <service-name>
minikube service hello-node-service
```

##### Afficher URL des services exposés par Minikube

```bash
minikube service list
```

##### Afficher les ressources (e.g. Pods/Services/Deployments) ayant un label particulier

```bash
# voir les labels des ressources
kubectl describe pods

# listing
kubectl get pods -l <label-name>
kubectl get pods -l run=kubernetes-bootcamp
```

##### Appliquer un label à une ressource

```bash
kubectl label <resource-type> <resource-name> <label>
kubectl label pod $POD_NAME app=v1
```

#### Scale l'application

##### Scaler up/down un Déploiement avec des Replicas

```bash
kubectl scale <deployment-name> --replicas=<count>
kubectl scale deployments/kubernetes-bootcamp --replicas=4
```

Ajouter des Replicas provoque la création automatique du même nombre de Pods.
En effectuant plusieurs ping successifs de l'application, on constate que désormais différents Pods répondent aux appels.
```bash
curl $(minikube ip):$NODE_PORT
```

#### Update l'application (Rolling Update)

##### Spécifier une nouvelle image au niveau du Deployment

```bash
kubectl set image <deployment-name> <container-name>=<image-name>
kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2
```

##### Vérifier le statut du rollout

```bash
# vérifier que l'app fonctionne toujours
curl $(minikube ip):$NODE_PORT


kubectl rollout status <deployment-name>
kubectl rollout status deployments/kubernetes-bootcamp
```

##### Rollback un déploimeent à la version précédente de l'image

```bash
# vérifier le statut du déploiement
kubectl get deployments

# vérifier le statut des pods
kubectl get pods

# voir les logs du pod contenant des erreurs
kubectl describe pods <pod-name>

# rollback
kubectl rollout undo <deployment-name>
kubectl rollout undo deployments/kubernetes-bootcamp
```

#### Gérer les secrets

```bash
# create
kubectl create secret generic mysql-pass --from-literal=password=YOUR_PASSWORD

# get
kubectl get secrets

# delete
kubectl delete secret <secret-name>
```

#### Nettoyer des ressources

##### Supprimer un Kubernetes Service

```bash
kubectl delete service <service-name>
kubectl delete service hello-node-service

# utilisation des labels
kubectl delete service -l <service-label>
kubectl delete service -l run=kubernetes-bootcamp
```

##### Supprimer un Deployment

```bash
kubectl delete deployment <deployment-name>
kubectl delete deployment hello-node
```

##### Stopper la VM Minikube

````bash
minikube stop
````

##### Supprimer la VM Minikube

````bash
minikube delete
````

#### Divers

##### Configurer le daemon Docker pour qu'il utiliser le host de Minikube

```bash
eval $(minikube docker-env)
```

##### Configurer le daemon Docker pour qu'il utilise le host par défaut

```bash
eval $(docker-machine env -u)
```

### Références
- [https://kubernetes.io/docs/tutorials/kubernetes-basics/](https://kubernetes.io/docs/tutorials/kubernetes-basics/){:target="_blank"}
- [https://kubernetes.io/docs/reference/kubectl/overview/](kubectl overview){:target="_blank"}
- [https://kubernetes.io/docs/tutorials/kubernetes-basics/expose/expose-intro/#services-and-labels](https://kubernetes.io/docs/tutorials/kubernetes-basics/expose/expose-intro/#services-and-labels)
---
layout: post
title: "Docker Cookbook - Containers"
date: "2016-11-26 23:15"
categories:
    - docker
tags:
    - devops
comments: true
---

### Comment ...

* [Docker](#docker)
	* [Afficher la version de Docker](#afficher-la-version-de-docker)
	* [Afficher l'aide Docker](#afficher-laide-docker)
* [Containers](#containers)
	* [Lancer un Hello World](#lancer-un-hello-world)
	* [Lancer un container interactif](#lancer-un-container-interactif)
	* [Lancer un container démon](#lancer-un-container-démon)
	* [Afficher les logs d'un container](#afficher-les-logs-dun-container)
	* [Stopper un container](#stopper-un-container)
	* [Relancer un container](#relancer-un-container)
	* [Tuer un container (envoi d'un signal "KILL" par défaut)](#tuer-un-container-envoi-dun-signal-quot-kill-quot-par-défaut)
	* [Supprimer un container](#supprimer-un-container)
	* [Lister les containers](#lister-les-containers)
	* [Mapper des ports de la machine hôte vers les ports exposés du container](#mapper-des-ports-de-la-machine-hôte-vers-les-ports-exposés-du-container)
	* [Afficher quel port de la machine hôte est mappé avec un port donné du container](#afficher-quel-port-de-la-machine-hôte-est-mappé-avec-un-port-donné-du-container)
	* [Afficher les processus en cours d'exécution dans un container](#afficher-les-processus-en-cours-dexécution-dans-un-container)
	* [Inspecter la configuration et statut d'un container (retour au format JSON)](#inspecter-la-configuration-et-statut-dun-container-retour-au-format-json)

### Docker

#### Afficher la version de Docker

```bash
docker version
```

#### Afficher l'aide Docker

```bash
# afficher la liste de toutes les commandes Docker disponibles
docker --help

# afficher l'aide d'utilisation d'une commande Docker spécifique
docker <command> --help
```

### Containers

#### Lancer un Hello World

```bash
docker run <image name> <command to run inside container>
docker run ubuntu /bin/echo 'Hello world'
```

#### Lancer un container interactif

```bash
docker run -t -i <image name> <command to run inside container>
docker run -t -i ubuntu /bin/bash
```

- `-t` : assigner un terminal au container
- `-i` : récupération de l'entrée standard du container

#### Lancer un container démon

```bash
docker run -d <image name> <command to run inside container>
docker run -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

- `-d` : lancer le container en tâche de fond

#### Afficher les logs d'un container

```bash
# affiche les derniers logs
docker logs <container name|id>

# affiche les logs en temps réel (e.g. tail -f)
docker logs -f <container name|id>
```

#### Stopper un container

```bash
docker stop <container name|id>
```

#### Relancer un container

```bash
docker start <container name|id>

# stop puis start un container
docker restart <container name|id>
```

#### Tuer un container (envoi d'un signal "KILL" par défaut)

```bash
docker kill <container name|id>
```

#### Supprimer un container

```bash
docker rm <container name|id>
```

*Remarque: le container doit être stoppé avant de pouvoir être supprimé*

#### Lister les containers

```bash
# liste les containers en cours d'exécution uniquement
docker ps

# liste tous les containers
docker ps -a
```

#### Mapper des ports de la machine hôte vers les ports exposés du container

```bash
docker run -d -P <image name> <command to run inside container>

# lancer en tâche de fond (-d) une webapp python tournant sur le port 5000 du container
docker run -d -P training/webapp python app.py

# idem mais en précisant un port explicite sur la machine hôte
docker run -d -p 49155:5000 training/webapp python app.py
```

- `-P` : mapping de ports aléatoires (entre 32768 to 61000) et éphémères de la machine hôte vers les ports exposés du container

```bash
docker ps

# mapping du port 49155 de la machine hôte vers le port 5000 du container
0.0.0.0:49155->5000/tcp
```

#### Afficher quel port de la machine hôte est mappé avec un port donné du container

```bash
docker port <container name|id> <container port>
docker port nostalgic_morse 5000

0.0.0.0:49155
```

#### Afficher les processus en cours d'exécution dans un container

```bash
docker top <container name|id>
```

#### Inspecter la configuration et statut d'un container (retour au format JSON)

```bash
docker inspect <container name|id>

# afficher un élément spécifique de la configuration via un format de template
docker inspect -f <template format> <container name|id>

# e.g. adresse ip du container
{% raw %}docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' nostalgic_morse{% endraw %}
```

### Références
- [https://docs.docker.com/engine/tutorials/dockerizing/](https://docs.docker.com/engine/tutorials/dockerizing/){:target="_blank"}
- [https://docs.docker.com/engine/tutorials/usingdocker/](https://docs.docker.com/engine/tutorials/usingdocker/){:target="_blank"}

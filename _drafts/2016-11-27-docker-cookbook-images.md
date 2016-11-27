---
layout: post
title: "Docker Cookbook - Images"
date: "2016-11-27 22:11"
categories:
    - docker
tags:
    - devops
comments: true
---

### Comment ...

#### Lister les images téléchargées sur la machine hôte

```bash
docker images
```

#### Télécharger une image

```bash
docker pull <image name>
docker pull <image name>:<image version>
```

Si la version de l'image est omise, c'est la version la plus récente qui est téléchargée.

#### Rechercher une image

```bash
docker search <image name>
docker search ubuntu
```

#### Créer une nouvelle image en apportant des changements à une image de base

```bash
# créer un container depuis l'image `training/sinatra` et s'y connecter
docker run -t -i training/sinatra /bin/bash

# installer des dépendances dans ce container
root@0b2616b0e5a8:/ apt-get install -y ruby2.0-dev ruby2.0
root@0b2616b0e5a8:/ exit

# commiter une copie de ce container vers une nouvelle image
docker commit -m <your commit message> -a <author name> <container id> <our_user>/<new image name>
docker commit -m "Added json gem" -a "Kate Smith" 0b2616b0e5a8 ouruser/sinatra:v2
```

L'inconvénient de cette méthode de création d'image est que le processus est lourd (installation et configuratoin manuelle des dépendances du container), et aussi il n'est pas possible de la partager avec d'autres personnes.
Une meilleur méthode est de créer un fichier Dockerfile.

#### Créer une nouvelle image à partir d'un fichier Dockerfile

Exemple de fichier Dockerfile

```
FROM ubuntu:14.04
MAINTAINER Kate Smith <ksmith@example.com>
RUN apt-get update && apt-get install -y ruby ruby-dev
RUN gem install sinatra
```

```bash
docker build -t <our_user>/<new image name> .
```

Nous indiquons le chemin où se trouve le fichier Dockerfile avec `.`


#### TODO
https://docs.docker.com/engine/tutorials/dockerimages/#/setting-tags-on-an-image

### Références
- [https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/){:target="_blank"}
- [https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/){:target="_blank"}
- [](){:target="_blank"}

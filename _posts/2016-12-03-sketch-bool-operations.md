---
layout: post
title: "Sketch Tips - Opérations booléennes"
date: "2016-12-03 14:18"
categories:
    - sketch
tags:
    - design
comments: true
---

Les opérations booléennes permettent de combiner des shapes basiques entre eux afin d'obtenir des formes complexes.<br>
C'est le layer sélectionné le plus bas (dans la pile des layers) qui sert de base aux opérations avec les layers supérieurs.

### Union

Obtention d'un layer étant la somme des layers sélectionnés.<br>
Remarque: Les layers supérieurs s'adaptent aux styles (couleur, largeur des bordures, etc.) du layer le plus bas dans la pile.

### Subtract
Obtention d'un layer étant la soustraction -au layer le plus bas dans la pile- de toutes les formes des layers supérieurs.<br>
e.g. Résultat = Layer inférieur - Layer supérieur 1 - Layer supérieur 2 - etc.

### Intersect
Otention d'un layer étant la zone commune de tous les layers sélectionnés.<br>
Remarque: La zone commune s'adapte aux styles du layer le plus bas dans la pile.

### Difference

Le résultat est l'union de tous les layers sélectionnés, mais sans les zones communes.<br>
Remarque: Encore une fois, les layers supérieurs s'adaptent aux styles (couleur, largeur des bordures, etc.) du layer le plus bas dans la pile.

### Modification des opérations

Après avoir appliqué une opération booléenne à plusieurs shapes, on obtient un "Combined Shape".
En dépliant le groupe, il est possiblé de modifier les opérations appliquées en cliquant sur l'icône se situant à droite du shape concerné.

<img src="{{ site.url }}/assets/images/2016-12-03-sketch-bool-operations/03_12_2016_15_34.png" alt="modify-boolean-operations">

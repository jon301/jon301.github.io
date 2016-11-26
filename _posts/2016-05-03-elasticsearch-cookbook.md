---
layout: post
title: "Elasticsearch Cookbook"
date: "2016-05-06 07:00"
categories:
    - elasticsearch
tags:
    - devops
comments: true
---

Synthèse très simplifiée de la liste des méthodes API d'Elasticsearch permettant d'effectuer
les actions les plus basiques.

### Comment ...

* [Cluster](#cluster)
	* [Vérifier le statut du cluster](#vrifier-le-statut-du-cluster)
	* [Lister les noeuds du cluster](#lister-les-noeuds-du-cluster)
* [Indexes](#indexes)
	* [Lister tous les indexes](#lister-tous-les-indexes)
	* [Créer un index](#crer-un-index)
	* [Supprimer un index](#supprimer-un-index)
* [Documents](#documents)
	* [Indexer un document](#indexer-un-document)
	* [Requêter un document](#requter-un-document)
	* [Modifier un document](#modifier-un-document)
	* [Supprimer un document](#supprimer-un-document)
* [Réaliser des opérations en lot](#raliser-des-oprations-en-lot)
* [Recherche](#recherche)
	* [Rechercher tous les documents d'un index](#rechercher-tous-les-documents-dun-index)
	* [Récupérer un nombre définis de résultats](#rcuprer-un-nombre-dfinis-de-rsultats)
	* [Trier les résultats](#trier-les-rsultats)
	* [Récupérer uniquement certains champs des documents](#rcuprer-uniquement-certains-champs-des-documents)
	* [Rechercher les documents contenant un ou plusieurs termes](#rechercher-les-documents-contenant-un-ou-plusieurs-termes)
	* [Rechercher les documents contenant une phrase donnée](#rechercher-les-documents-contenant-une-phrase-donne)
	* [Composer plusieurs critères de recherche](#composer-plusieurs-critres-de-recherche)

### Elasticsearch

L'API expose différentes méthodes permettant de :

- Vérifier l'état des clusters, des noeuds, la santé des indexes, leur statut,
    leurs statistiques
- Administrer vos clusters, noeuds, indexer vos données
- Effectuer des opérations CRUD et des recherches sur vos indexes de données
- Exécuter des opérations de recherches avancées avec support de pagination,
    tris, filtres, scripting, aggrégations, etc.

### Cluster

#### Vérifier le statut du cluster

```bash
curl '192.168.99.100:9200/_cat/health?v'
```

- `green` : tout est OK, le cluster fonctionne normalement
- `yellow` : les données sont accessibles mais certains replicas ne sont pas encore alloués
- `red` : certaines données ne sont pas accessibles pour une raison indéterminée

#### Lister les noeuds du cluster

```bash
curl '192.168.99.100:9200/_cat/nodes?v'
```

### Indexes

#### Lister tous les indexes

```bash
curl '192.168.99.100:9200/_cat/indices?v'
```

#### Créer un index

```bash
# création d'un index `customer`
curl -XPUT '192.168.99.100:9200/customer?pretty'

# informations sur l'index `customer`
curl '192.168.99.100:9200/customer?pretty'

# liste tous les indexes
curl '192.168.99.100:9200/_cat/indices?v'
```

On obtient 1 index `customer` avec 5 primary shards et 1 replica (valeur par
défaut) contenant 0 document.

On remarque également que l'index est en statut `yellow` car il n'y a qu'un seul
replica.
Quand un second noeud rejoindra le cluster et que le replica sera alloué sur ce
noeud, le statut de cet index passera en `green`.

#### Supprimer un index

```bash
# suppression de l'index `customer`
curl -XDELETE '192.168.99.100:9200/customer?pretty'

# liste tous les indexes
curl '192.168.99.100:9200/_cat/indices?v'
```

### Documents

#### Indexer un document

```bash
# indexation d'un document dans l'index `customer`, de type `external`, et d'ID 1
curl -XPUT '192.168.99.100:9200/customer/external/1?pretty' -d '
{
    "name": "John Doe"
}'
```

#### Requêter un document

```bash
# récupérer le document d'ID 1 dans l'index `customer`
curl -XGET '192.168.99.100:9200/customer/external/1?pretty'
```

#### Modifier un document

```bash
# indexation d'un document dans l'index `customer`, de type `external`, et d'ID 1
curl -XPUT '192.168.99.100:9200/customer/external/1?pretty' -d '
{
  "name": "John Doe"
}'

# modifier ce même document
curl -XPUT '192.168.99.100:9200/customer/external/1?pretty' -d '
{
  "name": "Jane Doe"
}'
```

Le paramètre ID est optionnel. Si il n'est pas spécifié, Elasticsearch génère un
ID aléatoire pour le document.

```bash
curl -XPOST '192.168.99.100:9200/customer/external?pretty' -d '
{
  "name": "Jane Doe"
}'
```

On utilise une requête de type `POST` car on n'a pas spécifié de paramètre ID.

#### Supprimer un document

```bash
# suppression du document d'ID 1 dans l'index `customer`
curl -XDELETE '192.168.99.100:9200/customer/external/1?pretty'
```


### Réaliser des opérations en lot

Elasticsearch propose une API pour réaliser des lots d'opérations de manière
très efficaces, avec un minimum de requêtes client-serveur.

Cette API exécute toutes les actions données dans un ordre séquentiel.
Si une des actions échoue, Elasticsearch passe à l'action suivante.
Un rapport de toutes les actions exécutées est retourné par l'API.


```bash
# index 2 documents (ID 1 - John Doe et ID 2 - Jane Doe) en 1 seule requête
curl -XPOST '192.168.99.100:9200/customer/external/_bulk?pretty' -d '
{ "index": { "_id": "1" } }
{ "name": "John Doe" }
{ "index": { "_id": "2" } }
{ "name": "Jane Doe" }
'

# modifie le 1er document (ID 1) et supprime ensuite le 2e (ID 2) en 1 seule requête
curl -XPOST '192.168.99.100:9200/customer/external/_bulk?pretty' -d '
{ "update": { "_id": "1" } }
{ "doc": { "name": "John Doe becomes Jane Doe" } }
{ "delete": { "_id": "2" } }
'
```

### Recherche

#### Rechercher tous les documents d'un index

L'API de recherche est accessible via le endpoint `_search` et accepte 2 types
de requêtes différentes

- requête GET avec paramètres de recherche passés via la querystring
- requete POST avec paramètres de recherche dans le body

```bash
#retourner tous les documents de l'index `customer`

# requête GET
curl '192.168.99.100:9200/customer/_search?q=*&pretty'

# requête POST
curl -XPOST '192.168.99.100:9200/customer/_search?pretty' -d '
{
  "query": { "match_all": {} }
}'
```

#### Récupérer un nombre définis de résultats

```bash
# récupérer le 1er document trouvé parmi tous ceux de l'index `customer`
curl -XPOST '192.168.99.100:9200/customer/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "size": 1
}'
```

Si le paramètre `size` n'est pas spécifié, retourne 10 résultats par défaut.

```bash
# récupérer les documents 11 à 20
curl -XPOST '192.168.99.100:9200/customer/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "from": 10,
  "size": 10
}'
```

Si `from` n'est pas spécifié, récupère les éléments à partir de l'indice 0.

#### Trier les résultats

Sorts the results by account balance in descending order and returns the top 10 (default size) documents

```bash
# indexer 3 documents dans l'index `customer`
curl -XPOST '192.168.99.100:9200/customer/external/_bulk?pretty' -d '
{ "index": { "_id": "1" } }
{ "name": "Jane Doe", "age": 42 }
{ "index": { "_id": "2" } }
{ "name": "John Doe", "age": 10 }
{ "index": { "_id": "3" } }
{ "name": "Junn Doe", "age": 84 }
{ "index": { "_id": "4" } }
{ "name": "Jin Doe", "age": 65 }
'

# retourner les documents de l'index `customer` triés par age dans l'ordre décroissant
# et ne récupérer que les 2 premiers résultats
curl -XPOST '192.168.99.100:9200/customer/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "sort": { "age": { "order": "desc" } },
  "size": 2
}'
```

#### Récupérer uniquement certains champs des documents

Par défaut, une recherche retourne le document JSON entièrement dans la propriété `_source`.
Il est cependant possible de ne demander à avoir qu'une partie du document.

```bash
curl -XPOST '192.168.99.100:9200/customer/external?pretty' -d '
{
  "firstname": "Jane",
  "lastname": "Doe",
  "address": "10 avenue des petits pois verts"
}'

# retourner uniquement le champ `firstname` des documents de l'index `customer`
curl -XPOST '192.168.99.100:9200/customer/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "_source": ["firstname"]
}'
```

#### Rechercher les documents contenant un ou plusieurs termes

On peut appliquer des critères de correspondance sur un ou plusieurs champs des
documents via les requêtes de type `match`.


```bash
# retourne tous les customers ayant le terme `Jane` dans le champ `firstname`
curl -XPOST '192.168.99.100:9200/customer/_search?pretty' -d '
{
  "query": { "match": { "firstname": "Jane" } }
}'

# retourne tous les customers ayant le terme `oranges` ou `verts` dans le champ `address`
curl -XPOST '192.168.99.100:9200/customer/_search?pretty' -d '
{
  "query": { "match": { "address": "oranges verts" } }
}'
```

Une alernative est de composer plusieurs critères de correspondance via une
requête de type `bool` et l'opérateur `should`.

```bash
# retourne tous les customers ayant le terme `oranges` ou `verts` dans le champ `address`
curl -XPOST '192.168.99.100:9200/customer/_search?pretty' -d '
{
  "query": {
    "bool": {
      "should": [
        { "match": { "address": "oranges" } },
        { "match": { "address": "verts" } }
      ]
    }
  }
}'
```

#### Rechercher les documents contenant une phrase donnée

Utiliser les requêtes de type `match_phrase`.

```bash
# retourne les customers ayant la phrase `petits pois verts` dans leur champ `address`
curl -XPOST '192.168.99.100:9200/customer/_search?pretty' -d '
{
  "query": { "match_phrase": { "address": "petits pois verts" } }
}'
```

#### Composer plusieurs critères de recherche

Utiliser les requêtes de type `bool`.

```bash
# recherche les customers ayant `Jane` dans le champ `firstname`
# et `avenue` ou `pois`` ou `verts` dans le champ `address`
curl -XPOST '192.168.99.100:9200/customer/_search?pretty' -d '
{
  "query": {
    "bool": {
      "must": [
        { "match": { "firstname": "Jane" } },
        { "match": { "address": "avenue pois verts" } }
      ]
    }
  }
}'

# recherche les customers ayant ayant `jane dans le champ `firstname`
# mais pas `petits pois verts` dans le champ `address`
curl -XPOST '192.168.99.100:9200/customer/_search?pretty' -d '
{
  "query": {
    "bool": {
      "must": [
        { "match": { "firstname": "Jane" } }
      ],
      "must_not": [
        { "match_phrase": { "address": "petits pois verts" } }
      ]
    }
  }
}'
```

Tous les critères de la requête de type `bool` doivent être remplis par le
document pour que ce dernier soit retourné.

### Références
- [https://www.elastic.co/guide/en/elasticsearch/reference/master/index.html](https://www.elastic.co/guide/en/elasticsearch/reference/master/index.html){:target="_blank"}
- [https://www.elastic.co/guide/en/elasticsearch/reference/master/cat.html](https://www.elastic.co/guide/en/elasticsearch/reference/master/cat.html){:target="_blank"}
- [https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html#_version_types){:target="_blank"}
- [https://www.elastic.co/guide/en/elasticsearch/reference/master/docs-bulk.html](https://www.elastic.co/guide/en/elasticsearch/reference/master/docs-bulk.html){:target="_blank"}
- [https://www.elastic.co/guide/en/elasticsearch/reference/master/search-uri-request.html](https://www.elastic.co/guide/en/elasticsearch/reference/master/search-uri-request.html){:target="_blank"}
- [https://www.elastic.co/guide/en/elasticsearch/reference/master/search-request-body.html](https://www.elastic.co/guide/en/elasticsearch/reference/master/search-request-body.html){:target="_blank"}
- [https://www.elastic.co/guide/en/elasticsearch/reference/master/query-dsl-bool-query.html](https://www.elastic.co/guide/en/elasticsearch/reference/master/query-dsl-bool-query.html){:target="_blank"}

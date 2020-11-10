# HowTo de l'API WFS (Web Feature Service) MSC GeoMet Web 3.0

## OGC Web Feature Service 3.0

## Présentation de l'API MSC GeoMet WFS 3.0

[L'API OGC WFS 3.0](https://rawgit.com/opengeospatial/WFS_FES/master/docs/17-069.html) fournit une liste de spécifications standardisées permettant d'interroger des données géospatiales sur le Web.

Le service fonctionne via HTTP.

Les demandes sont effectuées via des requêtes HTTP GET.

Les réponses sont au format JSON/[GeoJSON](http://geojson.org/) par défaut.

Aucune authentification HTTP n'est requise.

## Points de service

### WFS 3

https://geo.weather.gc.ca/geomet/features

### Documentation OpenAPI 3.0

https://geo.weather.gc.ca/geomet/features/api

## Collections d'entités

Répertoriez toutes les collections d’entités disponibles:

https://geo.weather.gc.ca/geomet/features/collections

La réponse fournit une liste de collections de fonctionnalités avec leurs métadonnées associées (titre, description, liens, étendue, CRS).

## Collection de d'entités

Répertoriez une seule collection d'entités:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-stations

## Inspection du schéma de collection d'entités

Émettez une requête renvoyant une seule entité pour inspecter sa géométrie et ses propriétés:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?limit=1

## Requête

Les requêtes sur les collections d'entités permettent un filtrage spatial, temporel et sur des propriétés. Les paramètres de filtre peuvent être combinés pour formuler une recherche exclusive ('et').

Les exemples suivants utilisent la collection [hydrométrique de moyennes quotidiennes](https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean).

Requête par défaut, pas de filtres:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items

### Spatial

Requête par zone de sélection (minx, miny, maxx, maxy)

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?bbox=-140,43.2,-65,67

### Temporel

Requête pour une valeur instantannée:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?time=1972-10-30

Requête pour un intervalle de temps:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?time=1972-10-30/2010-07-31

### Propriété

Requête par une propriété de collection d'entités:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=10CD001

### Pagination

#### Startindex
Le paramètre `startindex` peut être utilisé pour spécifier l’enregistrement à partir duquel extraire les entités. La valeur par défaut est 0 (premièreentité).

#### Limite
Le paramètre `limit` permet de définir le nombre maximal d'enregistrements à renvoyer. La taille de réponse par défaut est de 500 entitée. Si vous définissez une `limite` de 0, seul le nombre d'entités trouvées sera retourné (sans les entités réelles retournées).

#### Parcourir les résultats

Les paramètres `startindex` et `limit` peuvent être utilisés en tandem pour parcourir les collections d’entités. Les exemples ci-dessous montrent comment ajuster et parcourir les résultats d'une requête.

Requête et limite aux entités 1-2:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=10CD001&limit=2

Requête et limite aux entités 1 à 100:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=10CD001&limit=100

Requête et limite aux entités 101-200:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=10CD001&startindex=101&limit=100

#### Stratégies de pagination

La stratégie de pagination est couramment utilisée pour améliorer les performances lors du retour d'extractions de données volumineuses. Par exemple, un client peut faire défiler par 1 000 entités pour parcourir un enregistrement de station complet:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=01AP004&startindex=0&limit=1000

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=01AP004&startindex=1000&limit=1000

Le client peut alors simplement parcourir tous les éléments jusqu'à ce qu'il n'y ait plus d'enregistrements. Cela constituerait le dossier complet.

Une autre stratégie possible consiste à interroger toutes les données sans renvoyer aucun enregistrement:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=01AP004&limit=0

... puis en examinant la réponse (voir `numéro Correspondance`) pour évaluer la taille de l'enregistrement complet. Le client peut alors décider comment ou s'il doit paginer en conséquence.
 
### Combinaison de paramètres de filtre

Interrogez toutes les moyennes quotidiennes d'une station entre 2001 et 2010:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=10CD001&time=2001-01-01/2010-12-31

### Exporter au format CSV

Toute requête peut être exportée au format CSV en ajoutant `f=csv` à la demande.

### Tri
Toute requête peut être triée en ajoutant sortby = PROPERTY: X, où PROPERTY est la propriété de tri et X, l'ordre de tri (A est ascendant, D est descendant). L'ordre de tri est facultatif. Le tri en fonction de plusieurs propriétés est pris en charge en fournissant au paramètre sortby une liste séparée par des virgules.

## Accès par identifiant

Récupérer une seule caractéristique par identifiant:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items/08JB003.1950-08-02?f=json


## Outils et implémentations

### GDAL

Utilisation du support WFS 3 de [GDAL](http://www.gdal.org/drv_wfs3.html):

```bash
# list datasets
ogrinfo WFS3: https://geo.weather.gc.ca/geomet/features
# décrire un seul jeu de données
ogrinfo WFS3: https://geo.weather.gc.ca/geomet/features hydrometric-daily-mean -al -so
# effectuer une requête spatiale
ogrinfo WFS3: https://geo.weather.gc.ca/geomet/features hydrometric-daily-mean -spat -100 50 -90 55
```

### geojson.io

En utilisant [geojson.io](https://geojson.io), copiez / collez le résultat de la requête pour visualiser et pointer / cliquer sur la requête.


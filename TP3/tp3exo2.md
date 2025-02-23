# Guide CouchDB

## Présentation de CouchDB

Apache CouchDB est une base de données NoSQL orientée document qui utilise le format JSON pour stocker les données, JavaScript pour les requêtes MapReduce, et HTTP pour l'API REST. CouchDB est conçu pour la scalabilité, la réplication et la tolérance aux pannes.

### Caractéristiques principales

- Stockage de documents JSON
- Accès via une API RESTful
- Réplication multi-maîtres
- Indexation et requêtes MapReduce
- Conflits gérés nativement

## Installation de CouchDB avec Docker

Pour installer CouchDB à l'aide de Docker, suivez ces étapes :

### 1. Télécharger et exécuter l'image CouchDB

```sh
docker run -d --name couchdb -p 5984:5984 -e COUCHDB_USER=admin -e COUCHDB_PASSWORD=password couchdb
```

### 2. Vérifier l'installation

Ouvrez un navigateur et accédez à http\://localhost:5984/\_utils pour accéder à Fauxton, l'interface web de CouchDB.

Vous pouvez également tester l'installation avec `curl` :

```sh
curl http://admin:password@localhost:5984/
```

## Utilisation de CouchDB avec `curl`

### 1. Créer une base de données

```sh
curl -X PUT http://admin:password@localhost:5984/movies
```

### 2. Ajouter un document

```sh
curl -X POST http://admin:password@localhost:5984/movies \
     -H "Content-Type: application/json" \
     -d '{"_id": "movie:1", "title": "Inception", "year": 2010, "genre": "Sci-Fi"}'
```

### 3. Récupérer un document

```sh
curl -X GET http://admin:password@localhost:5984/movies/movie:1
```

### 4. Mettre à jour un document

Récupérez d'abord le document pour obtenir la valeur `_rev` (révision) :

```sh
curl -X GET http://admin:password@localhost:5984/movies/movie:1
```

Puis mettez-le à jour en incluant `_rev` :

```sh
curl -X PUT http://admin:password@localhost:5984/movies/movie:1 \
     -H "Content-Type: application/json" \
     -d '{"_id": "movie:1", "_rev": "1-xxx", "title": "Inception", "year": 2010, "genre": "Science Fiction"}'
```

## Importer une base de données depuis un fichier JSON

### 1. Préparer le fichier JSON

Assurez-vous que votre fichier JSON contient un tableau de documents. Le fichier `films.json` a été fourni et contient une liste de films au format JSON.

### 2. Importer les données

Utilisez `curl` pour importer les documents :

```sh
cat films.json | jq -c '.[]' | while read doc; do \
    curl -X POST http://admin:password@localhost:5984/movies \
         -H "Content-Type: application/json" \
         -d "$doc"; \
done
```

Cela va insérer chaque document du fichier `films.json` dans la base `movies`.

## MapReduce avec CouchDB

### Présentation de MapReduce dans CouchDB

MapReduce est une approche de traitement des données utilisée dans CouchDB pour créer des vues indexées. Une fonction `map` est utilisée pour émettre des paires clé-valeur, et une fonction `reduce` permet d'agréger ces valeurs.

### Exemple 1 : Nombre de films par année

#### Fonction Map

Cette fonction `map` émet chaque film avec son année de sortie :

```javascript
function (doc) {
    emit(doc.year, doc.title);
}
```

#### Fonction Reduce

Cette fonction `reduce` compte le nombre de films par année :

```javascript
function (keys, values) {
    return values.length;
}
```

#### Requête MapReduce

Vous pouvez exécuter cette vue et récupérer le nombre de films par année en interrogeant la base avec :

```sh
curl -X GET "http://admin:password@localhost:5984/movies/_design/movies/_view/movies_by_year?group=true"
```

### Exemple 2 : Nombre de films réalisés par acteur

#### Fonction Map

Cette fonction `map` émet les acteurs avec le titre des films dans lesquels ils ont joué :

```javascript
function(doc) {
    for (i = 0; i < doc.actors.length; i++) {
        emit({"prénom": doc.actors[i].first_name, "nom": doc.actors[i].last_name}, doc.title);
    }
}
```

#### Fonction Reduce

Cette fonction `reduce` compte le nombre de films réalisés par acteur :

```javascript
function (keys, values) {
    return values.length;
}
```

#### Requête MapReduce

Vous pouvez exécuter cette vue et récupérer le nombre de films réalisés par acteur en interrogeant la base avec :

```sh
curl -X GET "http://admin:password@localhost:5984/movies/_design/movies/_view/movies_by_actor?group=true"
```

## Conclusion

CouchDB est une base de données NoSQL puissante et flexible qui permet une gestion des documents simple et efficace. Grâce à Docker et `curl`, il est facile de configurer, interagir avec, et importer des bases de données dans CouchDB. L'utilisation de MapReduce permet de créer des vues indexées pour exploiter efficacement les données stockées.

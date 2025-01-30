# 📌 Introduction

Ce document présente un TP pratique sur MongoDB, une base de données NoSQL orientée documents. L'objectif est de se familiariser avec les requêtes MongoDB et de manipuler des données stockées sous format JSON.

## 📌 Objectif du TP

L'objectif de ce TP est d'apprendre à interagir avec une base de données MongoDB en utilisant des requêtes basiques et avancées, telles que l'insertion, la récupération, le filtrage et l'agrégation des données.

## 📌 Présentation de MongoDB

MongoDB est une base de données NoSQL qui stocke les données sous forme de documents JSON/BSON. Contrairement aux bases relationnelles traditionnelles, MongoDB est plus flexible et permet le stockage de données semi-structurées. Ses principales caractéristiques incluent :

- Stockage orienté documents
- Évolutivité horizontale
- Requêtes riches en filtrage et agrégation
- Indexation avancée

## 📌 Installation de MongoDB avec Docker

L'installation de MongoDB peut être simplifiée grâce à Docker. Voici les étapes :

### 🔹 Étape 1 : Installer Docker

Avant d'installer MongoDB, assurez-vous que Docker est installé sur votre machine. Vous pouvez le télécharger depuis [Docker](https://www.docker.com/).

### 🔹 Étape 2 : Lancer un conteneur MongoDB

Exécutez la commande suivante pour télécharger et exécuter un conteneur MongoDB :

```sh
docker run -d --name mongodb -p 27017:27017 mongo:latest
```

Cela va :

- Télécharger l'image MongoDB si elle n'est pas encore présente
- Démarrer un conteneur MongoDB
- Mapper le port `27017` (port par défaut de MongoDB)

### 🔹 Étape 3 : Accéder à MongoDB

Utilisez la commande suivante pour entrer dans le shell MongoDB à l'intérieur du conteneur :

```sh
docker exec -it mongodb mongosh
```

Vous pouvez maintenant exécuter vos requêtes MongoDB.

---

## 📌 Comparaison entre MongoDB et Redis

MongoDB et Redis sont deux bases de données NoSQL populaires mais avec des différences notables. Voici un tableau comparatif :

| Caractéristique       | MongoDB                                                                                    | Redis                                                                             |
| --------------------- | ------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------- |
| **Type**              | Base de données orientée documents                                                         | Base de données clé-valeur en mémoire                                             |
| **Stockage**          | Stocke des documents JSON/BSON sur disque                                                  | Stocke les données en mémoire (avec persistance optionnelle)                      |
| **Performance**       | Bonne pour les requêtes complexes et le stockage de grandes quantités de données           | Extrêmement rapide pour les lectures/écritures grâce au stockage en mémoire       |
| **Modèle de données** | Documents semi-structurés (JSON)                                                           | Clé-valeur avec structures de données avancées (listes, ensembles, etc.)          |
| **Cas d'utilisation** | Applications nécessitant des requêtes complexes, de l'agrégation et du stockage persistant | Caching, sessions utilisateurs, files d'attente, leaderboards                     |
| **Persistance**       | Stocke les données de manière permanente                                                   | Par défaut, fonctionne en mémoire mais peut être configuré pour la persistance    |
| **Scalabilité**       | Évolutivité horizontale avec sharding                                                      | Évolutivité horizontale avec clustering et réplication                            |
| **Transactions**      | Supporte les transactions ACID (depuis la version 4.0)                                     | Transactions limitées (Multi-Exec pour exécuter plusieurs commandes atomiquement) |
| **Requêtes avancées** | Supporte les requêtes complexes avec indexation et agrégation                              | Supporte des opérations simples basées sur clé-valeur                             |

En résumé, **MongoDB** est plus adapté pour le stockage de données complexes et structurées, tandis que **Redis** est optimisé pour la rapidité et les cas d'utilisation nécessitant une faible latence.

---

# 📌 Explication des requêtes MongoDB exécutées

## 📌 1. Vérifier le nombre total de documents dans la collection `films`

```js
db.films.countDocuments();
```

**Résultat :** `278`

> Cette requête compte le nombre total de films présents dans la collection `films`.

---

## 📌 2. Afficher un film au hasard pour voir la structure des documents

```js
db.films.findOne();
```

**Résultat :** Un document représentant un film, par exemple "Eternal Sunshine of the Spotless Mind".

> Cette requête récupère **un seul document** dans la collection `films` pour comprendre sa structure.

---

## 📌 3. Trouver tous les films d'action

```js
db.films.find({ genre: "Action" });
```

**Résultat :** Une liste de films d’action comme "Kill Bill: Volume 1", "Gladiator", "Minority Report"...

> Cette requête renvoie **tous les films dont le genre est "Action"**.

---

## 📌 4. Compter le nombre de films d’action

```js
db.films.countDocuments({ genre: "Action" });
```

**Résultat :** `36`

> Cette requête compte le nombre de films qui appartiennent au genre "Action".

---

## 📌 Conclusion

Ce fichier explique **chaque requête MongoDB exécutée**, les résultats obtenus et leur interprétation.
Si des films ou acteurs spécifiques ne sont pas trouvés, cela peut être dû à **l’absence de ces données** ou **une différence de format** dans les enregistrements.

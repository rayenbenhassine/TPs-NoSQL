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

## 📌 **1. Vérifier le nombre de documents dans la collection**

```js
db.films.countDocuments();
```

- Compte le nombre total de documents dans la collection `films`.

---

## 📌 **2. Afficher un document pour comprendre la structure**

```js
db.films.findOne();
```

- Affiche un seul document de la collection `films` pour examiner sa structure.

---

## 📌 **3. Liste des films d’action**

```js
db.films.find({ genre: "Action" });
```

- Récupère tous les films dont le `genre` est "Action".

---

## 📌 **4. Nombre de films d’action**

```js
db.films.countDocuments({ genre: "Action" });
```

- Compte le nombre de films appartenant au genre "Action".

---

## 📌 **5. Films d’action produits en France**

```js
db.films.find({ genre: "Action", pays: "France" });
```

- Sélectionne uniquement les films d’action **produits en France**.

---

## 📌 **6. Films d’action produits en France en 1963**

```js
db.films.find({ genre: "Action", pays: "France", annee: 1963 });
```

- Affiche les films d’action produits en France **en 1963**.

---

## 📌 **7. Afficher les films d’action réalisés en France sans les notes (`grades`)**

```js
db.films.find({ genre: "Action", pays: "France" }, { grades: 0 });
```

- Exclut l’attribut `grades` dans les résultats.

---

## 📌 **8. Afficher uniquement les titres et notes des films d’action en France**

```js
db.films.find(
  { genre: "Action", pays: "France" },
  { _id: 0, titre: 1, grades: 1 }
);
```

- Affiche uniquement les **titres** et les **notes** (`grades`) des films d’action français, sans afficher `_id`.

---

## 📌 **9. Films d’action en France avec une note supérieure à 10**

```js
db.films.find(
  { genre: "Action", pays: "France", notes: { $gt: 10 } },
  { _id: 0, titre: 1, notes: 1 }
);
```

- Sélectionne les films d’action français où **au moins une note est > 10**.

---

## 📌 **10. Films d’action en France avec uniquement des notes supérieures à 10**

```js
db.films.find(
  { genre: "Action", pays: "France", notes: { $elemMatch: { $gt: 10 } } },
  { _id: 0, titre: 1, notes: 1 }
);
```

- Utilise `$elemMatch` pour s'assurer que **toutes** les notes sont > 10.

---

## 📌 **11. Afficher tous les genres de films disponibles**

```js
db.films.distinct("genre");
```

- Affiche tous les **genres distincts** présents dans la base `lesfilms`.

---

## 📌 **12. Afficher toutes les notes (`grades`) attribuées**

```js
db.films.distinct("grades");
```

- Récupère toutes les **notes distinctes** existantes.

---

## 📌 **13. Films où un des artistes suivants apparaît**

```js
db.films.find({ artistes: { $in: ["artist:4", "artist:18", "artist:11"] } });
```

- Sélectionne les films contenant **au moins un des artistes spécifiés**.

---

## 📌 **14. Films sans résumé (`summary`)**

```js
db.films.find({ $or: [{ summary: { $exists: false } }, { summary: "" }] });
```

- Recherche les films **sans résumé** (champ `summary` inexistant ou vide).

---

## 📌 **15. Films avec Leonardo DiCaprio en 1997**

```js
db.films.find({ artistes: "Leonardo DiCaprio", annee: 1997 });
```

- Sélectionne les films **avec Leonardo DiCaprio** sortis **en 1997**.

---

## 📌 **16. Films avec Leonardo DiCaprio OU sortis en 1997**

```js
db.films.find({ $or: [{ artistes: "Leonardo DiCaprio" }, { annee: 1997 }] });
```

- Recherche les films **joués par Leonardo DiCaprio OU sortis en 1997**.

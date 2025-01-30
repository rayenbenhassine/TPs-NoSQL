# Redis : Guide d'utilisation et exemples

Ce document fournit une introduction à Redis et explique ses principales fonctionnalités avec des exemples concrets.

## Qu'est-ce que Redis ?

Redis (REmote DIctionary Server) est une base de données NoSQL de type clé-valeur, réputée pour sa rapidité. Il stocke les données en mémoire, mais offre des mécanismes de persistance sur disque pour éviter les pertes de données.
Redis est classé comme une **base de donnée clé-valeur** (key-value store). Il est parfois aussi considéré comme un **cache en mémoire**.


## Intérêt de Redis

Redis se distingue des autres bases de données NoSQL par plusieurs aspects, ce qui le rend particulièrement adapté à certains cas d'utilisation.

**Avantages de Redis :**

*   **Rapidité extrême :** Grâce à son stockage en mémoire, Redis offre des performances exceptionnelles en lecture et en écriture. Les opérations sont généralement exécutées en quelques microsecondes.
*   **Structures de données riches :** Redis ne se limite pas à un simple stockage clé-valeur. Il propose des structures de données avancées comme les listes, les ensembles, les ensembles ordonnés et les hashs, permettant de modéliser des données complexes de manière efficace.
*   **Support de la persistance :** Bien que les données soient stockées en mémoire, Redis offre des mécanismes de persistance (RDB et AOF) pour garantir la durabilité des données en cas de redémarrage du serveur.
*   **Fonctionnalités avancées :** Redis propose des fonctionnalités telles que Pub/Sub (publication/abonnement), les transactions, le scripting Lua et l'éviction de clés (pour gérer la mémoire).
*   **Simplicité d'utilisation :** L'API de Redis est simple et intuitive, ce qui facilite son intégration dans les applications.



## Comparaison avec d'autres bases de données NoSQL

Voici une comparaison succincte avec d'autres bases de données NoSQL populaires :

*   **Redis vs. MongoDB :** MongoDB est une base de données orientée documents, stockant les données au format BSON (une forme binaire de JSON). MongoDB est conçu pour le stockage de gros volumes de données et offre des fonctionnalités de requêtes complexes. Redis est plus performant pour les opérations simples et rapides en mémoire, mais moins adapté au stockage de données massives et aux requêtes complexes. On utilise MongoDB pour des applications nécessitant un schéma flexible et des requêtes complexes sur de gros volumes de données, et Redis pour le caching, les sessions, les compteurs, les files d'attente, etc.

*   **Redis vs. Cassandra :** Cassandra est une base de données NoSQL distribuée, conçue pour la haute disponibilité et la tolérance aux pannes à grande échelle. Elle est optimisée pour les opérations d'écriture et la distribution des données sur plusieurs nœuds. Redis est plus performant pour les opérations de lecture et d'écriture en mémoire sur un seul nœud (bien qu'il existe des solutions de clustering Redis). On utilise Cassandra pour des applications nécessitant une très haute disponibilité et une scalabilité horizontale massive, et Redis pour des performances élevées en mémoire sur un seul serveur ou un cluster plus restreint.

*   **Redis vs. Couchbase:** Couchbase est une base de données NoSQL qui combine les avantages d'un stockage clé-valeur et d'un stockage orienté document. Elle offre des fonctionnalités de cache, de stockage persistant, et de requêtes complexes avec N1QL (un langage de requête similaire à SQL). Redis est plus simple et plus rapide pour les opérations en mémoire, tandis que Couchbase est plus adapté aux applications qui ont besoin d'une plus grande flexibilité de schéma et de capacités de requête plus riches, tout en offrant une bonne performance.
**Tableau comparatif simplifié :**

| Caractéristique       | Redis          | MongoDB      | Cassandra    | Couchbase    |
| --------------------- | -------------- | ------------ | ------------ | ------------ |
| Type                  | Clé-valeur / Data Structure Server | Orientée documents | Distribuée large échelle | Clé-valeur / Document |
| Stockage              | Mémoire (avec persistance) | Disque (avec mémoire) | Disque (distribué) | Mémoire et Disque |
| Performance           | Très rapide    | Rapide       | Haute disponibilité, écriture rapide | Bon compromis |
| Structures de données | Riches         | Documents JSON | Tables         | Clé-valeur et documents JSON |
| Persistance           | Oui            | Oui          | Oui          | Oui |
| Scalabilité           | Verticale (et clustering) | Horizontale    | Horizontale massive | Horizontale |
| Cas d'utilisation    | Cache, sessions, files d'attente, compteurs | Applications web, Big Data | Applications critiques, IoT | Applications web, mobiles, IoT |

En résumé, le choix de la base de données NoSQL dépend des besoins spécifiques de l'application. Redis est un excellent choix pour les applications nécessitant des performances élevées en mémoire, des structures de données riches et des fonctionnalités avancées, mais moins adapté au stockage de données massives et aux requêtes complexes nécessitant un langage de requête puissant comme SQL.


## Lancer un serveur et un client Redis

Redis est composé d'un serveur et d'un client (redis-cli). Voici comment les lancer :

**1. Lancer le serveur Redis :**

*   **Si installé via un gestionnaire de paquets (recommandé) :**

    La plupart des distributions Linux (Debian, Ubuntu, CentOS, etc.) proposent un paquet Redis. L'installation et le lancement se font généralement avec les commandes suivantes (exemple pour Ubuntu/Debian) :

    ```bash
    sudo apt update
    sudo apt install redis-server
    sudo systemctl start redis-server  # Démarre le service
    sudo systemctl enable redis-server # Pour que Redis démarre au démarrage du système
    ```

    Le serveur Redis écoute par défaut sur le port 6379.

*   **Si compilé depuis les sources :**

    Après avoir téléchargé et compilé les sources de Redis, vous pouvez lancer le serveur avec la commande suivante, depuis le répertoire `src` de Redis :

    ```bash
    ./redis-server
    ```

    Vous pouvez spécifier un fichier de configuration :

    ```bash
    ./redis-server /chemin/vers/redis.conf
    ```

    Le fichier de configuration par défaut est généralement `redis.conf` et se trouve dans le répertoire racine de Redis.

**2. Lancer le client Redis (redis-cli) :**

Une fois le serveur lancé, vous pouvez utiliser le client `redis-cli` pour interagir avec Redis.

*   **Connexion au serveur local (par défaut) :**

    ```bash
    redis-cli
    ```

    Cela connectera le client au serveur Redis local sur le port 6379.

*   **Connexion à un serveur distant ou sur un port différent :**

    ```bash
    redis-cli -h <adresse_ip_du_serveur> -p <port>
    ```

    Par exemple, pour se connecter au serveur `192.168.1.10` sur le port `6380` :

    ```bash
    redis-cli -h 192.168.1.10 -p 6380
    ```

*   **Connexion avec un mot de passe (si configuré) :**

    Si vous avez configuré un mot de passe dans le fichier `redis.conf` avec la directive `requirepass`, vous devez l'utiliser lors de la connexion :

    ```bash
    redis-cli -a <mot_de_passe>
    ```

    Ou, une fois connecté, vous pouvez utiliser la commande `AUTH` :

    ```
    redis-cli
    AUTH <mot_de_passe>
    ```



## Définition des clés/valeurs

Dans Redis, les données sont stockées sous forme de paires clé-valeur. Les clés sont des chaînes de caractères et les valeurs peuvent être de différents types (chaînes, listes, ensembles, ensembles ordonnés, hashs, etc.).

**Exemple :**

<pre><code>SET nom "Jean"</code></pre>
*   `SET nom "Jean"`: Définit la clé `nom` avec la valeur `"Jean"`.

<pre><code>SET age 30</code></pre>
*   `SET age 30`: Définit la clé `age` avec la valeur `30`.

<pre><code>GET nom</code></pre>
*   `GET nom`: Récupère la valeur associée à la clé `nom`.

<pre><code>GET age</code></pre>
*   `GET age`: Récupère la valeur associée à la clé `age`.

## Suppression de clé

La commande `DEL` permet de supprimer une clé et sa valeur associée. Elle retourne le nombre de clés supprimées.

**Exemple :**

<pre><code>DEL nom</code></pre>
*   `DEL nom`: Supprime la clé `nom`. Retourne 1 si la clé existait, 0 sinon.

<pre><code>DEL nom age</code></pre>
*   `DEL nom age`: Supprime les clés `nom` et `age`. Retourne le nombre de clés effectivement supprimées.

## Durée de vie d'une clé (Expiration)

La commande `EXPIRE` définit une durée de vie (en secondes) pour une clé. `PEXPIRE` permet de définir une durée en millisecondes.

**Exemple :**

<pre><code>SET message "Bonjour"</code></pre>
*   `SET message "Bonjour"`: Définit la clé `message` avec la valeur `"Bonjour"`.

<pre><code>EXPIRE message 10</code></pre>
*   `EXPIRE message 10`: Définit l'expiration de la clé `message` à 10 secondes.

<pre><code>PEXPIRE message 5000</code></pre>
*   `PEXPIRE message 5000`: Définit l'expiration de la clé `message` à 5000 millisecondes (5 secondes).

<pre><code>TTL message</code></pre>
*   `TTL message`: Retourne le temps restant avant l'expiration de la clé `message` en secondes. Retourne -1 si la clé n'a pas d'expiration, -2 si la clé n'existe pas.

<pre><code>PTTL message</code></pre>
*   `PTTL message`: Retourne le temps restant avant l'expiration de la clé `message` en millisecondes. Retourne -1 si la clé n'a pas d'expiration, -2 si la clé n'existe pas.

<pre><code>PERSIST message</code></pre>
*   `PERSIST message`: Supprime l'expiration de la clé `message`.

## Les listes

Les listes dans Redis sont des séquences ordonnées d'éléments.

**Exemple :**

<pre><code>RPUSH fruits "pomme" "banane" "orange"</code></pre>
*   `RPUSH fruits "pomme" "banane" "orange"`: Ajoute les éléments "pomme", "banane" et "orange" à la fin de la liste `fruits`.

<pre><code>LPUSH fruits "fraise"</code></pre>
*   `LPUSH fruits "fraise"`: Ajoute l'élément "fraise" au début de la liste `fruits`.

<pre><code>LRANGE fruits 0 -1</code></pre>
*   `LRANGE fruits 0 -1`: Récupère tous les éléments de la liste `fruits` (de l'index 0 jusqu'au dernier index -1).

<pre><code>RPOP fruits</code></pre>
*   `RPOP fruits`: Supprime et retourne le dernier élément de la liste `fruits`.

<pre><code>LPOP fruits</code></pre>
*   `LPOP fruits`: Supprime et retourne le premier élément de la liste `fruits`.

<pre><code>LLEN fruits</code></pre>
*   `LLEN fruits`: Retourne la longueur de la liste `fruits`.

## Les ensembles (Sets)

Les ensembles sont des collections non ordonnées d'éléments uniques.

**Exemple :**

<pre><code>SADD couleurs "rouge" "vert" "bleu"</code></pre>
*   `SADD couleurs "rouge" "vert" "bleu"`: Ajoute les éléments "rouge", "vert" et "bleu" à l'ensemble `couleurs`.

<pre><code>SMEMBERS couleurs</code></pre>
*   `SMEMBERS couleurs`: Retourne tous les éléments de l'ensemble `couleurs`.

<pre><code>SREM couleurs "vert"</code></pre>
*   `SREM couleurs "vert"`: Supprime l'élément "vert" de l'ensemble `couleurs`.

<pre><code>SISMEMBER couleurs "vert"</code></pre>
*   `SISMEMBER couleurs "vert"`: Vérifie si l'élément "vert" est membre de l'ensemble `couleurs`. Retourne 1 si c'est le cas, 0 sinon.

<pre><code>SCARD couleurs</code></pre>
*   `SCARD couleurs`: Retourne la cardinalité (le nombre d'éléments) de l'ensemble `couleurs`.

<pre><code>SADD autres_couleurs "jaune" "vert"</code></pre>
*   `SADD autres_couleurs "jaune" "vert"`: Ajoute les éléments "jaune" et "vert" à l'ensemble `autres_couleurs`.

<pre><code>SUNION couleurs autres_couleurs</code></pre>
*   `SUNION couleurs autres_couleurs`: Retourne l'union des ensembles `couleurs` et `autres_couleurs`.

<pre><code>SINTER couleurs autres_couleurs</code></pre>
*   `SINTER couleurs autres_couleurs`: Retourne l'intersection des ensembles `couleurs` et `autres_couleurs`.

<pre><code>SDIFF couleurs autres_couleurs</code></pre>
*   `SDIFF couleurs autres_couleurs`: Retourne la différence entre l'ensemble `couleurs` et l'ensemble `autres_couleurs` (les éléments présents dans `couleurs` mais pas dans `autres_couleurs`).

## Les ensembles ordonnés (Sorted Sets)

Les ensembles ordonnés associent un score (nombre flottant) à chaque élément, permettant le tri.

**Exemple :**

<pre><code>ZADD classement 10 "Alice"</code></pre>
*   `ZADD classement 10 "Alice"`: Ajoute l'élément "Alice" à l'ensemble ordonné `classement` avec le score 10.

<pre><code>ZADD classement 5 "Bob"</code></pre>
*   `ZADD classement 5 "Bob"`: Ajoute l'élément "Bob" à l'ensemble ordonné `classement` avec le score 5.

<pre><code>ZADD classement 15 "Charlie"</code></pre>
*   `ZADD classement 15 "Charlie"`: Ajoute l'élément "Charlie" à l'ensemble ordonné `classement` avec le score 15.

<pre><code>ZRANGE classement 0 -1 WITHSCORES</code></pre>
*   `ZRANGE classement 0 -1 WITHSCORES`: Retourne les éléments de l'ensemble ordonné `classement` triés par score croissant, avec leurs scores.

<pre><code>ZREVRANGE classement 0 -1</code></pre>
*   `ZREVRANGE classement 0 -1`: Retourne les éléments de l'ensemble ordonné `classement` triés par score décroissant.

<pre><code>ZRANK classement "Alice"</code></pre>
*   `ZRANK classement "Alice"`: Retourne le rang (l'index, commençant à 0) de l'élément "Alice" dans l'ensemble ordonné `classement` (selon l'ordre croissant des scores).

<pre><code>ZSCORE classement "Bob"</code></pre>
*   `ZSCORE classement "Bob"`: Retourne le score de l'élément "Bob" dans l'ensemble ordonné `classement`.

## Les Hashs

Les hashs dans Redis permettent de stocker des paires champ-valeur au sein d'une même clé. Ils sont utiles pour représenter des objets.

**Exemple :**

<pre><code>HSET utilisateur:1 nom "Alice" age 25</code></pre>
*   `HSET utilisateur:1 nom "Alice" age 25`: Définit les champs `nom` à "Alice" et `age` à 25 dans le hash identifié par la clé `utilisateur:1`.

<pre><code>HGET utilisateur:1 nom</code></pre>
*   `HGET utilisateur:1 nom`: Récupère la valeur du champ `nom` dans le hash `utilisateur:1`.

<pre><code>HGETALL utilisateur:1</code></pre>
*   `HGETALL utilisateur:1`: Récupère tous les champs et leurs valeurs du hash `utilisateur:1`.

<pre><code>HMSET utilisateur:2 nom "Bob" age 30 ville "Paris"</code></pre>
*   `HMSET utilisateur:2 nom "Bob" age 30 ville "Paris"`: Définit plusieurs champs (`nom`, `age`, `ville`) et leurs valeurs dans le hash `utilisateur:2`.

<pre><code>HMGET utilisateur:2 nom ville</code></pre>
*   `HMGET utilisateur:2 nom ville`: Récupère les valeurs des champs `nom` et `ville` du hash `utilisateur:2`.

<pre><code>HINCRBY utilisateur:1 age 1</code></pre>
*   `HINCRBY utilisateur:1 age 1`: Incrémente la valeur du champ `age` du hash `utilisateur:1` de 1. Utile pour les compteurs.

<pre><code>HVALS utilisateur:2</code></pre>
*   `HVALS utilisateur:2`: Récupère toutes les valeurs des champs du hash `utilisateur:2`.

<pre><code>HKEYS utilisateur:1</code></pre>
*   `HKEYS utilisateur:1`: Récupère tous les noms des champs du hash `utilisateur:1`.

<pre><code>HEXISTS utilisateur:1 ville</code></pre>
*   `HEXISTS utilisateur:1 ville`: Vérifie si le champ `ville` existe dans le hash `utilisateur:1`. Retourne 1 si c'est le cas, 0 sinon.

<pre><code>HLEN utilisateur:2</code></pre>
*   `HLEN utilisateur:2`: Retourne le nombre de champs dans le hash `utilisateur:2`.

## Pub/Sub (Publication/Abonnement)

Redis implémente le modèle de publication/abonnement, permettant une communication asynchrone entre clients.

**Exemple :**

Client 1 (abonnement aux canaux `canal1` et `canal2`) :

<pre><code>SUBSCRIBE canal1 canal2</code></pre>
*   `SUBSCRIBE canal1 canal2`: Le client s'abonne aux canaux `canal1` et `canal2`. Il recevra tous les messages publiés sur ces canaux.

Client 2 (publication de messages) :

<pre><code>PUBLISH canal1 "Bonjour le monde !"</code></pre>
*   `PUBLISH canal1 "Bonjour le monde !"`: Publie le message "Bonjour le monde !" sur le canal `canal1`.

<pre><code>PUBLISH canal2 "Autre message"</code></pre>
*   `PUBLISH canal2 "Autre message"`: Publie le message "Autre message" sur le canal `canal2`.

<pre><code>PUBLISH message "Ceci n'arrivera pas aux clients abonnés à canal1 ou canal2"</code></pre>
*   `PUBLISH message "Ceci n'arrivera pas aux clients abonnés à canal1 ou canal2"`: Ce message est publié sur le canal `message` et ne sera reçu que par les clients abonnés à ce canal.

Client 3 (abonnement avec un motif) :

<pre><code>PSUBSCRIBE mes*</code></pre>
*   `PSUBSCRIBE mes*`: Le client s'abonne à tous les canaux dont le nom commence par "mes".

<pre><code>PUBLISH message "Message important"</code></pre>
*   `PUBLISH message "Message important"`: Ce message sera reçu par le Client 3 car le canal `message` correspond au motif `mes*`.

<pre><code>PUBLISH message_test "Message de test"</code></pre>
*   `PUBLISH message_test "Message de test"`: Ce message sera également reçu par le Client 3.

<pre><code>PUBLISH test "Ce message ne sera pas reçu car ne commence pas par mes"</code></pre>
*   `PUBLISH test "Ce message ne sera pas reçu car ne commence pas par mes"`: Ce message ne sera pas reçu par le Client 3.

## Afficher les clés

La commande `KEYS` permet d'afficher les clés correspondant à un motif. **Attention :** En production, sur des bases de données importantes, l'utilisation de `KEYS *` est fortement déconseillée car elle peut bloquer le serveur. Privilégiez l'utilisation de `SCAN` pour une itération plus performante.

**Exemple :**

<pre><code>KEYS *</code></pre>
*   `KEYS *`: Affiche toutes les clés de la base de données. **À éviter en production sur des bases importantes.**

<pre><code>KEYS uti*</code></pre>
*   `KEYS uti*`: Affiche les clés commençant par "uti".

## Changer de base de données

Redis supporte plusieurs bases de données logiques (16 par défaut, numérotées de 0 à 15). La commande `SELECT` permet de sélectionner une base de données.

**Exemple :**

<pre><code>SELECT 1</code></pre>
*   `SELECT 1`: Sélectionne la base de données numéro 1.

## Reprise sur panne (Persistance)

Redis offre deux mécanismes principaux de persistance des données sur disque, permettant la reprise sur panne :

*   **RDB (Redis DataBase) :** Crée des snapshots (instantanés) des données à intervalles réguliers. C'est une méthode compacte et performante pour les sauvegardes, mais une perte de données est possible entre le dernier snapshot et la panne.
*   **AOF (Append Only File) :** Enregistre chaque opération d'écriture dans un fichier journal. Cette méthode assure une meilleure durabilité des données (moins de risque de perte), mais le fichier AOF peut être plus volumineux que les fichiers RDB.

Il est possible d'utiliser les deux mécanismes simultanément pour une sécurité maximale. La configuration de la persistance se fait dans le fichier `redis.conf`.


## Conclusion
Ce TP nous a offert une introduction pratique à Redis et à ses capacités. Nous avons manipulé les différents types de données : chaînes, listes, ensembles, ensembles ordonnés et hashs, en utilisant les commandes correspondantes. Nous avons également abordé des concepts importants tels que l'expiration de clés, le Pub/Sub et la persistance des données. En comparant Redis à d'autres bases de données NoSQL, 

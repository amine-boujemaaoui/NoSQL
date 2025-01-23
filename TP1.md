
# Rapport : Introduction et Utilisation de Redis

## **Introduction générale**
Redis est une base de données NoSQL clé-valeur en mémoire (in-memory) qui permet de manipuler efficacement des données simples et structurées. Son architecture rapide et légère en fait un outil idéal pour des scénarios tels que le caching, la gestion de sessions, les classements et les notifications en temps réel. Ce rapport explore les manipulations essentielles pour comprendre son fonctionnement, ses structures de données, et son utilisation dans des applications pratiques.

---

## **Partie 1 : Commandes de base et introduction à Redis**

### **Connexion au serveur Redis**
1. **Démarrage du serveur Redis** :  
   Lancer le serveur Redis avec la commande :  
   ```bash
   redis-server
   ```
2. **Connexion via le client Redis (CLI)** :  
   Utiliser le client CLI pour interagir avec Redis :  
   ```bash
   redis-cli
   ```
   Par défaut, Redis fonctionne en local (`127.0.0.1`) sur le port **6379**.

### **Manipulations de base**
Redis prend en charge plusieurs opérations essentielles basées sur le modèle CRUD :
- **Créer une clé avec une valeur** :  
  ```bash
  SET nom "Alice"
  ```
  Cette commande définit une clé `nom` avec la valeur `"Alice"`.  
- **Lire une clé existante** :  
  ```bash
  GET nom
  ```
- **Supprimer une clé** :  
  ```bash
  DEL nom
  ```

> **Note importante** : Par défaut, Redis stocke les données en mémoire. Si le serveur est arrêté, les données risquent de ne pas être conservées à moins que la persistance ne soit configurée.

### **Exemple pratique : compteur de visiteurs**
1. Initialisation du compteur :  
   ```bash
   SET visiteur 0
   ```
2. Incrémentation automatique :  
   ```bash
   INCR visiteur
   ```
3. Décrémentation :  
   ```bash
   DECR visiteur
   ```

### **Configuration de la durée de vie d’une clé (TTL)**
1. **Définir une durée de vie** :  
   ```bash
   SET ma_clé "valeur"
   EXPIRE ma_clé 60
   ```
   Ici, la clé `ma_clé` sera supprimée après 60 secondes.  
2. **Vérifier la durée de vie restante** :  
   ```bash
   TTL ma_clé
   ```

---

## **Partie 2 : Structures de données avancées**

Redis supporte des structures de données variées qui augmentent sa polyvalence.

### **Listes**
Les listes permettent de stocker des éléments ordonnés.  
1. **Ajouter des éléments** :  
   ```bash
   LPUSH ma_liste "A"
   RPUSH ma_liste "B"
   ```
   - `LPUSH` ajoute à gauche (début).  
   - `RPUSH` ajoute à droite (fin).  
2. **Lire une liste entière** :  
   ```bash
   LRANGE ma_liste 0 -1
   ```
   Ici, `0` désigne le premier élément et `-1` le dernier.

3. **Supprimer un élément** :  
   - Supprimer à gauche :  
     ```bash
     LPOP ma_liste
     ```
   - Supprimer à droite :  
     ```bash
     RPOP ma_liste
     ```

### **Sets**
Les sets sont des collections non ordonnées où chaque élément est unique.  
1. **Ajouter des éléments** :  
   ```bash
   SADD mon_set "A" "B" "C"
   ```
2. **Vérifier si un élément existe** :  
   ```bash
   SISMEMBER mon_set "A"
   ```
3. **Supprimer un élément** :  
   ```bash
   SREM mon_set "A"
   ```

### **Sets ordonnés**
Les sets ordonnés permettent de stocker des éléments associés à un score.  
1. **Ajouter des éléments avec un score** :  
   ```bash
   ZADD classement 100 "Alice" 200 "Bob"
   ```
2. **Récupérer les éléments dans l’ordre croissant** :  
   ```bash
   ZRANGE classement 0 -1 WITHSCORES
   ```
3. **Récupérer les éléments dans l’ordre décroissant** :  
   ```bash
   ZREVRANGE classement 0 -1 WITHSCORES
   ```

### **Hashmaps**
Les hashmaps permettent de stocker des paires clé-valeur organisées.  
1. **Ajouter des champs à un hash** :  
   ```bash
   HSET utilisateur:1 nom "Alice" age 30 email "alice@example.com"
   ```
2. **Récupérer tous les champs** :  
   ```bash
   HGETALL utilisateur:1
   ```
3. **Incrémenter un champ numérique** :  
   ```bash
   HINCRBY utilisateur:1 age 1
   ```

---

## **Partie 3 : Fonctionnalités avancées**

### **Publication/Souscription (Pub/Sub)**
Redis peut être utilisé comme un système de messagerie temps réel.  
1. **Souscrire à un canal** :  
   ```bash
   SUBSCRIBE canal1
   ```
   Un client inscrit à ce canal restera en attente des messages.  
2. **Publier un message sur un canal** :  
   ```bash
   PUBLISH canal1 "Nouveau message"
   ```
   Tous les clients abonnés recevront immédiatement le message.

3. **Souscription avec pattern** :  
   Pour s’abonner à plusieurs canaux correspondant à un motif donné :  
   ```bash
   PSUBSCRIBE "canal*"
   ```

### **Gestion des bases de données**
Redis prend en charge jusqu'à 16 bases de données par défaut.  
1. **Changer de base de données** :  
   ```bash
   SELECT 1
   ```
   Par défaut, Redis utilise la base `0`.  
2. **Lister toutes les clés d’une base** :  
   ```bash
   KEYS *
   ```

---

### **Gestion des bases multiples dans Redis**
Redis supporte jusqu'à 16 bases de données par défaut, numérotées de 0 à 15. Ces bases sont indépendantes les unes des autres. Voici les détails sur leur gestion :

1. **Sélection d’une base de données** :  
   Par défaut, Redis se connecte à la base numéro `0`. Vous pouvez changer de base avec la commande :  
   ```bash
   SELECT <numéro_de_base>
   ```
   Exemple :  
   ```bash
   SELECT 1
   ```
   Cela vous connecte à la base numéro 1.

2. **Clés spécifiques à chaque base** :  
   Les clés d’une base ne sont pas accessibles depuis une autre base. Si vous changez de base et que vous utilisez `KEYS *`, seules les clés de la base active seront affichées.

3. **Limites des bases multiples** :  
   - Redis ne fournit pas de mécanisme pour partager des données entre les bases.
   - Le nombre maximal de bases est fixé à 16 par défaut, mais peut être modifié dans le fichier de configuration (`databases` dans `redis.conf`).
   - L’utilisation de bases multiples est rarement conseillée dans des applications modernes, car les bases Redis sont davantage conçues pour des environnements où chaque instance Redis est dédiée à une tâche spécifique.

---

### **Limites de la persistance dans Redis**
Redis offre plusieurs mécanismes de persistance, mais chacun présente des limites à prendre en compte :

#### 1. **Snapshots (RDB - Redis Database File)**
   Redis génère des snapshots périodiques pour sauvegarder l’état de la base.  
   - **Avantages** :  
     - Processus rapide et adapté pour des sauvegardes planifiées.  
     - Format compact, facile à transférer ou sauvegarder.  
   - **Limites** :  
     - Les données créées ou modifiées après le dernier snapshot sont perdues en cas de panne.  
     - Peut provoquer des blocages temporaires si le fichier snapshot est volumineux.

#### 2. **Append-Only File (AOF)**
   Redis consigne toutes les écritures dans un fichier journal pour une persistance quasi-temps réel.  
   - **Avantages** :  
     - Sauvegarde plus granulaire, avec moins de pertes de données en cas de panne.  
     - Peut être configuré pour synchroniser les écritures immédiatement ou à intervalles réguliers.  
   - **Limites** :  
     - Fichier AOF plus volumineux que les snapshots RDB.  
     - Performance légèrement réduite, car chaque commande d’écriture est journalisée.  
     - Peut nécessiter une réécriture périodique pour éviter une croissance excessive du fichier.

#### 3. **Mode combiné (RDB + AOF)**  
   Redis peut être configuré pour utiliser les deux méthodes afin de combiner les avantages des snapshots et du journal d’écriture.  
   - **Limites** :  
     - Complexité accrue dans la configuration.  
     - Nécessite des ressources système supplémentaires (mémoire et processeur).

---

## **Conclusion**
Redis est une base NoSQL rapide et flexible, idéale pour les applications nécessitant des performances élevées et des manipulations en temps réel. Grâce à ses types de données variés (listes, sets, hashmaps) et ses fonctionnalités avancées (TTL, Pub/Sub), Redis s’adapte à de nombreux cas d’usage comme les classements, la gestion de sessions, ou encore les systèmes de notifications. Cependant, pour des données critiques, il est impératif de configurer la persistance afin de garantir leur conservation en cas de panne.

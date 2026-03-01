Salut les futurs experts SQL !

Ce TP est conçu pour vous faire manipuler concrètement l'optimisation de requêtes. L'objectif est de comprendre comment un index peut transformer une requête lente en une requête rapide, et surtout, comment identifier ce besoin d'indexation.

---

### **TP : Optimisation de Requêtes SQL par les Index**

**Objectif Pédagogique :**
Maîtriser l'analyse d'un plan d'exécution et l'utilisation des index pour améliorer significativement les performances des requêtes.

**Contexte & Prérequis :**
*   Connaissances de base en SQL (SELECT, INSERT, UPDATE, DELETE).
*   Compréhension des concepts de base de données relationnelles.
*   Accès à un SGBD (PostgreSQL, MySQL, SQL Server, Oracle, SQLite) et un client SQL.

---

**Mise en Situation : Le Cas de la Base "GestionCommerciale"**

Imaginez que vous travaillez sur une base de données de gestion commerciale. La table `Commandes` contient des millions d'enregistrements. Votre application métier doit fréquemment récupérer des commandes avec un certain statut et passées avant une certaine date. Récemment, les utilisateurs se plaignent de la lenteur de cette fonctionnalité. Votre mission est d'identifier la cause et d'y remédier.

---

**Étapes du TP :**

**Étape 1 : Préparation de l'Environnement et des Données**

1.  **Création de la table `Commandes` :**
    Créez une table nommée `Commandes` avec les colonnes suivantes :
    *   `id` (clé primaire, auto-incrémentée)
    *   `client_id` (INTEGER, pour simuler l'ID d'un client)
    *   `date_commande` (DATE)
    *   `montant_total` (DECIMAL(10, 2))
    *   `statut` (VARCHAR(50), par exemple : 'En attente', 'Validée', 'Expédiée', 'Annulée')

    *(**Conseil IA :** N'hésitez pas à demander à votre IA : "Génère le script SQL de création de table pour `Commandes` avec ces colonnes pour [votre SGBD].")*

2.  **Insertion de données volumineuses :**
    Insérez un grand nombre d'enregistrements (par exemple, entre 500 000 et 1 000 000) dans la table `Commandes`. Assurez-vous que les valeurs pour `statut` et `date_commande` soient variées et réalistes.

    *(**Conseil IA :** C'est une tâche parfaite pour l'IA ! Demandez-lui : "Génère un script SQL pour insérer 750 000 commandes aléatoires dans la table `Commandes` pour [votre SGBD], avec des statuts variés et des dates sur les 5 dernières années.")*

**Étape 2 : Identification de la Requête Lente**

1.  **La requête problématique :**
    Exécutez la requête suivante (adaptez la date et le statut si besoin) :

    ```sql
    SELECT id, client_id, date_commande, montant_total
    FROM Commandes
    WHERE statut = 'En attente' AND date_commande < '2023-01-01';
    ```

2.  **Analyse du plan d'exécution initial :**
    Utilisez la commande `EXPLAIN ANALYZE` (ou son équivalent dans votre SGBD, par exemple `EXPLAIN` pour MySQL/SQLite, `SET STATISTICS IO, TIME ON` pour SQL Server) pour obtenir le plan d'exécution et le temps réel d'exécution de la requête.

    ```sql
    EXPLAIN ANALYZE
    SELECT id, client_id, date_commande, montant_total
    FROM Commandes
    WHERE statut = 'En attente' AND date_commande < '2023-01-01';
    ```
    *(**Note :** `EXPLAIN ANALYZE` exécute réellement la requête, ce qui est crucial pour obtenir les temps réels.)*

3.  **Observation :**
    Notez le temps d'exécution total et identifiez les opérations principales dans le plan. Vous devriez probablement voir un "Seq Scan" (PostgreSQL) ou "Full Table Scan" (MySQL/SQL Server) sur la table `Commandes`.

**Étape 3 : Analyse du Plan d'Exécution et Conception de l'Index**

1.  **Interprétation :**
    Le "Seq Scan" indique que le SGBD parcourt toute la table ligne par ligne pour trouver les enregistrements correspondants. C'est inefficace sur une grande table.

2.  **Détermination des colonnes à indexer :**
    En vous basant sur la clause `WHERE` de la requête lente, déterminez quelle(s) colonne(s) sont les meilleures candidates pour un index afin d'accélérer la recherche. Pensez à l'ordre des colonnes si vous créez un index composite.

    *(**Conseil IA :** Si l'interprétation du plan d'exécution vous semble ardue, une IA peut vous aider. Demandez-lui : "J'ai ce plan d'exécution [collez votre plan ici], quelle est l'opération la plus coûteuse et quelle colonne devrais-je envisager d'indexer pour la requête `SELECT ... WHERE statut = 'En attente' AND date_commande < '2023-01-01';` ?")*

**Étape 4 : Création de l'Index**

1.  **Rédigez la commande `CREATE INDEX` :**
    Créez l'index que vous avez identifié à l'étape précédente. Un index composite sur `(statut, date_commande)` est souvent très efficace pour ce type de requête.

    ```sql
    -- Exemple pour un index composite
    CREATE INDEX idx_commandes_statut_date ON Commandes (statut, date_commande);
    ```

2.  **Exécutez la commande :**
    Lancez la création de l'index. Cela peut prendre un certain temps sur une grande table.

**Étape 5 : Vérification de l'Optimisation**

1.  **Ré-exécution de la requête avec `EXPLAIN ANALYZE` :**
    Exécutez à nouveau la requête problématique avec `EXPLAIN ANALYZE`.

    ```sql
    EXPLAIN ANALYZE
    SELECT id, client_id, date_commande, montant_total
    FROM Commandes
    WHERE statut = 'En attente' AND date_commande < '2023-01-01';
    ```

2.  **Comparaison et observation :**
    *   Comparez le nouveau temps d'exécution avec le temps initial. Vous devriez observer une amélioration drastique (souvent de plusieurs ordres de grandeur).
    *   Analysez le nouveau plan d'exécution. Vous devriez maintenant voir une opération de type "Index Scan" ou "Bitmap Index Scan" utilisant l'index que vous avez créé.

---

**Questions de Réflexion et d'Approfondissement :**

1.  **Pourquoi l'index a-t-il amélioré la performance ?** Expliquez le principe derrière cette accélération (structure de données de l'index, accès direct vs. séquentiel).
2.  **Quels sont les inconvénients de l'utilisation des index ?** (Pensez à l'impact sur les opérations d'écriture, l'espace disque, etc.)
3.  **Dans quel cas un index pourrait-il ne pas être utile, voire nuire aux performances ?** (Exemples : tables très petites, colonnes avec très peu de valeurs distinctes, requêtes sans clause `WHERE` pertinente).
4.  **Si la requête était `SELECT * FROM Commandes WHERE statut = 'En attente';` quel index auriez-vous créé ? Et si c'était `SELECT * FROM Commandes WHERE date_commande < '2023-01-01';` ?**
5.  **Explorez les "covering indexes" :** Si la requête était `SELECT id, date_commande FROM Commandes WHERE statut = 'En attente' AND date_commande < '2023-01-01';`, comment un index pourrait-il "couvrir" cette requête pour éviter même un accès à la table principale ?


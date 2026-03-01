Bonjour à toutes et à tous,

Ce TP est une opportunité d'explorer un concept fondamental en gestion de bases de données : les index. L'objectif est de comprendre leur utilité, leur fonctionnement et leur impact sur les performances de vos applications.

## TP : Exploration des Index SQL et de leur Impact

### Objectif Pédagogique

À l'issue de ce TP, vous serez capable de :
*   Créer différents types d'index (simples, uniques, composites).
*   Analyser le plan d'exécution de requêtes SQL pour évaluer l'impact des index.
*   Mesurer l'influence des index sur les performances de lecture (SELECT) et d'écriture (INSERT, UPDATE, DELETE).
*   Identifier les scénarios où l'utilisation d'index est pertinente et leurs limites.

### Prérequis

*   Connaissances de base en SQL (création de tables, insertion de données, requêtes SELECT, UPDATE, DELETE).
*   Un environnement de base de données relationnelle fonctionnel. Pour ce TP, **PostgreSQL** est recommandé en raison de son outil `EXPLAIN ANALYZE` très informatif, mais les concepts sont transposables à d'autres SGBD.
*   Un client SQL (psql, DBeaver, pgAdmin, etc.) pour exécuter vos requêtes.

### Environnement de Travail

Nous utiliserons PostgreSQL. Les commandes `EXPLAIN ANALYZE` sont spécifiques à ce SGBD. Si vous utilisez un autre SGBD, adaptez la commande d'analyse de plan d'exécution (par exemple, `EXPLAIN` pour MySQL, `EXPLAIN PLAN FOR` pour Oracle).

---

### Déroulement du TP

#### Étape 1 : Préparation de l'environnement et des données

1.  **Créez une base de données** dédiée à ce TP si ce n'est pas déjà fait.
    ```sql
    CREATE DATABASE tp_index;
    \c tp_index; -- Connectez-vous à la nouvelle base de données
    ```

2.  **Créez une table `Produits`** avec des colonnes variées.
    ```sql
    CREATE TABLE Produits (
        id SERIAL PRIMARY KEY,
        nom VARCHAR(100) NOT NULL,
        description TEXT,
        prix DECIMAL(10, 2) NOT NULL,
        date_ajout DATE DEFAULT CURRENT_DATE,
        categorie_id INT,
        code_produit VARCHAR(20) UNIQUE -- Ajouté pour l'index unique
    );
    ```

3.  **Insérez un grand volume de données.** Pour bien observer l'impact des index, nous avons besoin de beaucoup de lignes (au moins 100 000, idéalement 500 000 à 1 000 000).
    *   **Astuce :** Demandez à votre assistant IA de vous générer un script SQL pour insérer 500 000 lignes aléatoires dans la table `Produits`. Précisez les types de données pour chaque colonne.
    *   Voici un exemple de boucle simple pour PostgreSQL (à adapter et exécuter dans un bloc `DO $$ ... END $$` ou via un script externe) :
        ```sql
        DO $$
        DECLARE
            i INT := 0;
            max_rows INT := 500000; -- Nombre de lignes à insérer
        BEGIN
            WHILE i < max_rows LOOP
                INSERT INTO Produits (nom, description, prix, date_ajout, categorie_id, code_produit)
                VALUES (
                    'Produit ' || i,
                    'Description détaillée du produit ' || i || ' pour le test.',
                    (RANDOM() * 1000)::DECIMAL(10, 2),
                    CURRENT_DATE - (RANDOM() * 365)::INT * INTERVAL '1 day',
                    (RANDOM() * 10)::INT + 1, -- Catégories de 1 à 10
                    'CODE-' || LPAD(i::TEXT, 7, '0')
                );
                i := i + 1;
            END LOOP;
        END $$;
        ```

4.  **Analysez la table.** Ceci met à jour les statistiques du planificateur de requêtes, ce qui est crucial pour des plans d'exécution précis.
    ```sql
    ANALYZE Produits;
    ```

#### Étape 2 : Analyse des performances sans index (Baseline)

Nous allons établir une référence de performance avant d'ajouter des index.

1.  **Exécutez une requête de sélection simple** sur une colonne non indexée (`categorie_id` ou `nom`).
    ```sql
    EXPLAIN ANALYZE
    SELECT *
    FROM Produits
    WHERE categorie_id = 5;
    ```
    *   **Notez le "Execution Time"** et le "Planning Time". Observez le plan d'exécution (probablement un "Seq Scan" ou "Full Table Scan").

2.  **Exécutez une autre requête** sur une colonne textuelle.
    ```sql
    EXPLAIN ANALYZE
    SELECT id, nom, prix
    FROM Produits
    WHERE nom LIKE 'Produit 123%';
    ```
    *   **Notez les temps** et le plan d'exécution.

#### Étape 3 : Création et analyse d'un index simple

1.  **Créez un index sur la colonne `categorie_id`.**
    ```sql
    CREATE INDEX idx_produits_categorie_id ON Produits (categorie_id);
    ```
    *   **Observez le temps de création** de l'index. Que cela vous indique-t-il sur les ressources nécessaires ?

2.  **Ré-exécutez la première requête de l'Étape 2.**
    ```sql
    EXPLAIN ANALYZE
    SELECT *
    FROM Produits
    WHERE categorie_id = 5;
    ```
    *   **Comparez le "Execution Time"** avec la baseline.
    *   **Analysez le plan d'exécution.** Voyez-vous un "Index Scan" ou "Bitmap Index Scan" ? Qu'est-ce que cela signifie ?

3.  **Exécutez une requête qui ne bénéficie pas de cet index.**
    ```sql
    EXPLAIN ANALYZE
    SELECT *
    FROM Produits
    WHERE prix > 500;
    ```
    *   Pourquoi cet index n'est-il pas utilisé ici ?

#### Étape 4 : Création et analyse d'un index unique

La colonne `code_produit` a déjà une contrainte `UNIQUE`, ce qui crée automatiquement un index unique. Nous allons l'exploiter.

1.  **Exécutez une requête de sélection sur `code_produit`.**
    ```sql
    EXPLAIN ANALYZE
    SELECT *
    FROM Produits
    WHERE code_produit = 'CODE-0000123'; -- Adaptez avec un code existant
    ```
    *   **Analysez le plan d'exécution.** Est-ce un "Index Scan" ? Pourquoi est-il si rapide ?

2.  **Tentez d'insérer une ligne avec un `code_produit` existant.**
    ```sql
    INSERT INTO Produits (nom, description, prix, categorie_id, code_produit)
    VALUES ('Produit Dupliqué', 'Description', 10.00, 1, 'CODE-0000123');
    ```
    *   Quelle erreur obtenez-vous ? Quel est le rôle de l'index unique dans ce cas ?

#### Étape 5 : Création et analyse d'un index composite

Un index composite est créé sur plusieurs colonnes. Il est utile pour des requêtes filtrant sur ces colonnes simultanément.

1.  **Exécutez une requête avec plusieurs conditions `WHERE`.**
    ```sql
    EXPLAIN ANALYZE
    SELECT *
    FROM Produits
    WHERE categorie_id = 3 AND prix > 750;
    ```
    *   **Notez les temps** et le plan d'exécution. L'index `idx_produits_categorie_id` est-il utilisé ?

2.  **Créez un index composite sur `categorie_id` et `prix`.**
    ```sql
    CREATE INDEX idx_produits_cat_prix ON Produits (categorie_id, prix);
    ```

3.  **Ré-exécutez la requête précédente.**
    ```sql
    EXPLAIN ANALYZE
    SELECT *
    FROM Produits
    WHERE categorie_id = 3 AND prix > 750;
    ```
    *   **Comparez les temps** et le plan d'exécution. L'index composite est-il utilisé ? Est-ce plus rapide ?

4.  **Exécutez une requête qui utilise seulement la deuxième colonne de l'index composite.**
    ```sql
    EXPLAIN ANALYZE
    SELECT *
    FROM Produits
    WHERE prix > 750;
    ```
    *   L'index `idx_produits_cat_prix` est-il utilisé ? Pourquoi ou pourquoi pas ?

#### Étape 6 : Impact des index sur les opérations d'écriture

Les index accélèrent les lectures, mais ils ont un coût sur les écritures (INSERT, UPDATE, DELETE) car ils doivent être maintenus à jour.

1.  **Mesurez le temps d'insertion d'une nouvelle ligne.**
    ```sql
    EXPLAIN ANALYZE
    INSERT INTO Produits (nom, description, prix, date_ajout, categorie_id, code_produit)
    VALUES ('Nouveau Produit Test', 'Description test', 99.99, CURRENT_DATE, 1, 'CODE-NEW-001');
    ```
    *   **Notez le temps.**

2.  **Mesurez le temps de mise à jour d'une ligne** sur une colonne indexée.
    ```sql
    EXPLAIN ANALYZE
    UPDATE Produits
    SET prix = 123.45
    WHERE code_produit = 'CODE-0000001'; -- Adaptez avec un code existant
    ```
    *   **Notez le temps.**

3.  **Mesurez le temps de suppression d'une ligne** sur une colonne indexée.
    ```sql
    EXPLAIN ANALYZE
    DELETE FROM Produits
    WHERE code_produit = 'CODE-0000002'; -- Adaptez avec un code existant
    ```
    *   **Notez le temps.**

    *   **Réflexion :** Comparez ces temps avec ce que vous imaginez qu'ils seraient sans index. Pour des opérations unitaires, la différence est souvent minime, mais imaginez des milliers ou millions d'opérations.

#### Étape 7 : Nettoyage

Supprimez les index et la table pour nettoyer votre environnement.

```sql
DROP INDEX idx_produits_categorie_id;
DROP INDEX idx_produits_cat_prix;
-- L'index unique sur code_produit est lié à la contrainte UNIQUE, il sera supprimé avec la table.
DROP TABLE Produits;
```

---

### Questions d'Analyse et de Réflexion

1.  Décrivez en vos propres mots le rôle principal d'un index dans une base de données.
2.  Comment l'outil `EXPLAIN ANALYZE` vous a-t-il aidé à comprendre l'impact des index ? Quelles informations clés en avez-vous tirées ?
3.  Dans quels scénarios un index simple sur une colonne est-il plus efficace qu'un index composite ? Et inversement ?
4.  Quels sont les inconvénients majeurs de l'utilisation excessive d'index ? Pensez aux performances et à l'espace disque.
5.  Imaginez une table `Commandes` avec des colonnes `id_commande` (PK), `id_client`, `date_commande`, `montant_total`. Proposez un ou plusieurs index pertinents et justifiez vos choix pour les requêtes suivantes :
    *   "Trouver toutes les commandes d'un client donné."
    *   "Trouver toutes les commandes passées entre deux dates spécifiques."
    *   "Trouver les 10 commandes les plus récentes pour un client donné."
6.  Si vous deviez expliquer les index à un débutant en une minute, quels seraient les points clés que vous aborderiez ?
7.  Comment l'IA vous a-t-elle assisté dans ce TP ? Avez-vous rencontré des limites ou des biais dans ses réponses, notamment pour la génération de données ou l'explication des plans d'exécution ?

---

### Conseils pour la Réussite

*   **Prenez des notes :** Consignez les temps d'exécution et les plans d'exécution pour chaque requête. C'est essentiel pour la comparaison.
*   **Ne vous contentez pas de copier-coller :** Comprenez chaque commande et chaque résultat. Si quelque chose n'est pas clair, cherchez des explications (y compris auprès de votre assistant IA).
*   **Expérimentez :** Modifiez les valeurs dans les clauses `WHERE`, essayez d'autres colonnes pour les index, observez ce qui se passe.
*   **Soyez curieux :** Les index sont un vaste sujet. Ce TP n'est qu'une introduction.

Bonne exploration !
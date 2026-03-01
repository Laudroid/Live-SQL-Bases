Absolument ! Voici un sujet de TP conçu pour les apprenants, en tenant compte de l'utilisation de l'IA comme un outil d'apprentissage.

---

## TP 1 : Installation de PostgreSQL et Première Requête SELECT

### Objectif du TP

*   Installer un serveur de base de données PostgreSQL sur votre machine.
*   Exécuter une requête `SELECT` simple pour interroger des données.

### Énoncé du TP

Installer le système de gestion de base de données PostgreSQL sur votre machine. Créer une base de données et une table simple, puis insérer quelques données. Exécuter une requête `SELECT` pour afficher l'intégralité du contenu de cette table.

### Contexte

Ce premier TP est une étape fondamentale. Il vous permet de mettre en place l'environnement nécessaire pour manipuler des bases de données relationnelles et d'exécuter votre première commande SQL. Comprendre comment interagir avec un SGBD est la base de tout travail avec les données.

### Matériel et Outils

*   Un ordinateur (Windows, macOS ou Linux).
*   Une connexion internet.
*   Un terminal ou une invite de commandes.
*   Un navigateur web.

### Déroulement du TP

#### Étape 1 : Installation de PostgreSQL

1.  **Téléchargement :** Rendez-vous sur le site officiel de PostgreSQL ([www.postgresql.org](https://www.postgresql.org/download/)).
2.  **Installation :**
    *   Choisissez la version correspondant à votre système d'exploitation.
    *   Suivez les instructions d'installation. Lors de l'installation, il vous sera demandé de définir un mot de passe pour l'utilisateur `postgres` (l'administrateur par défaut). **Retenez bien ce mot de passe !**
    *   Vous pouvez choisir d'installer également `pgAdmin` (une interface graphique pour gérer PostgreSQL) et `psql` (le client en ligne de commande). Nous utiliserons principalement `psql` dans ce TP.
    *   **Conseil :** N'hésitez pas à consulter la documentation officielle de PostgreSQL ou à solliciter l'aide d'une IA pour des instructions spécifiques à votre système d'exploitation. Par exemple, vous pouvez demander : "Comment installer PostgreSQL sur [votre système d'exploitation] ?"

3.  **Vérification de l'installation :**
    *   Ouvrez votre terminal ou invite de commandes.
    *   Tapez la commande suivante et appuyez sur Entrée :
        ```bash
        psql --version
        ```
    *   Vous devriez voir s'afficher la version de PostgreSQL installée. Si ce n'est pas le cas, vérifiez que PostgreSQL a été correctement ajouté à votre PATH (variable d'environnement).

#### Étape 2 : Connexion au serveur et création d'une base de données

1.  **Connexion à `psql` :**
    *   Dans votre terminal, connectez-vous au serveur PostgreSQL en tant qu'utilisateur `postgres` :
        ```bash
        psql -U postgres
        ```
    *   Le système vous demandera le mot de passe que vous avez défini lors de l'installation. Entrez-le et appuyez sur Entrée.
    *   Vous devriez voir l'invite `postgres=#` ou `psql (version)` apparaître, indiquant que vous êtes connecté.

2.  **Création de votre première base de données :**
    *   Créez une nouvelle base de données nommée `ma_premiere_db` :
        ```sql
        CREATE DATABASE ma_premiere_db;
        ```
    *   Un message `CREATE DATABASE` devrait s'afficher.

3.  **Connexion à votre nouvelle base de données :**
    *   Pour travailler dans cette base de données, vous devez vous y connecter :
        ```sql
        \c ma_premiere_db
        ```
    *   L'invite devrait changer pour `ma_premiere_db=#`, confirmant que vous êtes bien connecté à votre base de données.

#### Étape 3 : Création d'une table et insertion de données

1.  **Création de la table `apprenants` :**
    *   Nous allons créer une table simple pour stocker des informations sur des apprenants. Copiez et collez le code SQL suivant dans votre terminal (assurez-vous d'être connecté à `ma_premiere_db`) :
        ```sql
        CREATE TABLE apprenants (
            id SERIAL PRIMARY KEY,
            nom VARCHAR(100) NOT NULL,
            promotion VARCHAR(10)
        );
        ```
    *   Un message `CREATE TABLE` devrait s'afficher.

2.  **Insertion de données :**
    *   Maintenant, ajoutons quelques apprenants à notre table :
        ```sql
        INSERT INTO apprenants (nom, promotion) VALUES ('Alice Dupont', '2024');
        INSERT INTO apprenants (nom, promotion) VALUES ('Bob Martin', '2023');
        INSERT INTO apprenants (nom, promotion) VALUES ('Charlie Leblanc', '2024');
        ```
    *   Après chaque commande `INSERT`, vous devriez voir `INSERT 0 1` s'afficher, indiquant qu'une ligne a été insérée.

#### Étape 4 : Exécution de la première requête SELECT

1.  **Sélectionner toutes les données :**
    *   C'est le moment de votre première requête `SELECT` ! Pour afficher toutes les colonnes et toutes les lignes de la table `apprenants`, utilisez la commande suivante :
        ```sql
        SELECT * FROM apprenants;
        ```
    *   **Bravo !** Vous devriez voir un tableau affichant les données que vous avez insérées.

2.  **Quitter `psql` :**
    *   Lorsque vous avez terminé, tapez `\q` et appuyez sur Entrée pour quitter le client `psql`.

### Vérification et Validation

*   Avez-vous réussi à installer PostgreSQL sans erreur majeure ?
*   Avez-vous pu vous connecter à `psql` et créer votre base de données ?
*   La table `apprenants` a-t-elle été créée et les données insérées ?
*   La requête `SELECT * FROM apprenants;` a-t-elle affiché les trois lignes de données que vous avez insérées ?

Si toutes ces étapes sont validées, vous avez réussi ce premier TP !

### Questions et Défis Supplémentaires

Pour aller un peu plus loin et explorer davantage :

1.  **Sélectionner des colonnes spécifiques :** Comment modifier la requête `SELECT` pour n'afficher que les colonnes `nom` et `promotion` des apprenants ?
2.  **Filtrer les données :** Comment afficher uniquement les apprenants de la promotion '2024' ? (Indice : cherchez la clause `WHERE`).
3.  **Utilisation de l'IA pour l'explication :** Si vous avez utilisé une IA pour l'installation, demandez-lui de vous expliquer les étapes qu'elle a suggérées et pourquoi elles sont nécessaires. Demandez-lui également de vous expliquer la signification de `SERIAL PRIMARY KEY` et `VARCHAR(100) NOT NULL` dans la création de table.
4.  **Exploration de `pgAdmin` :** Si vous avez installé `pgAdmin`, explorez son interface. Comment réaliser les mêmes opérations (création de DB, table, insertion, SELECT) via l'interface graphique ?

### Ressources Utiles

*   **Documentation officielle PostgreSQL :** [www.postgresql.org/docs/](https://www.postgresql.org/docs/)
*   **Tutoriels SQL en ligne :** De nombreux sites proposent des tutoriels pour les bases de SQL (par exemple, W3Schools, OpenClassrooms).
*   **Votre IA préférée :** N'hésitez pas à lui poser des questions spécifiques sur les commandes SQL, les erreurs rencontrées ou les concepts.

---

Amusez-vous bien avec vos premières requêtes SQL ! Gardez l'esprit curieux, c'est la meilleure façon d'apprendre.
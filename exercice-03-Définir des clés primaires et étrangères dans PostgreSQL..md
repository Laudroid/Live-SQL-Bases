Absolument ! Voici une proposition de sujet de TP, pragmatique et orientée vers l'apprentissage actif, tout en tenant compte de l'usage de l'IA.

---

## TP : Maîtrise des Clés Primaires et Étrangères avec PostgreSQL

### Objectif du TP

Ce TP vise à vous permettre de :
*   Concevoir des schémas de tables relationnelles simples.
*   Définir et implémenter des contraintes de clés primaires (`PRIMARY KEY`) pour garantir l'unicité et l'identification des enregistrements.
*   Définir et implémenter des contraintes de clés étrangères (`FOREIGN KEY`) pour établir des relations entre les tables et maintenir l'intégrité référentielle des données.
*   Comprendre l'impact des options `ON DELETE` et `ON UPDATE` sur les clés étrangères.

### Contexte

Vous êtes chargé(e) de créer la base de données pour un système de gestion de bibliothèque simplifié. Ce système doit permettre de suivre les livres, leurs auteurs, et les emprunts effectués par les adhérents. L'intégrité des données est primordiale pour éviter les incohérences (par exemple, un livre sans auteur, ou un emprunt pour un adhérent inexistant).

### Prérequis

*   Connaissances de base en SQL (types de données, `CREATE TABLE`).
*   Accès à un environnement PostgreSQL (local ou via un service en ligne).

---

### Exercices

**Étape 1 : Modélisation et Réflexion Préliminaire**

Avant de rédiger la moindre ligne de SQL, prenez un moment pour réfléchir à la structure de votre base de données.

1.  **Identifiez les entités principales** de notre système de bibliothèque (ex: Livre, Auteur, Adhérent, Emprunt).
2.  Pour chaque entité, **listez les attributs essentiels** que vous souhaiteriez stocker (ex: pour un livre, titre, année de publication, etc.).
3.  **Déterminez les relations** entre ces entités. Par exemple, un livre a-t-il un ou plusieurs auteurs ? Un adhérent peut-il emprunter plusieurs livres ?
4.  **Réfléchissez aux clés primaires** pour chaque table : quelle colonne ou combinaison de colonnes identifiera de manière unique chaque enregistrement ?
5.  **Anticipez les clés étrangères** : quelles colonnes serviront à lier les tables entre elles ?

*Conseil : Vous pouvez esquisser un petit diagramme (même simple) sur papier ou mentalement pour visualiser ces relations.*

**Étape 2 : Création de la Table `Auteur`**

1.  Créez une table nommée `Auteur` avec les colonnes suivantes :
    *   `id_auteur` : un identifiant unique pour chaque auteur (clé primaire, entier auto-incrémenté).
    *   `nom` : le nom de l'auteur (texte, non nul).
    *   `prenom` : le prénom de l'auteur (texte, non nul).
    *   `date_naissance` : la date de naissance de l'auteur (date, optionnel).

2.  **Question :** Comment vous assurez-vous que `id_auteur` est bien auto-incrémenté dans PostgreSQL ? Expliquez la syntaxe utilisée.

**Étape 3 : Création de la Table `Livre`**

1.  Créez une table nommée `Livre` avec les colonnes suivantes :
    *   `id_livre` : un identifiant unique pour chaque livre (clé primaire, entier auto-incrémenté).
    *   `titre` : le titre du livre (texte, non nul).
    *   `annee_publication` : l'année de publication (entier).
    *   `isbn` : le numéro ISBN du livre (texte, doit être unique et non nul).
    *   `id_auteur` : une colonne qui fera référence à l'auteur du livre.

2.  **Définissez la clé étrangère** sur la colonne `id_auteur` pour qu'elle référence la table `Auteur`.

3.  **Question :** Si un auteur est supprimé de la base de données, que doit-il advenir des livres qu'il a écrits ?
    *   Choisissez un comportement `ON DELETE` approprié parmi `CASCADE`, `RESTRICT`, `SET NULL`, `NO ACTION`.
    *   Justifiez votre choix en expliquant les implications de chaque option dans ce contexte.

**Étape 4 : Création de la Table `Adherent`**

1.  Créez une table nommée `Adherent` avec les colonnes suivantes :
    *   `id_adherent` : un identifiant unique pour chaque adhérent (clé primaire, entier auto-incrémenté).
    *   `nom` : le nom de l'adhérent (texte, non nul).
    *   `prenom` : le prénom de l'adhérent (texte, non nul).
    *   `email` : l'adresse email de l'adhérent (texte, doit être unique et non nul).
    *   `date_inscription` : la date d'inscription de l'adhérent (date, non nul, par défaut la date actuelle).

**Étape 5 : Création de la Table `Emprunt`**

1.  Créez une table nommée `Emprunt` avec les colonnes suivantes :
    *   `id_emprunt` : un identifiant unique pour chaque emprunt (clé primaire, entier auto-incrémenté).
    *   `id_livre` : une colonne qui fera référence au livre emprunté.
    *   `id_adherent` : une colonne qui fera référence à l'adhérent qui a emprunté le livre.
    *   `date_emprunt` : la date à laquelle le livre a été emprunté (date, non nul, par défaut la date actuelle).
    *   `date_retour_prevue` : la date à laquelle le livre doit être retourné (date, non nul).
    *   `date_retour_reelle` : la date réelle de retour du livre (date, optionnel).

2.  **Définissez les clés étrangères** nécessaires pour lier cette table aux tables `Livre` et `Adherent`.

3.  **Question :**
    *   Si un livre est supprimé, que doit-il advenir des enregistrements d'emprunt le concernant ? Choisissez et justifiez un comportement `ON DELETE`.
    *   Si un adhérent est supprimé, que doit-il advenir de ses emprunts ? Choisissez et justifiez un comportement `ON DELETE`.

**Étape 6 : Test et Vérification**

1.  **Insérez quelques données** valides dans chaque table (au moins 2-3 enregistrements par table) pour tester que vos contraintes fonctionnent comme prévu.
    *   Ex: Insérez un auteur, puis un livre pour cet auteur, puis un adhérent, puis un emprunt pour ce livre et cet adhérent.

2.  **Tentez les opérations suivantes et observez les messages d'erreur :**
    *   Insérer un livre avec un `id_auteur` qui n'existe pas dans la table `Auteur`.
    *   Insérer un adhérent avec une adresse `email` déjà existante.
    *   Supprimer un auteur qui a encore des livres associés (selon le comportement `ON DELETE` que vous avez choisi).
    *   Supprimer un livre qui est actuellement emprunté (selon le comportement `ON DELETE` que vous avez choisi).

3.  **Expliquez les messages d'erreur** que vous obtenez et comment ils confirment le bon fonctionnement de vos contraintes.

---

### Aller plus loin (Optionnel)

*   **Ajoutez une contrainte `CHECK`** à la table `Livre` pour vous assurer que `annee_publication` n'est pas antérieure à une certaine année (ex: 1500).
*   **Modifiez la table `Emprunt`** pour ajouter une contrainte qui garantit que `date_retour_reelle` ne peut pas être antérieure à `date_emprunt`.
*   **Comment modéliseriez-vous une relation "plusieurs à plusieurs"** si un livre pouvait avoir plusieurs auteurs, et un auteur pouvait écrire plusieurs livres ? Décrivez la structure de table additionnelle nécessaire.

---

### Ressources Utiles

*   Documentation officielle PostgreSQL sur `CREATE TABLE` : [https://www.postgresql.org/docs/current/sql-createtable.html](https://www.postgresql.org/docs/current/sql-createtable.html)
*   Tutoriels SQL sur les clés primaires et étrangères.

Bon courage pour ce TP ! N'hésitez pas à expérimenter et à consulter la documentation si vous avez des doutes. L'objectif est de comprendre les mécanismes, pas seulement de copier-coller du code.
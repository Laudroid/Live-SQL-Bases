
Ce corrigé détaille les étapes attendues, les commandes correctes, les résultats obtenus et les explications techniques.

---

# 1️⃣ Installation de PostgreSQL

L’installation se fait depuis le site officiel :

PostgreSQL Global Development Group
[https://www.postgresql.org/download/](https://www.postgresql.org/download/)

Selon votre système :

* **Windows** : téléchargement de l’installateur EnterpriseDB
* **macOS** : installateur graphique ou Homebrew
* **Linux** : via le gestionnaire de paquets (`apt`, `dnf`, `yum`…)

---

## ✔ Vérification de l’installation

Commande :

```bash
psql --version
```

Résultat attendu (exemple) :

```bash
psql (PostgreSQL) 16.2
```

Si la version s’affiche, l’installation est correcte et `psql` est accessible dans le `PATH`.

---

# 2️⃣ Connexion et création de la base

## ✔ Connexion au serveur

```bash
psql -U postgres
```

* Mot de passe demandé
* Invite attendue :

```bash
postgres=#
```

Cela signifie que vous êtes connecté au serveur PostgreSQL avec l’utilisateur administrateur.

---

## Création de la base de données

```sql
CREATE DATABASE ma_premiere_db;
```

Résultat attendu :

```sql
CREATE DATABASE
```

---

## Connexion à la base

```sql
\c ma_premiere_db
```

Résultat attendu :

```bash
You are now connected to database "ma_premiere_db" as user "postgres".
ma_premiere_db=#
```

---

# 3️⃣ Création de la table et insertion des données

## Création de la table

```sql
CREATE TABLE apprenants (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100) NOT NULL,
    promotion VARCHAR(10)
);
```

Résultat attendu :

```sql
CREATE TABLE
```

---

## Explication technique

### `SERIAL PRIMARY KEY`

* `SERIAL` : crée automatiquement un entier auto-incrémenté
* `PRIMARY KEY` :

  * identifie chaque ligne de manière unique
  * empêche les doublons
  * crée automatiquement un index

Chaque nouvel apprenant aura un `id` généré automatiquement.

---

### `VARCHAR(100) NOT NULL`

* `VARCHAR(100)` : chaîne de caractères de maximum 100 caractères
* `NOT NULL` : la colonne ne peut pas être vide

Cela garantit l’intégrité des données.

---

## Insertion des données

```sql
INSERT INTO apprenants (nom, promotion) VALUES ('Alice Dupont', '2024');
INSERT INTO apprenants (nom, promotion) VALUES ('Bob Martin', '2023');
INSERT INTO apprenants (nom, promotion) VALUES ('Charlie Leblanc', '2024');
```

Résultat attendu à chaque fois :

```sql
INSERT 0 1
```

Cela signifie qu’une ligne a bien été ajoutée.

---

# 4️⃣ Première requête SELECT

## Afficher toutes les données

```sql
SELECT * FROM apprenants;
```

Résultat attendu :

```
 id |       nom        | promotion
----+------------------+-----------
  1 | Alice Dupont     | 2024
  2 | Bob Martin       | 2023
  3 | Charlie Leblanc  | 2024
(3 rows)
```

🎉 Bravo : vous venez d’exécuter votre première requête SQL !

---

# 5️⃣ Questions supplémentaires – Corrigé

---

## 1. Sélectionner uniquement certaines colonnes

```sql
SELECT nom, promotion FROM apprenants;
```

Résultat :

```
       nom        | promotion
------------------+-----------
 Alice Dupont     | 2024
 Bob Martin       | 2023
 Charlie Leblanc  | 2024
```

On remplace `*` par la liste des colonnes voulues.

---

## 2. Filtrer les données (clause WHERE)

Afficher uniquement les apprenants de la promotion 2024 :

```sql
SELECT * FROM apprenants
WHERE promotion = '2024';
```

Résultat :

```
 id |       nom        | promotion
----+------------------+-----------
  1 | Alice Dupont     | 2024
  3 | Charlie Leblanc  | 2024
```

La clause `WHERE` permet d’ajouter une condition.

---

## 3. Exploration via pgAdmin

Si vous avez installé :

 pgAdmin 4

Vous pouvez :

* Créer une base via clic droit → Create → Database
* Créer une table via Query Tool
* Exécuter des requêtes SQL dans l’éditeur graphique
* Visualiser les données via View/Edit Data

C’est exactement le même moteur PostgreSQL, mais avec une interface graphique.

# Correction – TP Modélisation E-A et Conversion Relationnelle (PostgreSQL)

## Partie 1 : Modélisation Entité-Association (E-A)

### 1. Identification des entités

#### Entité : REALISATEUR

**Attributs :**

* id_realisateur (clé primaire)
* nom
* prenom
* date_naissance
* nationalite

**Clé primaire :**

* id_realisateur

Justification :
On choisit une clé technique (identifiant numérique) plutôt que le nom/prénom afin d’éviter les ambiguïtés (homonymes).

---

#### Entité : FILM

**Attributs :**

* id_film (clé primaire)
* titre
* annee_sortie
* duree_minutes
* genre

**Clé primaire :**

* id_film

Justification :
Le titre seul n’est pas suffisant comme clé primaire (plusieurs films peuvent avoir le même titre). On utilise donc un identifiant technique.

---

### 2. Identification des relations

#### Relation : REALISE

* Un réalisateur peut réaliser plusieurs films.
* Un film est réalisé par un seul réalisateur (dans notre modèle simplifié).

**Cardinalités :**

* REALISATEUR (1,N)
* FILM (1,1)

Interprétation :

* Un réalisateur participe à 0..N films.
* Chaque film doit obligatoirement être associé à 1 et un seul réalisateur.

Il s’agit donc d’une relation 1-N.

---

## Partie 2 : Conversion en Schéma Relationnel

### 1. Définition des tables

#### Table : realisateur

| Colonne        | Type PostgreSQL | Contraintes |
| -------------- | --------------- | ----------- |
| id_realisateur | SERIAL          | PK          |
| nom            | VARCHAR(100)    | NOT NULL    |
| prenom         | VARCHAR(100)    | NOT NULL    |
| date_naissance | DATE            |             |
| nationalite    | VARCHAR(100)    |             |

Clé primaire :

* id_realisateur

---

#### Table : film

| Colonne        | Type PostgreSQL | Contraintes  |
| -------------- | --------------- | ------------ |
| id_film        | SERIAL          | PK           |
| titre          | VARCHAR(255)    | NOT NULL     |
| annee_sortie   | INTEGER         | NOT NULL     |
| duree_minutes  | INTEGER         |              |
| genre          | VARCHAR(100)    |              |
| id_realisateur | INTEGER         | FK, NOT NULL |

Clé primaire :

* id_film

Clé étrangère :

* id_realisateur référence realisateur(id_realisateur)

---

### 2. Gestion de la relation

Règle appliquée :

Pour une relation 1-N :

* On place la clé primaire de la table du côté "1" (realisateur)
* Comme clé étrangère dans la table du côté "N" (film)

Donc :
film.id_realisateur → realisateur.id_realisateur

Le NOT NULL sur id_realisateur traduit le fait qu’un film doit obligatoirement avoir un réalisateur.

---

## 3. Script SQL DDL complet (PostgreSQL)

```sql
-- Suppression des tables si elles existent (ordre important à cause des FK)
DROP TABLE IF EXISTS film;
DROP TABLE IF EXISTS realisateur;

-- Création de la table realisateur
CREATE TABLE realisateur (
    id_realisateur SERIAL PRIMARY KEY,
    nom VARCHAR(100) NOT NULL,
    prenom VARCHAR(100) NOT NULL,
    date_naissance DATE,
    nationalite VARCHAR(100)
);

-- Création de la table film
CREATE TABLE film (
    id_film SERIAL PRIMARY KEY,
    titre VARCHAR(255) NOT NULL,
    annee_sortie INTEGER NOT NULL,
    duree_minutes INTEGER,
    genre VARCHAR(100),
    id_realisateur INTEGER NOT NULL,
    CONSTRAINT fk_film_realisateur
        FOREIGN KEY (id_realisateur)
        REFERENCES realisateur (id_realisateur)
        ON DELETE RESTRICT
        ON UPDATE CASCADE
);
```

---

## Vérification de cohérence

* Chaque entité du modèle E-A devient une table.
* Chaque attribut devient une colonne.
* Les clés primaires sont définies.
* La relation 1-N est correctement traduite par une clé étrangère.
* Les contraintes NOT NULL traduisent les cardinalités obligatoires.
* Les types sont adaptés à PostgreSQL (SERIAL, DATE, VARCHAR, INTEGER).


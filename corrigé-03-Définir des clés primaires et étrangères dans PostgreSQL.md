# Correction – TP : Maîtrise des Clés Primaires et Étrangères avec PostgreSQL

## Étape 1 : Modélisation et réflexion préliminaire

### 1. Entités principales

Les entités du système sont :

* `Auteur`
* `Livre`
* `Adherent`
* `Emprunt`

### 2. Attributs essentiels

**Auteur**

* id_auteur
* nom
* prenom
* date_naissance

**Livre**

* id_livre
* titre
* annee_publication
* isbn
* id_auteur

**Adherent**

* id_adherent
* nom
* prenom
* email
* date_inscription

**Emprunt**

* id_emprunt
* id_livre
* id_adherent
* date_emprunt
* date_retour_prevue
* date_retour_reelle

### 3. Relations entre les entités

* Un auteur peut écrire plusieurs livres.
* Un livre appartient à un seul auteur (dans ce modèle simplifié).
* Un adhérent peut emprunter plusieurs livres.
* Un livre peut être emprunté plusieurs fois dans le temps.
* Un emprunt concerne un seul livre et un seul adhérent.

### 4. Clés primaires

Chaque table possède une clé primaire technique de type entier auto-incrémenté :

* Auteur → id_auteur
* Livre → id_livre
* Adherent → id_adherent
* Emprunt → id_emprunt

### 5. Clés étrangères

* Livre.id_auteur → référence Auteur.id_auteur
* Emprunt.id_livre → référence Livre.id_livre
* Emprunt.id_adherent → référence Adherent.id_adherent

---

## Étape 2 : Création de la table Auteur

```sql
CREATE TABLE Auteur (
    id_auteur SERIAL PRIMARY KEY,
    nom VARCHAR(100) NOT NULL,
    prenom VARCHAR(100) NOT NULL,
    date_naissance DATE
);
```

### Explication de l’auto-incrémentation

En PostgreSQL, le mot-clé `SERIAL` :

* Crée une colonne entière
* Crée automatiquement une séquence
* Définit la valeur par défaut avec `nextval(sequence)`

Ainsi, chaque insertion génère automatiquement une nouvelle valeur unique.

Depuis PostgreSQL 10, on peut aussi utiliser :

```sql
id_auteur INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY
```

Cette syntaxe est plus conforme au standard SQL.

---

## Étape 3 : Création de la table Livre

```sql
CREATE TABLE Livre (
    id_livre SERIAL PRIMARY KEY,
    titre VARCHAR(255) NOT NULL,
    annee_publication INT,
    isbn VARCHAR(20) NOT NULL UNIQUE,
    id_auteur INT NOT NULL,
    CONSTRAINT fk_livre_auteur
        FOREIGN KEY (id_auteur)
        REFERENCES Auteur(id_auteur)
        ON DELETE RESTRICT
        ON UPDATE CASCADE
);
```

### Choix du ON DELETE

Options possibles :

* CASCADE : supprime automatiquement les livres de l’auteur supprimé.
* RESTRICT : empêche la suppression si des livres existent.
* SET NULL : met id_auteur à NULL.
* NO ACTION : similaire à RESTRICT.

Choix recommandé : `RESTRICT`

Justification :

Dans une bibliothèque, il n’est pas logique de supprimer un auteur si des livres existent encore en base.
`RESTRICT` empêche la suppression et protège l’intégrité des données.

---

## Étape 4 : Création de la table Adherent

```sql
CREATE TABLE Adherent (
    id_adherent SERIAL PRIMARY KEY,
    nom VARCHAR(100) NOT NULL,
    prenom VARCHAR(100) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    date_inscription DATE NOT NULL DEFAULT CURRENT_DATE
);
```

Points importants :

* `UNIQUE` sur email garantit qu’un email ne peut apparaître qu’une seule fois.
* `DEFAULT CURRENT_DATE` initialise automatiquement la date d’inscription.

---

## Étape 5 : Création de la table Emprunt

```sql
CREATE TABLE Emprunt (
    id_emprunt SERIAL PRIMARY KEY,
    id_livre INT NOT NULL,
    id_adherent INT NOT NULL,
    date_emprunt DATE NOT NULL DEFAULT CURRENT_DATE,
    date_retour_prevue DATE NOT NULL,
    date_retour_reelle DATE,
    CONSTRAINT fk_emprunt_livre
        FOREIGN KEY (id_livre)
        REFERENCES Livre(id_livre)
        ON DELETE RESTRICT
        ON UPDATE CASCADE,
    CONSTRAINT fk_emprunt_adherent
        FOREIGN KEY (id_adherent)
        REFERENCES Adherent(id_adherent)
        ON DELETE RESTRICT
        ON UPDATE CASCADE
);
```

### Comportement ON DELETE

#### Suppression d’un livre

Choix : `RESTRICT`

Justification :
On ne doit pas pouvoir supprimer un livre s’il est référencé dans un emprunt.
Cela évite de perdre l’historique des prêts.

#### Suppression d’un adhérent

Choix : `RESTRICT`

Justification :
Si l’adhérent a des emprunts en cours ou historiques, il est préférable d’empêcher la suppression.
Dans un système réel, on désactiverait l’adhérent plutôt que de le supprimer.

---

## Étape 6 : Tests et vérifications

### Insertion de données valides

```sql
INSERT INTO Auteur (nom, prenom, date_naissance)
VALUES ('Hugo', 'Victor', '1802-02-26');

INSERT INTO Auteur (nom, prenom)
VALUES ('Camus', 'Albert');

INSERT INTO Livre (titre, annee_publication, isbn, id_auteur)
VALUES ('Les Miserables', 1862, '1234567890', 1);

INSERT INTO Adherent (nom, prenom, email)
VALUES ('Dupont', 'Jean', 'jean.dupont@email.com');

INSERT INTO Emprunt (id_livre, id_adherent, date_retour_prevue)
VALUES (1, 1, CURRENT_DATE + INTERVAL '14 days');
```

---

### Tests d’erreurs

#### 1. Livre avec auteur inexistant

```sql
INSERT INTO Livre (titre, isbn, id_auteur)
VALUES ('Livre Invalide', '9999999999', 999);
```

Erreur attendue :
Violation de contrainte de clé étrangère.

Explication :
PostgreSQL refuse l’insertion car id_auteur = 999 n’existe pas dans Auteur.

---

#### 2. Email déjà existant

```sql
INSERT INTO Adherent (nom, prenom, email)
VALUES ('Martin', 'Paul', 'jean.dupont@email.com');
```

Erreur attendue :
Violation de contrainte UNIQUE.

Explication :
La base garantit l’unicité des emails.

---

#### 3. Suppression d’un auteur avec livres existants

```sql
DELETE FROM Auteur WHERE id_auteur = 1;
```

Erreur attendue :
Violation de contrainte de clé étrangère.

Explication :
`ON DELETE RESTRICT` empêche la suppression car des livres référencent cet auteur.

---

#### 4. Suppression d’un livre emprunté

```sql
DELETE FROM Livre WHERE id_livre = 1;
```

Erreur attendue :
Violation de contrainte de clé étrangère.

Explication :
Le livre est référencé dans Emprunt.
L’intégrité référentielle est protégée.

---

## Aller plus loin

### 1. Contrainte CHECK sur l’année de publication

```sql
ALTER TABLE Livre
ADD CONSTRAINT check_annee_publication
CHECK (annee_publication >= 1500);
```

---

### 2. Vérifier la cohérence des dates d’emprunt

```sql
ALTER TABLE Emprunt
ADD CONSTRAINT check_dates_retour
CHECK (
    date_retour_reelle IS NULL
    OR date_retour_reelle >= date_emprunt
);
```

---

### 3. Relation plusieurs-à-plusieurs (Livre ↔ Auteur)

Si un livre peut avoir plusieurs auteurs et un auteur plusieurs livres, il faut créer une table d’association :

```sql
CREATE TABLE Livre_Auteur (
    id_livre INT NOT NULL,
    id_auteur INT NOT NULL,
    PRIMARY KEY (id_livre, id_auteur),
    FOREIGN KEY (id_livre) REFERENCES Livre(id_livre) ON DELETE CASCADE,
    FOREIGN KEY (id_auteur) REFERENCES Auteur(id_auteur) ON DELETE CASCADE
);
```

Cette table :

* Contient les clés étrangères vers les deux tables
* Possède une clé primaire composite
* Représente la relation N:N


Voici le **corrigé complet** du TP – Maîtrise des requêtes CRUD.

# Corrigé – TP SQL CRUD

> Les requêtes sont compatibles MySQL / PostgreSQL (sauf mention contraire).
> Si vous utilisez PostgreSQL, pensez à adapter les types (`BOOLEAN`, `SERIAL`, etc.).

---

# Partie 1 : Requêtes SELECT

---

### 1️⃣ Sélectionner tous les enregistrements de la table `Livres`

```sql
SELECT * FROM Livres;
```

---

### 2️⃣ Afficher le titre et l'année de publication des livres disponibles

```sql
SELECT titre, annee_publication
FROM Livres
WHERE disponible = TRUE;
```

---

### 3️⃣ Trouver tous les livres écrits par Victor Hugo

```sql
SELECT l.titre, l.annee_publication
FROM Livres l
JOIN Auteurs a ON l.auteur_id = a.id
WHERE a.nom = 'Hugo'
AND a.prenom = 'Victor';
```

Ici on utilise une **jointure (JOIN)** pour relier les livres à leurs auteurs.

---

### 4️⃣ Lister les titres des livres publiés après 1900

```sql
SELECT titre
FROM Livres
WHERE annee_publication > 1900;
```

---

### 5️⃣ Compter le nombre total de livres disponibles

```sql
SELECT COUNT(*) AS nombre_livres_disponibles
FROM Livres
WHERE disponible = TRUE;
```

---

### 6️⃣ Afficher les auteurs de nationalité Britannique

```sql
SELECT nom, prenom
FROM Auteurs
WHERE nationalite = 'Britannique';
```

---

# Partie 2 : Requêtes INSERT

---

### 1️⃣ Ajouter l’auteur J.R.R. Tolkien

```sql
INSERT INTO Auteurs (nom, prenom, nationalite)
VALUES ('Tolkien', 'J.R.R.', 'Britannique');
```

---

### 2️⃣ Ajouter le livre "Le Seigneur des Anneaux"

Vérifier d'abord l'id de Tolkien :

```sql
SELECT id FROM Auteurs
WHERE nom = 'Tolkien';
```

(Supposons que l’id retourné soit 5)

```sql
INSERT INTO Livres (titre, auteur_id, annee_publication, disponible)
VALUES ('Le Seigneur des Anneaux', 5, 1954, TRUE);
```

---

### 3️⃣ Ajouter "Le Petit Prince" de Antoine de Saint-Exupéry

#### a) Ajouter l’auteur

```sql
INSERT INTO Auteurs (nom, prenom, nationalite)
VALUES ('Saint-Exupéry', 'Antoine de', 'Française');
```

#### b) Vérifier son id

```sql
SELECT id FROM Auteurs
WHERE nom = 'Saint-Exupéry';
```

(Supposons que l’id soit 6)

#### c) Ajouter le livre

```sql
INSERT INTO Livres (titre, auteur_id, annee_publication, disponible)
VALUES ('Le Petit Prince', 6, 1943, TRUE);
```

---

# Partie 3 : Requêtes UPDATE

---

### 1️⃣ Marquer "Vingt mille lieues sous les mers" comme non disponible

```sql
UPDATE Livres
SET disponible = FALSE
WHERE titre = 'Vingt mille lieues sous les mers';
```

---

### 2️⃣ Corriger l'année de publication de "1984"

```sql
UPDATE Livres
SET annee_publication = 1949
WHERE titre = '1984';
```

---

### 3️⃣ Renommer "Les Misérables"

```sql
UPDATE Livres
SET titre = 'Les Misérables (Édition Complète)'
WHERE titre = 'Les Misérables';
```

---

# Partie 4 : Requêtes DELETE

---

### 1️⃣ Supprimer "Le Crime de l'Orient-Express"

```sql
DELETE FROM Livres
WHERE titre = 'Le Crime de l''Orient-Express';
```

 Notez l'échappement avec deux apostrophes `''`.

---

### 2️⃣ Supprimer "Le Seigneur des Anneaux"

```sql
DELETE FROM Livres
WHERE titre = 'Le Seigneur des Anneaux';
```

---

### 3️⃣ Supprimer l’auteur Antoine de Saint-Exupéry

Important : un auteur ne peut pas être supprimé si des livres lui sont encore rattachés (contrainte de clé étrangère).

#### Étape 1 – Supprimer ses livres

```sql
DELETE FROM Livres
WHERE auteur_id = (
    SELECT id FROM Auteurs
    WHERE nom = 'Saint-Exupéry'
);
```

#### Étape 2 – Supprimer l’auteur

```sql
DELETE FROM Auteurs
WHERE nom = 'Saint-Exupéry';
```

---

# Récapitulatif pédagogique

Dans ce TP, vous avez pratiqué les 4 opérations fondamentales du SQL :

| Opération | Mot-clé | Objectif              |
| --------- | ------- | --------------------- |
| CREATE    | INSERT  | Ajouter des données   |
| READ      | SELECT  | Lire des données      |
| UPDATE    | UPDATE  | Modifier des données  |
| DELETE    | DELETE  | Supprimer des données |

Vous avez également manipulé :

* ✅ Les conditions (`WHERE`)
* ✅ Les jointures (`JOIN`)
* ✅ Les fonctions d’agrégation (`COUNT`)
* ✅ Les contraintes de clé étrangère
* ✅ La gestion des dépendances entre tables



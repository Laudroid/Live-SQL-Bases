# Correction – TP SQL orienté PostgreSQL

Maîtrise des Sous-Requêtes et Fonctions d'Agrégation

## 1. Adaptation du script pour PostgreSQL

PostgreSQL ne supporte pas `AUTO_INCREMENT`. On utilise `GENERATED ALWAYS AS IDENTITY` (ou `SERIAL`).

Voici la version compatible PostgreSQL :

```sql
CREATE TABLE Categories (
    id_categorie INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    nom_categorie VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE Produits (
    id_produit INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    nom_produit VARCHAR(100) NOT NULL,
    prix_unitaire DECIMAL(10, 2) NOT NULL,
    id_categorie INT REFERENCES Categories(id_categorie)
);

CREATE TABLE Clients (
    id_client INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    nom_client VARCHAR(100) NOT NULL,
    ville VARCHAR(50)
);

CREATE TABLE Commandes (
    id_commande INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    date_commande DATE NOT NULL,
    id_client INT REFERENCES Clients(id_client)
);

CREATE TABLE Lignes_Commande (
    id_ligne INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    id_commande INT REFERENCES Commandes(id_commande),
    id_produit INT REFERENCES Produits(id_produit),
    quantite INT NOT NULL,
    prix_total_ligne DECIMAL(10, 2) NOT NULL
);
```

Les requêtes d’insertion restent identiques.

---

# Correction des exercices

---

# Partie 1 – Fonctions d’Agrégation Simples

### 1. Nombre total de produits distincts

```sql
SELECT COUNT(*) AS nombre_total_produits
FROM Produits;
```

---

### 2. Prix unitaire moyen de tous les produits

```sql
SELECT AVG(prix_unitaire) AS prix_moyen
FROM Produits;
```

---

### 3. Produit le plus cher (nom et prix)

```sql
SELECT nom_produit, prix_unitaire
FROM Produits
ORDER BY prix_unitaire DESC
LIMIT 1;
```

Alternative avec sous-requête :

```sql
SELECT nom_produit, prix_unitaire
FROM Produits
WHERE prix_unitaire = (
    SELECT MAX(prix_unitaire)
    FROM Produits
);
```

---

### 4. Nombre total de commandes

```sql
SELECT COUNT(*) AS total_commandes
FROM Commandes;
```

---

### 5. Chiffre d’affaires total

```sql
SELECT SUM(prix_total_ligne) AS chiffre_affaires_total
FROM Lignes_Commande;
```

---

# Partie 2 – Agrégation avec GROUP BY

### 1. Nombre de produits par catégorie

```sql
SELECT c.nom_categorie, COUNT(p.id_produit) AS nombre_produits
FROM Categories c
LEFT JOIN Produits p ON p.id_categorie = c.id_categorie
GROUP BY c.nom_categorie
ORDER BY c.nom_categorie;
```

---

### 2. Prix unitaire moyen par catégorie

```sql
SELECT c.nom_categorie, AVG(p.prix_unitaire) AS prix_moyen
FROM Categories c
JOIN Produits p ON p.id_categorie = c.id_categorie
GROUP BY c.nom_categorie
ORDER BY c.nom_categorie;
```

---

### 3. Chiffre d’affaires par client

```sql
SELECT cl.nom_client,
       SUM(lc.prix_total_ligne) AS chiffre_affaires
FROM Clients cl
JOIN Commandes co ON co.id_client = cl.id_client
JOIN Lignes_Commande lc ON lc.id_commande = co.id_commande
GROUP BY cl.nom_client
ORDER BY chiffre_affaires DESC;
```

---

### 4. Nombre de commandes par ville

```sql
SELECT cl.ville,
       COUNT(co.id_commande) AS nombre_commandes
FROM Clients cl
JOIN Commandes co ON co.id_client = cl.id_client
GROUP BY cl.ville
ORDER BY nombre_commandes DESC;
```

---

# Partie 3 – Introduction aux Sous-Requêtes

### 1. Produits plus chers que le prix moyen

```sql
SELECT nom_produit, prix_unitaire
FROM Produits
WHERE prix_unitaire > (
    SELECT AVG(prix_unitaire)
    FROM Produits
);
```

---

### 2. Clients ayant passé plus d’une commande

```sql
SELECT nom_client
FROM Clients
WHERE id_client IN (
    SELECT id_client
    FROM Commandes
    GROUP BY id_client
    HAVING COUNT(*) > 1
);
```

---

### 3. Produits "Électronique" plus chers que la moyenne de leur catégorie

```sql
SELECT p.nom_produit, p.prix_unitaire
FROM Produits p
JOIN Categories c ON c.id_categorie = p.id_categorie
WHERE c.nom_categorie = 'Électronique'
AND p.prix_unitaire > (
    SELECT AVG(prix_unitaire)
    FROM Produits p2
    JOIN Categories c2 ON c2.id_categorie = p2.id_categorie
    WHERE c2.nom_categorie = 'Électronique'
);
```

---

### 4. Commandes supérieures au montant moyen

```sql
SELECT id_commande, total_commande
FROM (
    SELECT co.id_commande,
           SUM(lc.prix_total_ligne) AS total_commande
    FROM Commandes co
    JOIN Lignes_Commande lc ON lc.id_commande = co.id_commande
    GROUP BY co.id_commande
) AS sous_total
WHERE total_commande > (
    SELECT AVG(total_par_commande)
    FROM (
        SELECT SUM(prix_total_ligne) AS total_par_commande
        FROM Lignes_Commande
        GROUP BY id_commande
    ) AS moyenne
);
```

---

# Partie 4 – Sous-Requêtes Corrélées et Avancées

### 1. Produits plus chers que la moyenne de leur catégorie (corrélée)

```sql
SELECT p.nom_produit,
       p.prix_unitaire,
       c.nom_categorie
FROM Produits p
JOIN Categories c ON c.id_categorie = p.id_categorie
WHERE p.prix_unitaire > (
    SELECT AVG(p2.prix_unitaire)
    FROM Produits p2
    WHERE p2.id_categorie = p.id_categorie
);
```

---

### 2. Clients n’ayant jamais commandé de "Laptop"

Version avec NOT EXISTS (recommandée en PostgreSQL) :

```sql
SELECT cl.nom_client
FROM Clients cl
WHERE NOT EXISTS (
    SELECT 1
    FROM Commandes co
    JOIN Lignes_Commande lc ON lc.id_commande = co.id_commande
    JOIN Produits p ON p.id_produit = lc.id_produit
    WHERE co.id_client = cl.id_client
    AND p.nom_produit = 'Laptop'
);
```

---

### 3. Catégories sans produit vendu

```sql
SELECT c.nom_categorie
FROM Categories c
WHERE NOT EXISTS (
    SELECT 1
    FROM Produits p
    JOIN Lignes_Commande lc ON lc.id_produit = p.id_produit
    WHERE p.id_categorie = c.id_categorie
);
```

---

### 4. Client ayant dépensé le plus sur une seule commande

```sql
SELECT cl.nom_client,
       co.id_commande,
       SUM(lc.prix_total_ligne) AS total_commande
FROM Clients cl
JOIN Commandes co ON co.id_client = cl.id_client
JOIN Lignes_Commande lc ON lc.id_commande = co.id_commande
GROUP BY cl.nom_client, co.id_commande
ORDER BY total_commande DESC
LIMIT 1;
```

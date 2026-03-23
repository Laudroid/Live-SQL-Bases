# Correction – TP Index

## Étape 1 – Préparation et volumétrie

### Points clés attendus

* La colonne `id SERIAL PRIMARY KEY` crée automatiquement :

  * une contrainte de clé primaire
  * un index B-Tree unique associé
* La contrainte `UNIQUE` sur `code_produit` crée automatiquement un index unique B-Tree.
* Après insertion massive, la commande :

```sql
ANALYZE Produits;
```

est indispensable pour mettre à jour les statistiques (pg_statistic).
Sans statistiques à jour, PostgreSQL peut choisir un mauvais plan d’exécution.

---

## Étape 2 – Baseline sans index

### Requête sur `categorie_id`

```sql
EXPLAIN ANALYZE
SELECT *
FROM Produits
WHERE categorie_id = 5;
```

### Résultat attendu

Plan probable :

```
Seq Scan on produits
  Filter: (categorie_id = 5)
```

### Analyse

* PostgreSQL lit toute la table (scan séquentiel).
* Coût proportionnel au nombre total de lignes.
* Temps d’exécution significatif avec 500 000+ lignes.

Pourquoi ?

Car aucune structure d’accès rapide n’existe sur `categorie_id`.

---

### Requête sur `nom LIKE 'Produit 123%'`

Sans index adapté :

```
Seq Scan
```

Même comportement : lecture complète de la table.

Important :
Un index B-Tree peut être utilisé pour `LIKE 'prefix%'` **uniquement si** la collation et l’opérateur le permettent.
Mais ici, aucun index n’existe encore.

---

## Étape 3 – Index simple

### Création

```sql
CREATE INDEX idx_produits_categorie_id 
ON Produits (categorie_id);
```

### Observation

* Temps de création dépend du volume.
* PostgreSQL construit l’index en scannant toute la table.
* Consommation CPU + I/O disque.

---

### Nouvelle exécution

```sql
EXPLAIN ANALYZE
SELECT *
FROM Produits
WHERE categorie_id = 5;
```

### Plan attendu

Deux possibilités :

1. **Index Scan**
2. **Bitmap Index Scan + Bitmap Heap Scan**

Exemple courant :

```
Bitmap Heap Scan
  -> Bitmap Index Scan on idx_produits_categorie_id
```

### Explication

* Si la condition retourne peu de lignes → Index Scan.
* Si elle retourne beaucoup de lignes → Bitmap Scan (plus efficace pour lectures multiples).

### Comparaison avec baseline

* Execution Time fortement réduit.
* Planning Time légèrement augmenté (car choix d’index à évaluer).

---

### Pourquoi l’index n’est pas utilisé pour `prix > 500` ?

```sql
SELECT * FROM Produits WHERE prix > 500;
```

Car aucun index n’existe sur `prix`.

PostgreSQL ne peut pas exploiter `idx_produits_categorie_id` pour filtrer sur une autre colonne.

---

## Étape 4 – Index unique

La contrainte :

```sql
code_produit VARCHAR(20) UNIQUE
```

crée automatiquement :

```
CREATE UNIQUE INDEX ...
```

### Requête par égalité

```sql
SELECT * 
FROM Produits 
WHERE code_produit = 'CODE-0000123';
```

### Plan attendu

```
Index Scan using produits_code_produit_key
```

### Pourquoi si rapide ?

* Recherche par égalité.
* Index B-Tree.
* Cardinalité = 1 (unicité garantie).
* Accès direct logarithmique (O(log n)).

---

### Tentative de doublon

Erreur attendue :

```
ERROR: duplicate key value violates unique constraint
```

Rôle de l’index unique :

* Vérifier l’absence de valeur identique.
* Empêcher l’insertion.
* Garantir l’intégrité des données.

---

## Étape 5 – Index composite

### Requête initiale

```sql
SELECT *
FROM Produits
WHERE categorie_id = 3
AND prix > 750;
```

Avec seulement l’index simple :

* PostgreSQL peut utiliser `idx_produits_categorie_id`
* Puis filtrer `prix` ensuite.

Plan probable :

```
Bitmap Index Scan (categorie_id)
Filter: prix > 750
```

---

### Création index composite

```sql
CREATE INDEX idx_produits_cat_prix
ON Produits (categorie_id, prix);
```

### Nouvelle exécution

Plan attendu :

```
Index Scan using idx_produits_cat_prix
```

Amélioration car :

* L’index contient déjà les deux colonnes
* Moins de lignes à filtrer après lecture

---

### Cas important : ordre des colonnes

Index :

```
(categorie_id, prix)
```

Peut être utilisé pour :

* categorie_id = ?
* categorie_id = ? AND prix > ?
* categorie_id = ? AND prix BETWEEN ?

Mais PAS pour :

```
WHERE prix > 750
```

Pourquoi ?

Un index composite B-Tree respecte un ordre hiérarchique.
La première colonne est déterminante.

C’est la règle du préfixe gauche.

---

## Étape 6 – Impact sur les écritures

Chaque index doit être maintenu :

* INSERT → insertion dans chaque index
* UPDATE colonne indexée → mise à jour index
* DELETE → suppression entrée index

### INSERT

Avec plusieurs index :

* Plus lent qu’une table sans index.
* Différence faible pour une seule ligne.
* Impact fort pour insertion massive.

### UPDATE sur colonne indexée

```sql
UPDATE Produits
SET prix = 123.45
WHERE code_produit = ...
```

Deux effets :

1. Recherche rapide via index unique
2. Mise à jour éventuelle dans index composite si `prix` indexé

### DELETE

Même principe :

* Localisation rapide
* Suppression dans tous les index

---

## Réponses attendues aux questions

### 1. Rôle principal d’un index

Accélérer les lectures en fournissant une structure d’accès organisée aux données, au prix d’un coût supplémentaire en écriture et en stockage.

---

### 2. Apport de EXPLAIN ANALYZE

Permet d’observer :

* Type de scan (Seq Scan, Index Scan, Bitmap Scan)
* Coût estimé
* Nombre réel de lignes
* Temps d’exécution réel
* Écart entre estimation et réalité

C’est un outil fondamental d’optimisation sous PostgreSQL.

---

### 3. Index simple vs composite

Index simple préférable si :

* Filtrage sur une seule colonne
* Forte sélectivité

Index composite préférable si :

* Requêtes fréquentes combinant plusieurs colonnes
* Ordre respectant le préfixe gauche

---

### 4. Inconvénients des index

* Espace disque supplémentaire
* Ralentissement INSERT/UPDATE/DELETE
* Fragmentation
* Maintenance (VACUUM, REINDEX)

Trop d’index = dégradation globale des performances.

---

### 5. Cas de la table Commandes

Table :

```
(id_commande PK, id_client, date_commande, montant_total)
```

#### Trouver commandes d’un client

```sql
CREATE INDEX idx_commandes_client
ON Commandes (id_client);
```

#### Trouver entre deux dates

```sql
CREATE INDEX idx_commandes_date
ON Commandes (date_commande);
```

#### 10 plus récentes d’un client

```sql
CREATE INDEX idx_commandes_client_date
ON Commandes (id_client, date_commande DESC);
```

Permet :

```
WHERE id_client = ?
ORDER BY date_commande DESC
LIMIT 10
```

Index optimal car :

* Filtrage
* Tri évité
* Limitation rapide

---

### 6. Explication en une minute

Un index est une structure triée qui permet de retrouver des lignes rapidement sans lire toute la table.
Il accélère les SELECT mais ralentit les écritures et consomme de l’espace disque.
Il doit être créé uniquement sur les colonnes utilisées fréquemment dans les clauses WHERE, JOIN et ORDER BY.


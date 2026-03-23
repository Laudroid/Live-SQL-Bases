# Correction – TP Optimisation par les index

# Étape 1 – Création de la table

### 1. Création de la table `commandes`

En PostgreSQL, on utilise généralement `BIGSERIAL` ou `GENERATED ... AS IDENTITY` pour l’auto-incrément.

```sql
CREATE TABLE commandes (
    id BIGSERIAL PRIMARY KEY,
    client_id INTEGER NOT NULL,
    date_commande DATE NOT NULL,
    montant_total NUMERIC(10,2) NOT NULL,
    statut VARCHAR(50) NOT NULL
);
```

Remarques :

* `BIGSERIAL` est préférable si vous prévoyez plusieurs millions de lignes.
* `NUMERIC(10,2)` est adapté aux montants financiers.
* Les `NOT NULL` permettent au planificateur d’optimiser certaines estimations.

---

### 2. Insertion massive de données

En PostgreSQL, on peut générer des données volumineuses efficacement avec `generate_series()`.

Exemple pour 750 000 lignes :

```sql
INSERT INTO commandes (client_id, date_commande, montant_total, statut)
SELECT
    (random() * 10000)::int,
    CURRENT_DATE - ((random() * 1825)::int), -- 5 ans ≈ 1825 jours
    round((random() * 1000)::numeric, 2),
    (ARRAY['En attente','Validée','Expédiée','Annulée'])[floor(random()*4)+1]
FROM generate_series(1, 750000);
```

Après une insertion massive, il est important de mettre à jour les statistiques :

```sql
ANALYZE commandes;
```

Sans statistiques à jour, PostgreSQL peut produire un mauvais plan d’exécution.

---

# Étape 2 – Identification de la requête lente

Requête à analyser :

```sql
EXPLAIN ANALYZE
SELECT id, client_id, date_commande, montant_total
FROM commandes
WHERE statut = 'En attente'
AND date_commande < '2023-01-01';
```

---

## Plan attendu sans index

Sur une grande table sans index, PostgreSQL affichera typiquement :

```
Seq Scan on commandes
  Filter: (...)
```

Cela signifie :

* lecture complète de la table,
* chaque ligne est testée,
* coût proportionnel au nombre total de lignes.

Indicateurs importants à observer :

* `Seq Scan`
* `Rows Removed by Filter`
* `Execution Time`
* différence entre `rows=` estimées et réelles

---

# Étape 3 – Analyse et choix de l’index

### Pourquoi la requête est lente ?

Parce que PostgreSQL doit :

1. lire toutes les pages de la table,
2. tester chaque ligne,
3. filtrer selon deux colonnes non indexées.

Sur 750 000 lignes, cela implique un accès disque massif.

---

## Quelles colonnes indexer ?

La clause WHERE est :

```sql
WHERE statut = 'En attente'
AND date_commande < '2023-01-01'
```

On a :

* une condition d’égalité sur `statut`
* une condition de type intervalle (`<`) sur `date_commande`

En PostgreSQL (index B-Tree), l’ordre optimal est :

```
(colonne avec égalité, colonne avec intervalle)
```

Donc :

```sql
(statut, date_commande)
```

Pourquoi ?

Un index B-Tree fonctionne de gauche à droite :

* PostgreSQL filtre d’abord sur `statut`
* puis affine avec la condition sur `date_commande`

---

# Étape 4 – Création de l’index

```sql
CREATE INDEX idx_commandes_statut_date
ON commandes (statut, date_commande);
```

Puis :

```sql
ANALYZE commandes;
```

Cela garantit que le planner tient compte du nouvel index.

---

# Étape 5 – Vérification de l’optimisation

Relance :

```sql
EXPLAIN ANALYZE
SELECT id, client_id, date_commande, montant_total
FROM commandes
WHERE statut = 'En attente'
AND date_commande < '2023-01-01';
```

---

## Nouveau plan attendu

Vous devriez voir :

* `Index Scan`
  ou
* `Bitmap Index Scan` + `Bitmap Heap Scan`

### Pourquoi Bitmap Scan ?

PostgreSQL choisit souvent un Bitmap Scan quand :

* beaucoup de lignes correspondent,
* mais pas toute la table,
* et que la lecture en bloc est plus efficace.

---

## Résultat attendu

* chute drastique du `Execution Time`
* réduction massive des blocs lus
* disparition du `Seq Scan`

L’amélioration peut passer de plusieurs secondes à quelques millisecondes selon la volumétrie.

---

# Questions de réflexion – Correction

## 1. Pourquoi l’index améliore la performance ?

Un index B-Tree :

* est une structure arborescente équilibrée,
* permet un accès logarithmique O(log n),
* évite le scan complet.

Au lieu de parcourir 750 000 lignes, PostgreSQL :

1. localise directement la plage correspondant à `statut = 'En attente'`,
2. parcourt uniquement les entrées avec `date_commande < ...`.

On passe d’un coût proportionnel à N à un coût proportionnel à log(N) + K (K = lignes correspondantes).

---

## 2. Inconvénients des index

* Augmentation de l’espace disque.
* Ralentissement des INSERT.
* Ralentissement des UPDATE sur colonnes indexées.
* Ralentissement des DELETE.
* Maintenance supplémentaire (VACUUM, ANALYZE).

Chaque index est une structure supplémentaire à maintenir.

---

## 3. Cas où l’index est inutile ou contre-productif

### Table très petite

PostgreSQL privilégiera un Seq Scan, car :

* lire toute la table en mémoire est plus rapide qu’utiliser l’index.

### Faible sélectivité

Exemple :

```sql
WHERE statut = 'Validée'
```

Si 80 % des lignes ont ce statut, l’index est peu utile.

### Requêtes sans WHERE

Un index ne sert pas pour :

```sql
SELECT * FROM commandes;
```

---

## 4. Cas alternatifs

### Requête 1

```sql
SELECT * FROM commandes
WHERE statut = 'En attente';
```

Index recommandé :

```sql
CREATE INDEX idx_commandes_statut
ON commandes (statut);
```

---

### Requête 2

```sql
SELECT * FROM commandes
WHERE date_commande < '2023-01-01';
```

Index recommandé :

```sql
CREATE INDEX idx_commandes_date
ON commandes (date_commande);
```

---

## 5. Covering index (Index Only Scan)

Requête :

```sql
SELECT id, date_commande
FROM commandes
WHERE statut = 'En attente'
AND date_commande < '2023-01-01';
```

On peut créer :

```sql
CREATE INDEX idx_commandes_covering
ON commandes (statut, date_commande)
INCLUDE (id);
```

En PostgreSQL :

* `INCLUDE` ajoute des colonnes non clés,
* permet un `Index Only Scan`,
* évite l’accès à la table (Heap).

Condition supplémentaire :

* les pages doivent être "all-visible" (gérées par VACUUM).

Plan attendu :

```
Index Only Scan using idx_commandes_covering
```

Cela supprime totalement le `Heap Scan`.


# 6-Recherche Full-Text & alternatives  
## 1-Recherche Full-Text  
### 2-Requête avec to_tsvector, to_tsquery, ts_rank

---

PostgreSQL offre des fonctions spécifiques pour mener des recherches textuelles avancées grâce au module Full-Text Search. Les principales fonctions sont `to_tsvector`, `to_tsquery`, et `ts_rank`. Les combiner permet d’exécuter des requêtes efficaces et de classer les résultats par pertinence.

---

## 1. Fonctions essentielles

### 1.1 to_tsvector

- Convertit un texte brut en un **tsvector**, c’est-à-dire une représentation normalisée et indexable d’un document.
- Effectue un découpage (tokenization), supprime les stopwords, et applique un stemming selon la configuration linguistique.

**Exemple :**

```sql
SELECT to_tsvector('french', 'Les bases de données relationnelles sont complexes.');
```

Résultat possible :

```
'base':2 'donnée':3 'relationnel':4 'complexe':6
```

---

### 1.2 to_tsquery

- Génère un **tsquery** à partir d’une requête textuelle avec opérateurs booléens (`&` = AND, `|` = OR, `!` = NOT).
- Permet de rechercher la présence des mots dans un document.

**Exemple :**

```sql
SELECT to_tsquery('french', 'base & donnée');
```

Requête correspond à la recherche des documents contenant *base* ET *donnée*.

---

### 1.3 ts_rank

- Calcule un score de pertinence entre un document (`tsvector`) et une requête (`tsquery`).
- Permet de classer les résultats selon leur correspondance.

---

## 2. Utiliser ces fonctions dans une requête

Considérons une table `Article(titre TEXT, contenu TEXT)` :

```sql
SELECT titre,
       ts_rank(to_tsvector('french', contenu), to_tsquery('french', 'base & donnée')) AS rank
FROM Article
WHERE to_tsvector('french', contenu) @@ to_tsquery('french', 'base & donnée')
ORDER BY rank DESC;
```

- Clause `@@` signifie « correspond à la requête full-text ».
- `ts_rank` calcule la pertinence, les résultats sont triés pour mettre en tête les plus pertinents.

---

## 3. Optimisation avec colonnes matérialisées

Pour éviter le recalcul fréquent de `to_tsvector`, créer une colonne matérialisée indexée :

```sql
ALTER TABLE Article ADD COLUMN contenu_tsv tsvector GENERATED ALWAYS AS (to_tsvector('french', contenu)) STORED;
CREATE INDEX idx_contenu_tsv ON Article USING GIN(contenu_tsv);

SELECT titre, ts_rank(contenu_tsv, query) AS rank
FROM Article, to_tsquery('french', 'base & donnée') query
WHERE contenu_tsv @@ query
ORDER BY rank DESC;
```

---

## 4. Diagramme Mermaid – Workflow de la recherche Full-Text

```mermaid
flowchart TD
    Texte[Texte libre]
    ToTSVector[to_tsvector (normalisation)]
    ToTSQuery[to_tsquery (requête booléenne)]
    Match[Filtrage avec @@]
    RankCalcul[Calcul de ts_rank]
    Résultats[Résultats triés par pertinence]

    Texte --> ToTSVector
    ToTSQuery --> Match
    ToTSVector --> Match --> RankCalcul --> Résultats
```

---

## 5. Sources utilisées

- PostgreSQL Documentation, [Full Text Search Functions and Operators](https://www.postgresql.org/docs/current/textsearch-functions.html)  
- PostgreSQL Tutorial, [PostgreSQL ts_rank Function](https://www.postgresqltutorial.com/postgresql-ts_rank/)  
- Severalnines, [How to Use PostgreSQL Full Text Search Functions](https://severalnines.com/database-blog/how-use-full-text-search-postgresql)  

---

Les fonctions `to_tsvector`, `to_tsquery` et `ts_rank` forment le cœur du Full-Text Search dans PostgreSQL. Leur compréhension permet d’écrire des requêtes textuelles précises et performantes, capables de trier les résultats selon la pertinence réelle.
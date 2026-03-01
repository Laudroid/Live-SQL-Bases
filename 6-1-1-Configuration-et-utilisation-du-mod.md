# 6-Recherche Full-Text & alternatives  
## 1-Recherche Full-Text  
### 1-Configuration et utilisation du module Full-Text Search de PostgreSQL

---

PostgreSQL intègre un module puissant de **Full-Text Search (FTS)**, conçu pour effectuer des recherches textuelles avancées dans de larges volumes de texte. Ce module dépasse largement le simple filtre `LIKE` en proposant un index spécialisé, des fonctions linguistiques et une syntaxe dédiée.

---

## 1. Concepts clés du Full-Text Search PostgreSQL

- **tsvector** : format interne qui stocke un document sous forme vectorielle (listes de mots triés avec leurs positions).
- **tsquery** : format représentant la requête de recherche, avec opérateurs logiques (`AND`, `OR`, `NOT`) et possibilités de recherche de phrases.
- **Dictionnaires et configurations linguistiques** : gestion de la normalisation des mots, suppression des stopwords, stemming (réduction à la racine).
- **Index GIN (Generalized Inverted Index)** : index spécialisé optimisant les recherches full-text.

---

## 2. Configuration minimale

### 2.1 Création d’une colonne tsvector

Pour optimiser les recherches, on crée en général une colonne ou un index sur un `tsvector`. Exemple de table `Article` avec un champ `contenu` en texte :

```sql
ALTER TABLE Article
ADD COLUMN contenu_tsv tsvector;
```

### 2.2 Remplissage de la colonne tsvector

```sql
UPDATE Article
SET contenu_tsv = to_tsvector('french', contenu);
```

Ou via une colonne générée automatiquement (PostgreSQL 12+) :

```sql
ALTER TABLE Article
ADD COLUMN contenu_tsv tsvector GENERATED ALWAYS AS (to_tsvector('french', contenu)) STORED;
```

---

## 3. Création de l’index GIN

```sql
CREATE INDEX idx_contenu_tsv ON Article USING GIN(contenu_tsv);
```

Cet index permet d’accélérer les recherches full-text sur la colonne `contenu_tsv`.

---

## 4. Requête basique de recherche

```sql
SELECT id, titre
FROM Article
WHERE contenu_tsv @@ to_tsquery('french', 'base & données');
```

- L’opérateur `@@` signifie "correspond à la requête full-text".
- La requête cherche les documents contenant les mots "base" ET "données".

---

## 5. Exemple complet

Création table, insertion, index et recherche :

```sql
CREATE TABLE Article (
  id SERIAL PRIMARY KEY,
  titre TEXT,
  contenu TEXT,
  contenu_tsv tsvector GENERATED ALWAYS AS (to_tsvector('french', contenu)) STORED
);

INSERT INTO Article (titre, contenu) VALUES
('Introduction SQL', 'Le langage SQL est utilisé pour manipuler les bases de données.'),
('Base de données avancée', 'Les index améliorent la performance des requêtes.');

CREATE INDEX idx_contenu_tsv ON Article USING GIN(contenu_tsv);

SELECT id, titre
FROM Article
WHERE contenu_tsv @@ to_tsquery('french', 'base & données');
```

---

## 6. Diagramme Mermaid – Workflow Full-Text Search

```mermaid
flowchart TD
    Document[Document - texte libre]
    Convert[Conversion en tsvector (normalisation, stemming)]
    Index[Création index GIN sur tsvector]
    Requête[Requête tsquery (opérateurs booléens)]
    Recherche[Recherche rapide via index]
    Résultat[Documents correspondants]

    Document --> Convert --> Index
    Requête --> Recherche
    Index --> Recherche --> Résultat
```

---

## 7. Conseils et bonnes pratiques

- Utiliser la configuration de langue adaptée pour le stemming et stopwords (`french`, `english`, etc.).
- Préférer une colonne `tsvector` matérialisée (générée ou mise à jour via trigger) pour optimiser les performances.
- Exploiter les opérateurs booléens (`&`, `|`, `!`) pour affiner la recherche.
- Mettre à jour régulièrement les statistiques et analyser les plans d’exécution (`EXPLAIN`) pour vérifier l’utilisation de l’index.

---

## 8. Sources utilisées

- PostgreSQL Documentation, [Full Text Search](https://www.postgresql.org/docs/current/textsearch.html)  
- PostgreSQL Tutorial, [PostgreSQL Full Text Search](https://www.postgresqltutorial.com/postgresql-full-text-search/)  
- Severalnines, [PostgreSQL Full Text Search Guide](https://severalnines.com/database-blog/how-use-full-text-search-postgresql)  

---

Le module Full-Text Search de PostgreSQL fournit une infrastructure robuste et performante pour la recherche textuelle avancée, combinant indexation, traitement linguistique et syntaxe puissante afin d’exploiter efficacement des contenus volumineux.
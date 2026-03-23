# **Correction TP : Maîtriser la Recherche Full-Text avec PostgreSQL**

## **Exercice 1 : Préparation de l'environnement et des données**

1. **Création de la base de données :**

```sql
CREATE DATABASE actualites_db;
```

2. **Création de la table `articles` :**

```sql
CREATE TABLE articles (
    id SERIAL PRIMARY KEY,
    titre VARCHAR(255) NOT NULL,
    contenu TEXT NOT NULL,
    auteur VARCHAR(100),
    date_publication DATE
);
```

3. **Insertion de données d'exemple :**

```sql
INSERT INTO articles (titre, contenu, auteur, date_publication) VALUES
('Intelligence artificielle et travail', 'L''intelligence artificielle révolutionne le monde du travail. Les bases de données SQL restent fondamentales pour stocker ces informations.', 'Alice Dupont', '2026-02-01'),
('Développement web moderne', 'Le développement web moderne utilise souvent des frameworks JavaScript. La performance des requêtes SQL est essentielle pour une bonne expérience utilisateur.', 'Bob Martin', '2026-01-20'),
('Cloud computing et sécurité', 'Le cloud computing offre flexibilité et scalabilité. La sécurité des données est un enjeu majeur.', 'Carla Rossi', '2026-01-15'),
('Optimisation SQL avancée', 'Optimiser les requêtes SQL permet de gagner en performance et d''assurer une expérience utilisateur fluide.', 'David Lee', '2026-01-25'),
('Révolution des bases de données', 'Les bases de données évoluent vers des systèmes plus intelligents avec indexation Full-Text et capacités analytiques avancées.', 'Eva Nguyen', '2026-02-05');
```

---

## **Exercice 2 : Introduction à `to_tsvector`**

1. **Expérimentation de base :**

```sql
SELECT to_tsvector('french', 'Ceci est un exemple de texte pour la recherche full-text en français.');
```

* Résultat : les mots vides comme "est", "un", "de" sont supprimés. Les mots sont ramenés à leur racine (“stemming”), par exemple “texte” reste “texte”, “recherche” reste “recherch”.
* Changer la configuration (`'english'`, `'spanish'`) adapte la normalisation et les stop-words à la langue choisie.

2. **Application aux données :**

```sql
SELECT id, titre, to_tsvector('french', titre || ' ' || contenu) AS tsv
FROM articles;
```

* **Importance de la configuration linguistique :** pour un français correct, il faut `'french'` afin que les mots soient correctement stemmés et que les stop-words français soient ignorés.

---

## **Exercice 3 : Colonne générée et index GIN**

1. **Ajout d'une colonne `tsv_document` :**

```sql
ALTER TABLE articles
ADD COLUMN tsv_document TSVECTOR
GENERATED ALWAYS AS (
    setweight(to_tsvector('french', titre), 'A') ||
    setweight(to_tsvector('french', contenu), 'B')
) STORED;
```

2. **Création d'un index GIN :**

```sql
CREATE INDEX idx_articles_tsv ON articles USING GIN (tsv_document);
```

* **Question :** Sans index GIN, la recherche Full-Text sur de grandes tables serait très lente, car PostgreSQL devrait scanner toutes les lignes et calculer `to_tsvector` à la volée.

---

## **Exercice 4 : Utilisation de `to_tsquery`**

1. **Expérimentation de base :**

```sql
SELECT to_tsquery('french', 'recherche & texte'); -- mots "recherche" ET "texte"
SELECT to_tsquery('french', 'intelligence | IA');  -- "intelligence" OU "IA"
SELECT to_tsquery('french', 'base & !données');    -- "base" mais PAS "données"
SELECT to_tsquery('french', 'développement:*');    -- préfixe, match "développement", "développements", etc.
```

2. **Recherche d'articles :**

```sql
-- Articles contenant "intelligence" ET "artificielle"
SELECT titre FROM articles
WHERE tsv_document @@ to_tsquery('french', 'intelligence & artificielle');

-- Articles contenant "SQL" OU "PostgreSQL"
SELECT titre FROM articles
WHERE tsv_document @@ to_tsquery('french', 'SQL | PostgreSQL');

-- Articles contenant "développement" mais PAS "web"
SELECT titre FROM articles
WHERE tsv_document @@ to_tsquery('french', 'développement & !web');

-- Articles avec mots commençant par "révolu"
SELECT titre FROM articles
WHERE tsv_document @@ to_tsquery('french', 'révolu:*');
```

---

## **Exercice 5 : Pertinence et présentation**

1. **Classement des résultats :**

```sql
SELECT
    titre,
    ts_rank(tsv_document, to_tsquery('french', 'SQL & base')) AS score
FROM articles
WHERE tsv_document @@ to_tsquery('french', 'SQL & base')
ORDER BY score DESC;
```

* Les poids `'A'` (titre) et `'B'` (contenu) influencent le score : les mots trouvés dans le titre augmentent davantage la pertinence.

2. **Mise en évidence des mots-clés :**

```sql
SELECT
    titre,
    ts_headline('french', contenu, to_tsquery('french', 'intelligence & artificielle')) AS extrait
FROM articles
WHERE tsv_document @@ to_tsquery('french', 'intelligence & artificielle');
```

* `ts_headline` permet de montrer les occurrences des mots recherchés avec mise en forme, améliorant la lisibilité pour l’utilisateur.

---

## **Exercice 6 : Requêtes avancées (optionnel)**

1. **Recherche de phrase exacte :**

```sql
SELECT titre FROM articles
WHERE tsv_document @@ to_tsquery('french', 'base <-> données');
```

* `<->` impose que "base" soit immédiatement suivi de "données".

2. **Recherche pondérée avancée :**

```sql
SELECT titre FROM articles
WHERE tsv_document @@
      (setweight(to_tsquery('french', 'performance'), 'A') &&
       setweight(to_tsquery('french', 'optimisation'), 'B'));
```

3. **Combinaison complexe :**

```sql
SELECT titre FROM articles
WHERE tsv_document @@ to_tsquery('french', '(cloud | serveur) & sécurité & titre:sécurité');
```

* Combine plusieurs conditions logiques et pondérations pour des recherches précises.


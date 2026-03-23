# Correction – TP Recherche Textuelle Avancée 

Cette correction est rédigée pour **PostgreSQL**, en s’appuyant sur l’extension `pg_trgm` et les opérateurs natifs de recherche par expressions régulières.

# Partie 1 – Préparation de l’environnement

## 1. Création de la base et de la table

```sql
CREATE DATABASE tp_recherche_textuelle;

\c tp_recherche_textuelle;

CREATE TABLE documents (
    id SERIAL PRIMARY KEY,
    titre VARCHAR(255),
    contenu TEXT
);
```

## 2. Insertion des données

Insertion conforme à l’énoncé (au moins 10–15 lignes).

## 3. Activation de l’extension trigrammes

```sql
CREATE EXTENSION IF NOT EXISTS pg_trgm;
```

Vérification :

```sql
SELECT extname FROM pg_extension WHERE extname = 'pg_trgm';
```

---

# Partie 2 – Recherche avec LIKE

## 1. Recherche partielle classique

### a) Titre contenant "développement"

```sql
SELECT *
FROM documents
WHERE titre ILIKE '%développement%';
```

`ILIKE` permet une recherche insensible à la casse.

### b) Contenu contenant "SQL"

```sql
SELECT *
FROM documents
WHERE contenu ILIKE '%sql%';
```

---

## 2. Limites de LIKE

### a) Recherche avec faute : "programation"

```sql
SELECT *
FROM documents
WHERE contenu ILIKE '%programation%';
```

Constat :
La requête ne retourne rien si le texte contient "programmation" correctement orthographié.
`LIKE` ne gère pas la similarité, uniquement la correspondance littérale.

### b) Gérer "développement" ou "développemnt"

Il faudrait écrire :

```sql
WHERE titre ILIKE '%développement%'
   OR titre ILIKE '%développemnt%';
```

Ce n’est pas scalable, ni robuste.

Conclusion : `LIKE` est simple mais strictement littéral.

---

# Partie 3 – Recherche par trigrammes

Les trigrammes découpent une chaîne en séquences de 3 caractères et mesurent la similarité.

---

## 1. Mesure de similarité

### a) Comparaisons simples

```sql
SELECT similarity('développement', 'développemnt');
SELECT similarity('développement', 'développement web');
SELECT similarity('programmation', 'programation');
```

Résultat attendu :

* Faute légère → score élevé (souvent > 0.7)
* Ajout de mot → score intermédiaire
* Différence plus importante → score plus faible

### b) Seuil par défaut

```sql
SHOW pg_trgm.similarity_threshold;
```

Valeur par défaut : `0.3`

---

## 2. Recherche de documents similaires

### a) Titre similaire à "développemnt web"

```sql
SELECT *,
       similarity(titre, 'développemnt web') AS score
FROM documents
WHERE similarity(titre, 'développemnt web') > 0.3
ORDER BY score DESC;
```

Ou plus simplement avec l’opérateur `%` :

```sql
SELECT *
FROM documents
WHERE titre % 'développemnt web';
```

---

### b) Contenu similaire à "algorythme"

```sql
SELECT *,
       similarity(contenu, 'algorythme') AS score
FROM documents
WHERE similarity(contenu, 'algorythme') > 0.3
ORDER BY score DESC;
```

---

### c) Distance trigramme `<->` (plus petit = plus proche)

Trouver les 3 titres les plus proches de "SQL Avancé" :

```sql
SELECT *,
       titre <-> 'SQL Avancé' AS distance
FROM documents
ORDER BY distance ASC
LIMIT 3;
```

---

## 3. Optimisation avec index GIN

### a) Création de l’index

```sql
CREATE INDEX idx_titre_trgm
ON documents
USING GIN (titre gin_trgm_ops);
```

---

### b) Comparaison des performances

Avant index :

```sql
EXPLAIN ANALYZE
SELECT *
FROM documents
WHERE titre % 'développemnt web';
```

On observe :

* Seq Scan (scan complet)
* Coût plus élevé

Après index :

* Utilisation de Bitmap Index Scan ou Index Scan
* Temps d’exécution nettement réduit sur grande volumétrie

Conclusion :
L’index GIN est indispensable pour des recherches floues performantes.

---

# Partie 4 – Expressions régulières

PostgreSQL fournit :

* `~`  : sensible à la casse
* `~*` : insensible à la casse

---

## 1. Recherche de mots spécifiques

### a) "SQL" insensible à la casse

```sql
SELECT *
FROM documents
WHERE titre ~* 'sql';
```

---

### b) Contient "Python" mais pas "Java"

```sql
SELECT *
FROM documents
WHERE contenu ~* 'python'
  AND contenu !~* 'java';
```

---

## 2. Recherche de numéros de version

Motif :

* vX.Y
* version X.Y

```sql
SELECT *
FROM documents
WHERE contenu ~* '(v[0-9]+\.[0-9]+)|(version[ ]+[0-9]+\.[0-9]+)';
```

---

## 3. Recherche d’adresses email

```sql
SELECT *
FROM documents
WHERE contenu ~ '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}';
```

---

## 4. Extraction avec REGEXP_MATCHES

### a) Extraction d’email

```sql
SELECT id,
       regexp_matches(
           contenu,
           '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}',
           'g'
       ) AS email
FROM documents;
```

Le flag `'g'` permet plusieurs correspondances.

---

### b) Extraction de version

```sql
SELECT id,
       regexp_matches(
           contenu,
           '(v[0-9]+\.[0-9]+)|(version[ ]+[0-9]+\.[0-9]+)',
           'g'
       ) AS version
FROM documents;
```

---

# Partie 5 – Réflexion et comparaison

## 1. Quand utiliser chaque technique ?

### Trigrammes

À privilégier pour :

* Correction de fautes de frappe
* Recherche floue
* Autocomplétion
* Moteur de recherche applicatif

Exemple : moteur de recherche interne.

---

### Expressions régulières

Indispensables pour :

* Validation de formats (email, téléphone, version)
* Extraction structurée
* Recherche de motifs précis

Exemple : détecter des numéros de version ou des emails.

Les trigrammes sont inefficaces pour reconnaître un motif structuré.

---

### LIKE

Suffisant pour :

* Recherche simple
* Filtrage basique
* Petit volume de données
* Pas de besoin de tolérance aux fautes

---

## 2. Performance et indexation

### Impact de l’index GIN

* Évite le scan complet
* Réduit drastiquement le temps sur grandes tables
* Adapté aux colonnes TEXT volumineuses

---

### Index pour regex ?

PostgreSQL ne possède pas d’index natif spécifique aux regex complexes.

Cependant :

* Un index B-tree peut aider si la regex commence par un préfixe fixe.
* Un index GIN trigramme peut accélérer certaines regex.

Exemple :

```sql
CREATE INDEX idx_contenu_trgm
ON documents
USING GIN (contenu gin_trgm_ops);
```

Cela peut optimiser certaines recherches regex.

---

## 3. Combinaison des techniques

Scénario :

Un moteur de recherche qui :

* Tolère les fautes de frappe
* Extrait les versions détectées
* Classe les résultats par similarité

Exemple :

```sql
SELECT id,
       titre,
       similarity(titre, 'developpemnt web') AS score,
       regexp_matches(
           contenu,
           '(v[0-9]+\.[0-9]+)|(version[ ]+[0-9]+\.[0-9]+)'
       ) AS version
FROM documents
WHERE titre % 'developpemnt web'
ORDER BY score DESC;
```

Ici :

* Les trigrammes gèrent la recherche floue.
* Les regex extraient les informations structurées.

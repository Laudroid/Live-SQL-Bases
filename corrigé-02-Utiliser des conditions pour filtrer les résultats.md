# Corrigé – TP SQL : Filtrage et Tri des Données

Ce corrigé propose une solution pour chaque exercice. Les requêtes sont compatibles avec MySQL (syntaxe utilisée dans l’énoncé), mais restent adaptables à PostgreSQL, SQL Server ou SQLite avec de légères modifications si nécessaire.

---

## Exercice 1 : Filtrage simple avec `WHERE`

### 1. Livres du genre 'Science-fiction'

```sql
SELECT *
FROM Livres
WHERE genre = 'Science-fiction';
```

---

### 2. Livres publiés après l’année 2000

```sql
SELECT *
FROM Livres
WHERE annee_publication > 2000;
```

---

### 3. Livres dont le prix est strictement inférieur à 10.00€

```sql
SELECT *
FROM Livres
WHERE prix < 10.00;
```

---

### 4. Livres dont le stock est égal à 0

```sql
SELECT *
FROM Livres
WHERE stock = 0;
```

---

## Exercice 2 : Combiner les conditions avec `AND` et `OR`

### 1. Livres du genre 'Fantasy' et publiés avant 1990

```sql
SELECT *
FROM Livres
WHERE genre = 'Fantasy'
  AND annee_publication < 1990;
```

---

### 2. Livres de 'J.R.R. Tolkien' ou 'J.K. Rowling'

```sql
SELECT *
FROM Livres
WHERE auteur = 'J.R.R. Tolkien'
   OR auteur = 'J.K. Rowling';
```

Alternative plus propre :

```sql
SELECT *
FROM Livres
WHERE auteur IN ('J.R.R. Tolkien', 'J.K. Rowling');
```

---

### 3. Livres de 'Science-fiction' ou 'Dystopie' dont le prix est supérieur à 15.00€

Attention à la priorité des opérateurs :

```sql
SELECT *
FROM Livres
WHERE (genre = 'Science-fiction' OR genre = 'Dystopie')
  AND prix > 15.00;
```

---

### 4. Livres du genre 'Roman' ou 'Historique', publiés après 1900

```sql
SELECT *
FROM Livres
WHERE (genre = 'Roman' OR genre = 'Historique')
  AND annee_publication > 1900;
```

---

## Exercice 3 : Exclure des résultats avec `NOT`

### 1. Livres qui ne sont pas du genre 'Science-fiction'

```sql
SELECT *
FROM Livres
WHERE genre <> 'Science-fiction';
```

ou

```sql
SELECT *
FROM Livres
WHERE NOT genre = 'Science-fiction';
```

---

### 2. Livres non disponibles à la vente

```sql
SELECT *
FROM Livres
WHERE disponible = FALSE;
```

---

### 3. Livres dont l’auteur n’est pas 'George Orwell' et dont le stock est supérieur à 0

```sql
SELECT *
FROM Livres
WHERE auteur <> 'George Orwell'
  AND stock > 0;
```

---

## Exercice 4 : Trier les résultats avec `ORDER BY`

### 1. Tous les livres triés par prix croissant

```sql
SELECT *
FROM Livres
ORDER BY prix ASC;
```

---

### 2. Livres du genre 'Fantasy' triés par année décroissante

```sql
SELECT *
FROM Livres
WHERE genre = 'Fantasy'
ORDER BY annee_publication DESC;
```

---

### 3. Tous les livres triés par genre puis par titre

```sql
SELECT *
FROM Livres
ORDER BY genre ASC, titre ASC;
```

---

### 4. Livres dont le prix est entre 10.00€ et 20.00€, triés par auteur puis par prix décroissant

```sql
SELECT *
FROM Livres
WHERE prix BETWEEN 10.00 AND 20.00
ORDER BY auteur ASC, prix DESC;
```

---

## Exercice 5 : Combinaison avancée

### 1. Livres 'Science-fiction' ou 'Fantasy', publiés après 1980, stock > 50, triés par titre

```sql
SELECT *
FROM Livres
WHERE genre IN ('Science-fiction', 'Fantasy')
  AND annee_publication > 1980
  AND stock > 50
ORDER BY titre ASC;
```

---

### 2. Livres non 'Essai', prix < 25.00€, disponibles, triés par genre puis prix décroissant

```sql
SELECT *
FROM Livres
WHERE genre <> 'Essai'
  AND prix < 25.00
  AND disponible = TRUE
ORDER BY genre ASC, prix DESC;
```

---

### 3. Livres de 'George Orwell' ou 'Jane Austen', publiés avant 2000, triés par année croissante

```sql
SELECT *
FROM Livres
WHERE auteur IN ('George Orwell', 'Jane Austen')
  AND annee_publication < 2000
ORDER BY annee_publication ASC;
```

---

# Pour aller plus loin (Optionnel)

### 1. Recherche partielle : titre contenant "Prince"

```sql
SELECT *
FROM Livres
WHERE titre LIKE '%Prince%';
```

---

### 2. Nombre de livres de 'Science-fiction' publiés après 2000

```sql
SELECT COUNT(*) AS nombre_livres
FROM Livres
WHERE genre = 'Science-fiction'
  AND annee_publication > 2000;
```

---

### 3. Requête avancée avec sous-requête

Livres de 'Roman' ou 'Historique', publiés après 1950, dont le prix est supérieur à la moyenne de ces genres :

```sql
SELECT *
FROM Livres
WHERE genre IN ('Roman', 'Historique')
  AND annee_publication > 1950
  AND prix > (
      SELECT AVG(prix)
      FROM Livres
      WHERE genre IN ('Roman', 'Historique')
  );
```

---

## Remarques pédagogiques

* Toujours utiliser des parenthèses lorsque `AND` et `OR` sont combinés.
* `IN` permet d’alléger les conditions multiples sur une même colonne.
* `BETWEEN` inclut les bornes.
* `ORDER BY` peut trier sur plusieurs colonnes dans l’ordre indiqué.
* `COUNT()`, `AVG()` et les sous-requêtes permettent d’introduire progressivement des requêtes plus complexes.



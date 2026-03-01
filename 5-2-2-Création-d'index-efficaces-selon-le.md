# 5-Index & performance  
## 2-Optimisation des requêtes  
### 2-Création d'index efficaces selon les requêtes

---

Créer un index n’est pas forcément synonyme d’amélioration automatique des performances. La clé réside dans le **choix pertinent des colonnes à indexer**, la structure de l’index, et l’adéquation avec les requêtes qui seront exécutées. Cet article détaille les bonnes pratiques pour construire des index efficaces adaptés aux usages.

---

## 1. Comprendre la nature des requêtes

Avant toute création d’index, analyser les requêtes typiques est indispensable :

- Quelles colonnes sont utilisées dans les clauses `WHERE`, `JOIN`, `ORDER BY`, `GROUP BY` ?
- Quels types d’opérations (égalité, intervalle, préfixe) sont effectués ?
- Quelle est la sélectivité des colonnes (proportion de valeurs distinctes) ?

---

## 2. Choix et types d’index selon les cas

### 2.1 Index simple sur colonne(s) avec conditions fréquentes

Exemple : Requêtes filtrant souvent un nom d’employé

```sql
CREATE INDEX idx_nom ON Employe(nom);
```

### 2.2 Index composite (multi-colonnes)

Utile lorsque les requêtes filtrent sur plusieurs colonnes simultanément. Attention à l’ordre des colonnes dans l’index : l’ordre doit correspondre à celui utilisé dans la requête.

Exemple : Requête avec filtre sur `(departement_id, salaire)`

```sql
CREATE INDEX idx_dept_salaire ON Employe(departement_id, salaire);
```

---

## 3. Index couvrant (covering index)

Un index contenant toutes les colonnes nécessaires pour une requête évite d’aller lire la table (évite le "look-up") :

Exemple : pour la requête suivante

```sql
SELECT nom, salaire
FROM Employe
WHERE departement_id = 10;
```

Créer index couvrant :

```sql
CREATE INDEX idx_cover ON Employe(departement_id, nom, salaire);
```

---

## 4. Index adaptés aux types d’opérations

| Type d'opération       | Type d'index conseillé                   | Exemple                                    |
|-----------------------|-----------------------------------------|--------------------------------------------|
| Égalité (=)            | B-tree ou hash (selon SGBD)              | `CREATE INDEX ON Employe(nom);`            |
| Recherches par intervalle (`BETWEEN`, `<`, `>`) | B-tree                                   | `CREATE INDEX ON Employe(date_embauche);`  |
| Recherches full-text   | Index full-text (ex : GIN, FT indexes)   | `CREATE INDEX idx_texte ON Article USING GIN(to_tsvector('fr', contenu));` |
| Requêtes géographiques | Index spatial (ex : R-tree)               | `CREATE INDEX ON Lieux USING GIST(geom);`  |

---

## 5. Impact du cardinalité et sélectivité

- **Haute sélectivité** (bcp de valeurs distinctes) : index très efficace  
- **Basse sélectivité** (faible nombre de valeurs distinctes) : index peu profitable, voire pénalisant (ex : colonne booléenne)  

---

## 6. Exemple d’optimisation via index

Requête initiale :

```sql
SELECT * FROM Employe WHERE sexe = 'F' AND departement_id = 4;
```

- Index simple sur `sexe` peu efficace (2 valeurs possibles)  
- Meilleur choix : index composite sur `(departement_id, sexe)`

```sql
CREATE INDEX idx_dept_sexe ON Employe(departement_id, sexe);
```

---

## 7. Diagramme Mermaid – Création d’un index composite

```mermaid
graph LR
    A[Requête avec WHERE dept_id et sexe]
    B[Index composite (dept_id, sexe)]
    C[Accès rapide aux lignes correspondantes]

    A --> B --> C
```

---

## 8. Conseils supplémentaires

- Éviter la surabondance d’index (impact sur insertions/mises à jour).  
- Prioriser les colonnes souvent filtrées, triées ou jointes.  
- Mettre à jour les statistiques des tables pour optimiser les plans d’exécution.  
- Tester les performances avant/après création avec `EXPLAIN` ou outils de profilage.

---

## 9. Sources utilisées

- PostgreSQL Documentation, [Indexing Strategies](https://www.postgresql.org/docs/current/indexes-types.html)  
- Oracle Docs, [Choosing an Index](https://docs.oracle.com/en/database/oracle/oracle-database/19/admin/choosing-an-index.html)  
- SQL Server Docs, [Designing Indexes](https://docs.microsoft.com/en-us/sql/relational-databases/sql-server-index-design-guide)  
- Use The Index, Luke!, [Indexing Strategically](https://use-the-index-luke.com/sql/where-clause/index-selection)

---

Adapter la création d’index aux besoins spécifiques des requêtes garantit de tirer parti au maximum des performances des bases relationnelles. Comprendre les caractéristiques des colonnes et la nature des accès orientera toujours vers des index pertinents et mesurables.
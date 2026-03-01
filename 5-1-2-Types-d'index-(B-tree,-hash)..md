# 5-Index & performance  
## 1-Introduction aux index  
### 2-Types d'index (B-tree, hash)

---

Les **index** dans une base relationnelle existent sous plusieurs formes adaptées à différents types de requêtes et volumes de données. Parmi les types les plus courants, on distingue les **index B-tree** et les **index hash**. Chacun possède ses propres mécanismes et cas d’usage.

---

## 1. Index B-tree (Balanced Tree)

### Description

- Structure en arbre équilibré, souvent un **arbre B+**, qui maintient les données triées.
- Permet un accès rapide en O(log n) pour la recherche, l’insertion, la suppression.
- Gère efficacement les recherches d'égalité et les recherches par intervalle (ex : `WHERE age BETWEEN 20 AND 30`).
- Utilisé par défaut dans la majorité des SGBD relationnels (PostgreSQL, MySQL, Oracle, SQL Server).

### Exemple de création d’un index B-tree (valeur par défaut)

```sql
CREATE INDEX idx_nom ON Employe(nom);
```

Cet index accélère les requêtes :

```sql
SELECT * FROM Employe WHERE nom = 'Durand';
SELECT * FROM Employe WHERE nom BETWEEN 'A' AND 'M';
```

### Avantages

- Polyvalent, adapté pour des recherches complexes.
- Permet aussi les tris rapides (`ORDER BY`).
- Supporte les jointures sur colonnes indexées.

---

## 2. Index Hash

### Description

- Index basé sur une fonction de hachage qui transforme la valeur de la clé en un numéro de "bucket".
- Optimisé pour la recherche d’égalité (`=`), recherche très rapide en moyenne.
- Ne supporte pas les recherches par intervalle, ni les tris.
- Supporté par certains SGBD comme PostgreSQL (optionnel), rarement dans MySQL.

### Exemple de création d’index hash sur PostgreSQL

```sql
CREATE INDEX idx_hash ON Employe USING hash(nom);
```

### Utilisation typique

- Requêtes très fréquentes avec égalité stricte, ex: `WHERE id = 42`.
- Non adapté aux recherches de plages, inégalités, tris.

---

## 3. Comparaison simplifiée

| Critère                 | B-tree                   | Hash                    |
|-------------------------|--------------------------|-------------------------|
| Recherches supportées   | Égalité & intervalles    | Égalité uniquement      |
| Performances            | Bonnes en général        | Très rapides en égalité |
| Tri & ordre             | Oui                      | Non                     |
| Support dans SGBD       | Très répandu             | Partiel (PostgreSQL)    |

---

## 4. Diagramme Mermaid — vue conceptuelle

```mermaid
graph LR
    A[Recherche d'égalité] --> B[Utilise B-tree ou Hash]
    C[Recherche par intervalle] --> D[Utilise B-tree uniquement]

    subgraph Index B-tree
        B1[Recherche rapide O(log n)]
        B2[Tri et plage supportés]
    end

    subgraph Index Hash
        H1[Recherche rapide en moyenne O(1)]
        H2[Uniquement égalité]
    end

    B -->|polyvalent| B1
    B --> B2
    B -->|égalite simple| H1
    B --> H2
```

---

## 5. Sources utilisées

- PostgreSQL Documentation, [Index Types](https://www.postgresql.org/docs/current/indexes-types.html)  
- Oracle Docs, [Index Types](https://docs.oracle.com/en/database/oracle/oracle-database/19/admin/indexes-and-index-organized-tables.html#GUID-67AA4A81-910B-4EA7-8F53-A266F1EEED55)  
- SQL Server Docs, [Index Architecture Guide](https://docs.microsoft.com/en-us/sql/relational-databases/indexes/sql-server-index-design-guide)  
- W3Schools, [SQL CREATE INDEX](https://www.w3schools.com/sql/sql_create_index.asp)

---

Les index B-tree sont la norme polyvalente dans les bases relationnelles, capables d’optimiser une large gamme de requêtes, tandis que les index hash s’avèrent très rapides pour des recherches d’égalité spécifiques. Comprendre leurs différences aide à choisir la bonne structure selon les besoins applicatifs.
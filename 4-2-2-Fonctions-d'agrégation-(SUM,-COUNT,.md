# 4-Jointures & requêtes complexes  
## 2-Requêtes imbriquées et agrégations  
### 2-Fonctions d’agrégation (SUM, COUNT, AVG)

---

Les fonctions d’agrégation sont utilisées pour effectuer des calculs sur un ensemble de lignes et retourner une valeur unique représentative. Elles permettent de synthétiser des données numériques ou de compter des éléments dans une table.

---

## 1. Principales fonctions d’agrégation

| Fonction | Description                              | Exemple                                                   |
|----------|------------------------------------------|-----------------------------------------------------------|
| `SUM()`  | Calcule la somme des valeurs numériques | Total des ventes, salaires, quantité, etc.                |
| `COUNT()`| Compte le nombre d’enregistrements ou valeurs non nulles | Nombre d’employés, d’articles, etc.      |
| `AVG()`  | Calcule la moyenne des valeurs numériques | Salaire moyen, moyenne des notes, etc.                     |

---

## 2. Détails et comportements

- Ces fonctions ignorent par défaut les valeurs `NULL`.
- Elles sont souvent utilisées avec `GROUP BY` pour effectuer un calcul par groupe.
- `COUNT(*)` compte toutes les lignes, y compris celles avec valeurs `NULL`.
- Les résultats sont scalaires, c’est-à-dire une seule valeur par groupe ou par requête.

---

## 3. Exemples pratiques

### 3.1 Calcul de la somme des salaires

```sql
SELECT SUM(salaire) AS total_salaire
FROM Employe;
```

**Résultat :**

| total_salaire |  
|--------------|  
| 240000       |

---

### 3.2 Compter le nombre d’employés par département

```sql
SELECT id_departement, COUNT(*) AS nb_employes
FROM Employe
GROUP BY id_departement;
```

**Résultat :**

| id_departement | nb_employes |  
|----------------|-------------|  
| 10             | 5           |  
| 20             | 8           |  

---

### 3.3 Moyenne des salaires par département

```sql
SELECT id_departement, AVG(salaire) AS salaire_moyen
FROM Employe
GROUP BY id_departement;
```

**Résultat :**

| id_departement | salaire_moyen |  
|----------------|---------------|  
| 10             | 48000         |  
| 20             | 53000         |

---

## 4. Utilisation et combinaison avancée

On peut combiner plusieurs fonctions dans une requête pour obtenir différents agrégats simultanément.

```sql
SELECT id_departement, 
       COUNT(*) AS nb_employes, 
       SUM(salaire) AS masse_salariale, 
       AVG(salaire) AS salaire_moyen
FROM Employe
GROUP BY id_departement;
```

---

## 5. Illustration Mermaid – Agrégation par groupes

```mermaid
graph TD
    Table(Employe)
    GroupBy[GROUP BY id_departement]
    SUM[SUM(salaire)]
    COUNT[COUNT(*)]
    AVG[AVG(salaire)]
    Result[Résultat agrégé]

    Table --> GroupBy --> SUM & COUNT & AVG --> Result
```

---

## 6. Sources utilisées

- W3Schools, [SQL Aggregate Functions](https://www.w3schools.com/sql/sql_count_avg_sum.asp)  
- PostgreSQL Documentation, [Aggregate Functions](https://www.postgresql.org/docs/current/functions-aggregate.html)  
- Oracle Docs, [Aggregate Functions](https://docs.oracle.com/cd/B19306_01/server.102/b14200/functions004.htm)  
- SQL Server Docs, [Aggregate Functions (Transact-SQL)](https://docs.microsoft.com/en-us/sql/t-sql/functions/aggregate-functions-transact-sql)

---

Les fonctions d’agrégation `SUM`, `COUNT` et `AVG` simplifient le traitement de données numériques dans SQL, en permettant de synthétiser l’information selon des critères précis. Leur maîtrise est indispensable pour construire des rapports et analyses efficaces.
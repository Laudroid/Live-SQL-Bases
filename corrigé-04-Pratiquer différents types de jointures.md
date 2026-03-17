# Correction du TP – Maîtrise des jointures SQL (PostgreSQL)

# 1. Rappels importants pour PostgreSQL

* PostgreSQL supporte nativement :

  * `INNER JOIN`
  * `LEFT JOIN`
  * `RIGHT JOIN`
  * `FULL OUTER JOIN`
* Le mot-clé `OUTER` est optionnel.
* Les alias de table sont fortement recommandés pour améliorer la lisibilité.

Convention utilisée dans la correction :

* `d` → departments
* `e` → employees
* `p` → projects

---

# 2. Correction des exercices

---

## 2.1 INNER JOIN

L’`INNER JOIN` retourne uniquement les lignes ayant une correspondance dans les deux tables.

---

### 2.1.1 Employés et leurs départements

```sql
SELECT 
    e.first_name,
    e.last_name,
    d.department_name
FROM employees e
INNER JOIN departments d 
    ON e.department_id = d.department_id;
```

Analyse :

* L’employé Eve n’apparaît pas car `department_id` est NULL.
* Tous les employés ayant un département sont affichés.

---

### 2.1.2 Projets et employés assignés

```sql
SELECT 
    p.project_name,
    e.first_name,
    e.last_name
FROM projects p
INNER JOIN employees e 
    ON p.assigned_employee_id = e.employee_id;
```

Analyse :

* Les projets sans employé assigné (Audit Sécurité, Étude de Marché) n’apparaissent pas.
* Seuls les projets avec correspondance sont affichés.

---

## 2.2 LEFT JOIN

Le `LEFT JOIN` retourne toutes les lignes de la table de gauche.

---

### 2.2.1 Tous les départements et leurs employés

```sql
SELECT 
    d.department_name,
    e.first_name,
    e.last_name
FROM departments d
LEFT JOIN employees e 
    ON d.department_id = e.department_id
ORDER BY d.department_name;
```

Analyse :

* Tous les départements apparaissent.
* Si un département n’a aucun employé, les colonnes `first_name` et `last_name` sont NULL.
* Avec les données fournies, tous les départements ont au moins un employé.

---

### 2.2.2 Tous les employés et leurs projets

```sql
SELECT 
    e.first_name,
    e.last_name,
    p.project_name
FROM employees e
LEFT JOIN projects p 
    ON e.employee_id = p.assigned_employee_id
ORDER BY e.employee_id;
```

Analyse :

* Tous les employés apparaissent.
* Eve apparaît avec `project_name = NULL`.
* Bob apparaît plusieurs fois car il a deux projets.

---

## 2.3 RIGHT JOIN

Le `RIGHT JOIN` retourne toutes les lignes de la table de droite.

---

### 2.3.1 Employés et tous les départements (perspective inverse)

```sql
SELECT 
    e.first_name,
    e.last_name,
    d.department_name
FROM employees e
RIGHT JOIN departments d 
    ON e.department_id = d.department_id
ORDER BY d.department_name;
```

Analyse :

* Tous les départements sont affichés.
* Les départements sans employés auraient des valeurs NULL côté employé.
* Résultat équivalent au LEFT JOIN précédent en inversant les tables.

---

### 2.3.2 Projets et tous les employés

```sql
SELECT 
    p.project_name,
    e.first_name,
    e.last_name
FROM projects p
RIGHT JOIN employees e 
    ON p.assigned_employee_id = e.employee_id
ORDER BY e.employee_id;
```

Analyse :

* Tous les employés apparaissent.
* Les employés sans projet ont `project_name = NULL`.
* Résultat équivalent au LEFT JOIN précédent avec inversion.

---

## 2.4 FULL OUTER JOIN

PostgreSQL supporte directement `FULL OUTER JOIN`.

---

### 2.4.1 Tous les départements et tous les employés

```sql
SELECT 
    d.department_name,
    e.first_name,
    e.last_name
FROM departments d
FULL OUTER JOIN employees e 
    ON d.department_id = e.department_id
ORDER BY d.department_name;
```

Résultat attendu :

Inclut :

* Les employés avec département
* Les employés sans département (Eve → department_name NULL)
* Les départements sans employé (si existants → first_name NULL)

---

### 2.4.2 Tous les projets et tous les employés

```sql
SELECT 
    p.project_name,
    e.first_name,
    e.last_name
FROM projects p
FULL OUTER JOIN employees e 
    ON p.assigned_employee_id = e.employee_id
ORDER BY p.project_name;
```

Résultat attendu :

Inclut :

* Projets avec employé
* Projets sans employé (first_name NULL)
* Employés sans projet (project_name NULL)

---

# 3. Analyse et réflexion

---

## 3.1 Compréhension des NULL

Les `NULL` apparaissent lorsqu’aucune correspondance n’existe :

### LEFT JOIN

* Si aucune correspondance à droite → colonnes de droite à NULL.

Exemple :

* Eve n’a pas de projet → `project_name = NULL`.

### RIGHT JOIN

* Si aucune correspondance à gauche → colonnes de gauche à NULL.

### FULL OUTER JOIN

* Combine les deux comportements :

  * Employé sans département → colonnes département NULL.
  * Projet sans employé → colonnes employé NULL.

Important en PostgreSQL :

* `NULL` signifie absence de valeur.
* `NULL = NULL` est faux.
* Il faut utiliser `IS NULL` pour tester.

---

## 3.2 Comparaison des jointures

### INNER JOIN

Intersection stricte.
Utilisation :

* Quand on veut uniquement des données cohérentes des deux côtés.
* Cas métier : liste des employés réellement rattachés à un département.

---

### LEFT JOIN

Priorité à la table de gauche.
Utilisation :

* Quand la table de gauche représente l’entité principale.
* Exemple : afficher tous les employés, même sans projet.

---

### RIGHT JOIN

Symétrique du LEFT JOIN.
En pratique, on préfère souvent inverser les tables et utiliser LEFT JOIN pour plus de lisibilité.

---

### FULL OUTER JOIN

Union complète.
Utilisation :

* Audit de cohérence.
* Recherche d’incohérences.
* Vérification des correspondances manquantes.

---

## 3.3 Scénarios avancés

---

### Employés sans département

```sql
SELECT *
FROM employees
WHERE department_id IS NULL;
```

Alternative avec LEFT JOIN :

```sql
SELECT e.*
FROM employees e
LEFT JOIN departments d 
    ON e.department_id = d.department_id
WHERE d.department_id IS NULL;
```

---

### Départements sans employé

```sql
SELECT d.*
FROM departments d
LEFT JOIN employees e 
    ON d.department_id = e.department_id
WHERE e.employee_id IS NULL;
```

Ce type de requête est très utilisé pour détecter :

* Données orphelines
* Problèmes d’intégrité
* Données inutilisées


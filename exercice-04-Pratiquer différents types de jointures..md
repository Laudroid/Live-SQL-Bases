Bonjour !

Ce TP est conçu pour vous permettre de manipuler les différents types de jointures en SQL. L'objectif est de solidifier votre compréhension de la manière dont les tables peuvent être combinées pour extraire des informations pertinentes.

---

### **Travaux Pratiques : Maîtrise des Jointures SQL**

**Objectif du TP :** Pratiquer différents types de jointures (INNER JOIN, LEFT JOIN, RIGHT JOIN, FULL JOIN) et comprendre leurs nuances.

**Prérequis :**
*   Connaissances de base en SQL (SELECT, FROM, WHERE).
*   Un environnement de base de données (par exemple, MySQL, PostgreSQL, SQL Server, SQLite) et un client SQL pour exécuter les requêtes.

---

### **1. Contexte et Mise en place**

Nous allons travailler avec une base de données simple représentant des employés, des départements et des projets. Certaines données ont été volontairement conçues pour illustrer les cas où des correspondances sont manquantes, ce qui est essentiel pour comprendre les jointures externes.

**Script de création et insertion des données :**

Exécutez les requêtes DDL (Data Definition Language) et DML (Data Manipulation Language) suivantes dans votre environnement SQL pour créer les tables et les remplir.

```sql
-- Création de la table DEPARTMENTS
CREATE TABLE DEPARTMENTS (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(50) NOT NULL,
    location VARCHAR(50)
);

-- Création de la table EMPLOYEES
CREATE TABLE EMPLOYEES (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    department_id INT,
    salary DECIMAL(10, 2),
    hire_date DATE,
    FOREIGN KEY (department_id) REFERENCES DEPARTMENTS(department_id)
);

-- Création de la table PROJECTS
CREATE TABLE PROJECTS (
    project_id INT PRIMARY KEY,
    project_name VARCHAR(100) NOT NULL,
    assigned_employee_id INT,
    status VARCHAR(20),
    FOREIGN KEY (assigned_employee_id) REFERENCES EMPLOYEES(employee_id)
);

-- Insertion des données dans DEPARTMENTS
INSERT INTO DEPARTMENTS (department_id, department_name, location) VALUES
(10, 'Ressources Humaines', 'Paris'),
(20, 'Informatique', 'Lyon'),
(30, 'Marketing', 'Paris'),
(40, 'Ventes', 'Marseille'),
(50, 'Recherche & Développement', 'Toulouse');

-- Insertion des données dans EMPLOYEES
INSERT INTO EMPLOYEES (employee_id, first_name, last_name, department_id, salary, hire_date) VALUES
(1, 'Alice', 'Dupont', 10, 60000.00, '2020-01-15'),
(2, 'Bob', 'Martin', 20, 75000.00, '2019-03-20'),
(3, 'Charlie', 'Bernard', 20, 70000.00, '2021-06-01'),
(4, 'David', 'Petit', 30, 55000.00, '2022-02-10'),
(5, 'Eve', 'Durand', NULL, 50000.00, '2023-09-01'), -- Employé sans département
(6, 'Frank', 'Leroy', 10, 62000.00, '2021-11-05');

-- Insertion des données dans PROJECTS
INSERT INTO PROJECTS (project_id, project_name, assigned_employee_id, status) VALUES
(101, 'Refonte Site Web', 2, 'En cours'),
(102, 'Portail RH', 1, 'Terminé'),
(103, 'Campagne Marketing Hiver', 4, 'Planifié'),
(104, 'Audit Sécurité', NULL, 'En attente'), -- Projet sans employé assigné
(105, 'Développement App Mobile', 2, 'En cours'),
(106, 'Etude de Marché', NULL, 'Planifié'); -- Autre projet sans employé assigné
```

---

### **2. Exercices Pratiques**

Pour chaque type de jointure, écrivez la requête SQL demandée. Observez attentivement les résultats, en particulier les lignes avec des valeurs `NULL`.

#### **2.1. INNER JOIN**

L'INNER JOIN retourne uniquement les lignes lorsque la condition de jointure est vraie dans *les deux* tables.

1.  **Employés et leurs départements :**
    Affichez le prénom, le nom de chaque employé et le nom de leur département.
2.  **Projets et employés assignés :**
    Affichez le nom de chaque projet et le prénom et nom de l'employé qui lui est assigné.

#### **2.2. LEFT JOIN (ou LEFT OUTER JOIN)**

Le LEFT JOIN retourne toutes les lignes de la table de gauche, et les lignes correspondantes de la table de droite. Si aucune correspondance n'est trouvée dans la table de droite, des `NULL` apparaissent pour les colonnes de la table de droite.

1.  **Tous les départements et leurs employés :**
    Affichez le nom de chaque département et, s'ils existent, le prénom et le nom des employés qui y travaillent. Incluez les départements qui n'ont pas encore d'employé.
2.  **Tous les employés et leurs projets :**
    Affichez le prénom, le nom de chaque employé et, s'il en a un, le nom du projet auquel il est assigné. Incluez les employés qui n'ont pas de projet.

#### **2.3. RIGHT JOIN (ou RIGHT OUTER JOIN)**

Le RIGHT JOIN est l'inverse du LEFT JOIN. Il retourne toutes les lignes de la table de droite, et les lignes correspondantes de la table de gauche. Si aucune correspondance n'est trouvée dans la table de gauche, des `NULL` apparaissent pour les colonnes de la table de gauche.

1.  **Employés et tous les départements (perspective inverse) :**
    Affichez le prénom, le nom de chaque employé et le nom de leur département. Assurez-vous d'inclure tous les départements, même ceux qui n'ont pas d'employé. (Comparez le résultat avec la question 2.2.1).
2.  **Projets et tous les employés (perspective inverse) :**
    Affichez le nom de chaque projet et le prénom et nom de l'employé qui lui est assigné. Assurez-vous d'inclure tous les employés, même ceux qui n'ont pas de projet. (Comparez le résultat avec la question 2.2.2).

#### **2.4. FULL JOIN (ou FULL OUTER JOIN)**

Le FULL JOIN retourne toutes les lignes lorsqu'il y a une correspondance dans l'une ou l'autre des tables. Si aucune correspondance n'est trouvée, des `NULL` apparaissent pour les colonnes de la table où il n'y a pas de correspondance.

**Attention :** MySQL ne supporte pas nativement le `FULL OUTER JOIN`. Si vous utilisez MySQL, vous devrez le simuler en combinant un `LEFT JOIN` et un `RIGHT JOIN` avec un `UNION`.

1.  **Tous les départements et tous les employés :**
    Affichez le nom de chaque département et le prénom et nom de chaque employé. Le résultat doit inclure :
    *   Les employés avec un département.
    *   Les employés sans département.
    *   Les départements sans employé.
2.  **Tous les projets et tous les employés :**
    Affichez le nom de chaque projet et le prénom et nom de chaque employé. Le résultat doit inclure :
    *   Les projets avec un employé assigné.
    *   Les projets sans employé assigné.
    *   Les employés sans projet assigné.

---

### **3. Analyse et Réflexion**

Dans cette section, nous allons explorer comment l'utilisation d'outils d'IA peut enrichir votre apprentissage, tout en vous encourageant à développer un esprit critique.

1.  **Compréhension des `NULL` :**
    *   Dans les résultats de vos requêtes `LEFT JOIN` et `RIGHT JOIN`, identifiez les lignes où des `NULL` apparaissent. Expliquez pourquoi ces `NULL` sont présents pour chaque cas.
    *   Comment les `NULL` se manifestent-ils dans le résultat de votre `FULL JOIN` simulé ou natif ?

2.  **Comparaison des jointures :**
    *   Expliquez avec vos propres mots la différence fondamentale entre `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN` et `FULL JOIN`. Quand utiliseriez-vous chaque type de jointure ?
    *   Si vous avez utilisé une IA pour générer certaines de vos requêtes, demandez-lui d'expliquer la logique derrière un `LEFT JOIN` par rapport à un `INNER JOIN` en utilisant un exemple simple. Évaluez la clarté et la justesse de son explication.

3.  **Scénario avancé (optionnel) :**
    *   Comment écririez-vous une requête pour trouver *uniquement* les employés qui n'ont pas de département assigné ?
    *   Comment écririez-vous une requête pour trouver *uniquement* les départements qui n'ont aucun employé ?

---

N'hésitez pas à expérimenter avec les requêtes, à modifier les conditions, et à observer les changements dans les résultats. C'est en manipulant que l'on comprend le mieux !

Amusez-vous bien avec les jointures !
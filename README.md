# Cours SQL Bases

## Objectifs de la formation

- Manipuler des données relationnelles.
- Interroger une base avec SQL.
- Optimiser l’accès aux données.
- Comprendre les limites du SQL “classique”.
- Se préparer à l’usage du SQL en environnement pro (API, back-end).
## Séance 1 : Introduction aux bases de données relationnelles

### Objectifs pédagogiques

- Comprendre le concept et l'importance des bases de données relationnelles.
- Découvrir PostgreSQL comme SGBD de référence.
### Contenus

#### Concepts fondamentaux des bases relationnelles

- Définition d'une base de données relationnelle.
- Modèle relationnel : tables, lignes, colonnes.
- Clés primaires et étrangères.
#### Présentation de PostgreSQL

- Installation et configuration de base.
- Syntaxe de base de PostgreSQL.
## Séance 2 : Requêtes SQL fondamentales

### Objectifs pédagogiques

- Maîtriser les commandes SELECT, INSERT, UPDATE, DELETE.
- Commencer à manipuler les données dans PostgreSQL.
### Contenus

#### Les commandes de base SQL

- Syntaxe SELECT pour interroger une table.
- Ajout de données avec INSERT.
- Modification de données avec UPDATE.
- Suppression de données avec DELETE.
#### Conditions et filtres

- Utilisation de WHERE pour filtrer les résultats.
- Opérateurs logiques (AND, OR, NOT).
- Tri avec ORDER BY.
## Séance 3 : Modélisation & relations entre tables

### Objectifs pédagogiques

- Comprendre la modélisation relationnelle.
- Mettre en place des relations entre tables via clés étrangères.
### Contenus

#### Modèle entité-association (E-A)

- Concepts de base du modèle E-A.
- Conversion d'un schéma E-A en schéma relationnel.
#### Relations entre tables

- Définir les clés primaires et étrangères.
- Contraintes d'intégrité référentielle.
## Séance 4 : Jointures & requêtes complexes

### Objectifs pédagogiques

- Maîtriser les jointures SQL : INNER, LEFT, RIGHT, FULL.
- Construire des requêtes avec plusieurs tables.
### Contenus

#### Types de jointures

- Jointure interne (INNER JOIN).
- Jointures externes (LEFT, RIGHT, FULL JOIN).
#### Requêtes imbriquées et agrégations

- Sous-requêtes (subqueries).
- Fonctions d'agrégation (SUM, COUNT, AVG).
## Séance 5 : Index & performance

### Objectifs pédagogiques

- Comprendre l'importance des index pour les performances.
- Savoir créer et optimiser les index.
### Contenus

#### Introduction aux index

- Concept d'index dans une base relationnelle.
- Types d'index (B-tree, hash).
#### Optimisation des requêtes

- Analyse des plans d'exécution.
- Création d'index efficaces selon les requêtes.
## Séance 6 : Recherche Full-Text & alternatives

### Objectifs pédagogiques

- Comprendre la recherche Full-Text dans PostgreSQL.
- Découvrir les alternatives et bonnes pratiques.
### Contenus

#### Recherche Full-Text

- Configuration et utilisation du module Full-Text Search de PostgreSQL.
- Requête avec to_tsvector, to_tsquery, ts_rank.
#### Alternatives et bonnes pratiques

- Utilisation de trigrammes, expressions régulières.
- Limites et alternatives au Full-Text Search classique.

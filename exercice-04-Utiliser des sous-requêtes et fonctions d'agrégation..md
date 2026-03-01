# TP SQL : Maîtrise des Sous-Requêtes et Fonctions d'Agrégation

Ce TP vise à renforcer votre compréhension et votre maîtrise des sous-requêtes et des fonctions d'agrégation en SQL. Ces concepts sont fondamentaux pour extraire des informations complexes et synthétiser des données de manière efficace.

L'utilisation d'outils d'IA pour vous aider à formuler vos requêtes est encouragée. L'objectif est d'apprendre à les utiliser efficacement pour valider votre compréhension et affiner vos solutions, pas de copier-coller sans réfléchir. Soyez critique envers les réponses générées et assurez-vous de comprendre *pourquoi* une solution fonctionne et comment elle s'intègre dans le problème posé.

### Objectifs Pédagogiques

À la fin de ce TP, vous serez capable de :
*   Construire des requêtes utilisant des sous-requêtes pour filtrer ou obtenir des valeurs.
*   Appliquer diverses fonctions d'agrégation (COUNT, SUM, AVG, MIN, MAX) pour synthétiser des données.
*   Combiner sous-requêtes et fonctions d'agrégation pour résoudre des problèmes complexes.

### Prérequis

*   Connaissance des bases de SQL (SELECT, FROM, WHERE, GROUP BY, ORDER BY, JOIN).
*   Accès à un environnement SQL (MySQL, PostgreSQL, SQL Server, SQLite, etc.).

### Mise en place de l'environnement

Pour ce TP, nous allons simuler une base de données de gestion de magasin. Veuillez exécuter les scripts SQL ci-dessous pour créer les tables et insérer les données nécessaires.

```sql
-- Création des tables
CREATE TABLE Categories (
    id_categorie INT PRIMARY KEY AUTO_INCREMENT,
    nom_categorie VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE Produits (
    id_produit INT PRIMARY KEY AUTO_INCREMENT,
    nom_produit VARCHAR(100) NOT NULL,
    prix_unitaire DECIMAL(10, 2) NOT NULL,
    id_categorie INT,
    FOREIGN KEY (id_categorie) REFERENCES Categories(id_categorie)
);

CREATE TABLE Clients (
    id_client INT PRIMARY KEY AUTO_INCREMENT,
    nom_client VARCHAR(100) NOT NULL,
    ville VARCHAR(50)
);

CREATE TABLE Commandes (
    id_commande INT PRIMARY KEY AUTO_INCREMENT,
    date_commande DATE NOT NULL,
    id_client INT,
    FOREIGN KEY (id_client) REFERENCES Clients(id_client)
);

CREATE TABLE Lignes_Commande (
    id_ligne INT PRIMARY KEY AUTO_INCREMENT,
    id_commande INT,
    id_produit INT,
    quantite INT NOT NULL,
    prix_total_ligne DECIMAL(10, 2) NOT NULL, -- quantite * prix_unitaire du produit au moment de la commande
    FOREIGN KEY (id_commande) REFERENCES Commandes(id_commande),
    FOREIGN KEY (id_produit) REFERENCES Produits(id_produit)
);

-- Insertion des données
INSERT INTO Categories (nom_categorie) VALUES
('Électronique'),
('Alimentaire'),
('Vêtements'),
('Livres');

INSERT INTO Produits (nom_produit, prix_unitaire, id_categorie) VALUES
('Laptop', 1200.00, 1),
('Smartphone', 800.00, 1),
('Pain', 2.50, 2),
('Lait', 1.20, 2),
('T-shirt', 25.00, 3),
('Jean', 60.00, 3),
('Roman Fantastique', 15.00, 4),
('Livre de Cuisine', 30.00, 4);

INSERT INTO Clients (nom_client, ville) VALUES
('Alice Dupont', 'Paris'),
('Bob Martin', 'Lyon'),
('Charlie Leblanc', 'Paris'),
('Diana Rossi', 'Marseille');

INSERT INTO Commandes (date_commande, id_client) VALUES
('2023-01-10', 1), -- Alice
('2023-01-12', 2), -- Bob
('2023-01-15', 1), -- Alice
('2023-01-20', 3), -- Charlie
('2023-02-01', 2), -- Bob
('2023-02-05', 4); -- Diana

-- Insertion des lignes de commande (calcul de prix_total_ligne basé sur le prix unitaire actuel du produit)
-- Commande 1 (Alice)
INSERT INTO Lignes_Commande (id_commande, id_produit, quantite, prix_total_ligne) VALUES
(1, 1, 1, 1200.00), -- Laptop
(1, 3, 2, 5.00);    -- Pain (2 * 2.50)

-- Commande 2 (Bob)
INSERT INTO Lignes_Commande (id_commande, id_produit, quantite, prix_total_ligne) VALUES
(2, 2, 1, 800.00),  -- Smartphone
(2, 4, 5, 6.00);    -- Lait (5 * 1.20)

-- Commande 3 (Alice)
INSERT INTO Lignes_Commande (id_commande, id_produit, quantite, prix_total_ligne) VALUES
(3, 5, 3, 75.00),   -- T-shirt (3 * 25.00)
(3, 6, 1, 60.00);   -- Jean

-- Commande 4 (Charlie)
INSERT INTO Lignes_Commande (id_commande, id_produit, quantite, prix_total_ligne) VALUES
(4, 7, 2, 30.00);   -- Roman Fantastique (2 * 15.00)

-- Commande 5 (Bob)
INSERT INTO Lignes_Commande (id_commande, id_produit, quantite, prix_total_ligne) VALUES
(5, 1, 1, 1200.00), -- Laptop
(5, 3, 1, 2.50);    -- Pain

-- Commande 6 (Diana)
INSERT INTO Lignes_Commande (id_commande, id_produit, quantite, prix_total_ligne) VALUES
(6, 8, 1, 30.00);   -- Livre de Cuisine
```

### Consignes Générales

*   Exécutez chaque requête et vérifiez le résultat.
*   N'hésitez pas à décomposer les problèmes complexes en étapes plus petites.
*   Documentez vos requêtes avec des commentaires si nécessaire pour expliquer votre logique.

---

### Exercices

#### Partie 1 : Fonctions d'Agrégation Simples

1.  **Nombre total de produits distincts** dans la base de données.
2.  **Prix unitaire moyen** de tous les produits.
3.  **Produit le plus cher** (afficher son nom et son prix).
4.  **Nombre total de commandes** passées.
5.  **Chiffre d'affaires total** (somme de `prix_total_ligne` de toutes les lignes de commande).

#### Partie 2 : Agrégation avec `GROUP BY`

1.  **Nombre de produits par catégorie** (afficher le nom de la catégorie et le compte).
2.  **Prix unitaire moyen par catégorie** (afficher le nom de la catégorie et le prix moyen).
3.  **Chiffre d'affaires par client** (afficher le nom du client et le CA total de ses commandes).
4.  **Nombre de commandes par ville de client**.

#### Partie 3 : Introduction aux Sous-Requêtes

1.  **Produits dont le prix unitaire est supérieur au prix unitaire moyen** de tous les produits.
2.  **Clients ayant passé plus de 1 commande**. Affichez le nom du client.
3.  **Produits appartenant à la catégorie "Électronique" et dont le prix est supérieur au prix moyen des produits de cette même catégorie "Électronique"**.
4.  **Commandes dont le montant total est supérieur au montant moyen de toutes les commandes**. Affichez l'ID de la commande et son montant total.

#### Partie 4 : Sous-Requêtes Correlées et Avancées (Défi)

1.  **Produits dont le prix unitaire est supérieur au prix unitaire moyen de leur propre catégorie**. Affichez le nom du produit, son prix, et le nom de sa catégorie.
2.  **Clients qui n'ont jamais commandé de "Laptop"**. Affichez leur nom.
3.  **Catégories qui n'ont aucun produit vendu**. (Indice : une catégorie peut exister mais n'avoir aucun produit associé à une ligne de commande).
4.  **Trouver le client qui a dépensé le plus d'argent sur une seule commande**. Affichez le nom du client, l'ID de la commande et le montant total de cette commande.

Bonne pratique !
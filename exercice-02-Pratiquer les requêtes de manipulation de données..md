Bonjour !

Ce TP est conçu pour vous permettre de pratiquer les requêtes de manipulation de données (DML) fondamentales en SQL. Vous allez interagir avec une base de données exemple pour créer, lire, modifier et supprimer des enregistrements.

---

### **TP : Maîtrise des Requêtes DML (SELECT, INSERT, UPDATE, DELETE)**

**Objectif :** Pratiquer les requêtes `SELECT`, `INSERT`, `UPDATE` et `DELETE` pour manipuler des données dans une base de données relationnelle.

**Prérequis :**
*   Un environnement SQL fonctionnel (par exemple, MySQL, PostgreSQL, SQLite, SQL Server) et un client pour exécuter vos requêtes.
*   Connaissance de base de la structure d'une table et des types de données SQL.

---

### **1. Mise en place de la base de données**

Pour commencer, nous allons créer une petite base de données de gestion de bibliothèque simplifiée.

**1.1. Création des tables**

Exécutez les requêtes suivantes pour créer les tables `Auteurs` et `Livres` :

```sql
-- Table Auteurs
CREATE TABLE Auteurs (
    id INT PRIMARY KEY AUTO_INCREMENT, -- Utilisez SERIAL pour PostgreSQL
    nom VARCHAR(100) NOT NULL,
    prenom VARCHAR(100),
    nationalite VARCHAR(50)
);

-- Table Livres
CREATE TABLE Livres (
    id INT PRIMARY KEY AUTO_INCREMENT, -- Utilisez SERIAL pour PostgreSQL
    titre VARCHAR(255) NOT NULL,
    auteur_id INT,
    annee_publication INT,
    disponible BOOLEAN DEFAULT TRUE,
    FOREIGN KEY (auteur_id) REFERENCES Auteurs(id)
);
```
*Note : Si vous utilisez PostgreSQL, remplacez `AUTO_INCREMENT` par `SERIAL` pour les colonnes `id`.*

**1.2. Insertion des données initiales**

Maintenant, peuplons nos tables avec quelques données :

```sql
-- Insertion d'auteurs
INSERT INTO Auteurs (nom, prenom, nationalite) VALUES
('Hugo', 'Victor', 'Française'),
('Verne', 'Jules', 'Française'),
('Christie', 'Agatha', 'Britannique'),
('Orwell', 'George', 'Britannique');

-- Insertion de livres
INSERT INTO Livres (titre, auteur_id, annee_publication, disponible) VALUES
('Les Misérables', 1, 1862, TRUE),
('Vingt mille lieues sous les mers', 2, 1870, TRUE),
('Le Crime de l\'Orient-Express', 3, 1934, TRUE),
('1984', 4, 1949, TRUE),
('Notre-Dame de Paris', 1, 1831, FALSE),
('Le Tour du monde en quatre-vingts jours', 2, 1873, TRUE);
```

---

### **2. Exercices de manipulation de données**

Maintenant que notre base est prête, vous pouvez commencer à pratiquer les requêtes DML.

#### **Partie 1 : Requêtes SELECT (Lecture de données)**

Écrivez les requêtes `SELECT` pour obtenir les informations suivantes :

1.  Sélectionnez tous les enregistrements de la table `Livres`.
2.  Affichez le titre et l'année de publication de tous les livres qui sont actuellement disponibles.
3.  Trouvez tous les livres écrits par 'Victor Hugo' (vous devrez joindre les tables `Livres` et `Auteurs`).
4.  Listez les titres des livres publiés après l'année 1900.
5.  Comptez le nombre total de livres disponibles dans la bibliothèque.
6.  Affichez le nom et le prénom de tous les auteurs de nationalité 'Britannique'.

#### **Partie 2 : Requêtes INSERT (Ajout de données)**

Ajoutez les nouvelles données suivantes à votre base :

1.  Insérez un nouvel auteur : 'J.R.R.' 'Tolkien', de nationalité 'Britannique'.
2.  Ajoutez un nouveau livre pour cet auteur : 'Le Seigneur des Anneaux', publié en 1954, et disponible. (Assurez-vous d'utiliser l'`id` correct de l'auteur que vous venez d'insérer).
3.  Insérez un livre dont l'auteur n'est pas encore dans la base (par exemple, 'Le Petit Prince' de 'Antoine de Saint-Exupéry'). Pour cela, vous devrez d'abord ajouter l'auteur, puis le livre.

#### **Partie 3 : Requêtes UPDATE (Modification de données)**

Modifiez les données existantes comme suit :

1.  Marquez le livre 'Vingt mille lieues sous les mers' comme non disponible.
2.  Corrigez l'année de publication du livre '1984' à 1949 (si vous l'aviez mal saisie ou si elle a été mise à jour).
3.  Renommez le titre du livre 'Les Misérables' en 'Les Misérables (Édition Complète)'.

#### **Partie 4 : Requêtes DELETE (Suppression de données)**

Supprimez les données suivantes de votre base :

1.  Supprimez le livre 'Le Crime de l\'Orient-Express' de la base de données.
2.  Supprimez le livre 'Le Seigneur des Anneaux' que vous avez ajouté précédemment.
3.  Supprimez l'auteur 'Antoine de Saint-Exupéry' (assurez-vous d'abord que tous ses livres ont été supprimés ou que la contrainte de clé étrangère le permet, sinon vous pourriez rencontrer une erreur).

---

### **Considérations sur l'IA**

L'IA est un outil puissant. N'hésitez pas à l'utiliser pour vous aider à trouver la syntaxe, à explorer des pistes ou à vérifier vos requêtes. Cependant, l'objectif principal de ce TP est votre *compréhension* des concepts. Assurez-vous de comprendre chaque partie de la requête générée et d'être capable de l'expliquer. Ne vous contentez pas de copier-coller : utilisez l'IA comme un assistant, pas comme un substitut à votre apprentissage.

---

### **Conseils pour la réussite**

*   Lisez attentivement chaque consigne.
*   Testez vos requêtes une par une et observez les résultats.
*   N'ayez pas peur de faire des erreurs, elles font partie du processus d'apprentissage. Si une requête ne fonctionne pas, analysez le message d'erreur et essayez de comprendre pourquoi.
*   Pensez à la logique derrière chaque opération : que voulez-vous faire, et comment SQL peut-il vous aider à l'accomplir ?

Bonne pratique !
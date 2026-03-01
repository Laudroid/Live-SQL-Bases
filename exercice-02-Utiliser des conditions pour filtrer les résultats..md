Bonjour !

Ce TP a pour objectif de vous familiariser avec les clauses `WHERE`, `AND`, `OR`, `NOT`, et `ORDER BY` en SQL, qui sont fondamentales pour filtrer et organiser les données.

---

### TP SQL - Filtrage et Tri des Données

**Objectif du TP :** Utiliser des conditions pour filtrer les résultats et les trier.

**Contexte :**
Vous travaillez en tant que développeur junior pour une librairie en ligne. Votre tâche est d'extraire des informations précises de la base de données des livres afin de répondre aux besoins du marketing, de la gestion des stocks et des analyses commerciales.

**Prérequis :**
*   Connaissance des requêtes `SELECT` de base.
*   Un environnement SQL (MySQL, PostgreSQL, SQLite, SQL Server, etc.) pour exécuter les requêtes.

---

#### 1. Mise en place de l'environnement

Commencez par créer une base de données et une table `Livres`, puis insérez les données suivantes. Ces données serviront de base pour tous les exercices.

```sql
-- Création de la base de données (si nécessaire)
CREATE DATABASE IF NOT EXISTS librairie;
USE librairie;

-- Création de la table Livres
CREATE TABLE IF NOT EXISTS Livres (
    id_livre INT PRIMARY KEY AUTO_INCREMENT,
    titre VARCHAR(255) NOT NULL,
    auteur VARCHAR(255) NOT NULL,
    genre VARCHAR(100),
    prix DECIMAL(5, 2) NOT NULL,
    annee_publication INT,
    stock INT NOT NULL DEFAULT 0,
    disponible BOOLEAN NOT NULL DEFAULT TRUE
);

-- Insertion des données
INSERT INTO Livres (titre, auteur, genre, prix, annee_publication, stock, disponible) VALUES
('Le Seigneur des Anneaux', 'J.R.R. Tolkien', 'Fantasy', 25.50, 1954, 150, TRUE),
('1984', 'George Orwell', 'Dystopie', 12.00, 1949, 80, TRUE),
('Dune', 'Frank Herbert', 'Science-fiction', 18.75, 1965, 120, TRUE),
('Orgueil et Préjugés', 'Jane Austen', 'Roman', 9.99, 1813, 60, TRUE),
('Le Petit Prince', 'Antoine de Saint-Exupéry', 'Conte', 7.50, 1943, 200, TRUE),
('Harry Potter à l''école des sorciers', 'J.K. Rowling', 'Fantasy', 22.00, 1997, 180, TRUE),
('Fondation', 'Isaac Asimov', 'Science-fiction', 15.00, 1951, 90, TRUE),
('Le Guide du voyageur galactique', 'Douglas Adams', 'Science-fiction', 11.25, 1979, 70, TRUE),
('Crime et Châtiment', 'Fiodor Dostoïevski', 'Roman', 14.50, 1866, 40, TRUE),
('Les Misérables', 'Victor Hugo', 'Roman', 28.00, 1862, 30, TRUE),
('Le Nom de la Rose', 'Umberto Eco', 'Historique', 16.90, 1980, 55, TRUE),
('Blade Runner', 'Philip K. Dick', 'Science-fiction', 10.50, 1968, 0, FALSE),
('Le Hobbit', 'J.R.R. Tolkien', 'Fantasy', 19.99, 1937, 110, TRUE),
('L''Alchimiste', 'Paulo Coelho', 'Philosophie', 8.90, 1988, 130, TRUE),
('Sapiens : Une brève histoire de l''humanité', 'Yuval Noah Harari', 'Essai', 20.00, 2011, 75, TRUE),
('Le Problème à trois corps', 'Liu Cixin', 'Science-fiction', 17.00, 2008, 65, TRUE),
('Le Meurtre de Roger Ackroyd', 'Agatha Christie', 'Policier', 9.50, 1926, 85, TRUE),
('Le Silence des agneaux', 'Thomas Harris', 'Thriller', 13.00, 1988, 50, TRUE),
('La Nuit des temps', 'René Barjavel', 'Science-fiction', 10.00, 1968, 45, TRUE),
('Le Trône de Fer', 'George R.R. Martin', 'Fantasy', 29.99, 1996, 95, TRUE);
```

---

#### 2. Consignes Générales

*   Pour chaque exercice, écrivez une requête `SELECT` qui répond à la question posée.
*   N'hésitez pas à expérimenter et à tester différentes approches.
*   Vérifiez toujours vos résultats par rapport aux données initiales pour vous assurer qu'ils sont corrects.

---

#### 3. Exercices

**Exercice 1 : Filtrage simple avec `WHERE`**

1.  Affichez tous les livres du genre 'Science-fiction'.
2.  Listez les livres publiés après l'année 2000.
3.  Trouvez les livres dont le prix est strictement inférieur à 10.00€.
4.  Sélectionnez les livres dont le stock est égal à 0.

**Exercice 2 : Combiner les conditions avec `AND` et `OR`**

1.  Affichez les livres du genre 'Fantasy' *et* publiés avant 1990.
2.  Listez les livres dont l'auteur est 'J.R.R. Tolkien' *ou* 'J.K. Rowling'.
3.  Trouvez les livres de 'Science-fiction' *ou* de 'Dystopie' dont le prix est supérieur à 15.00€.
4.  Sélectionnez les livres du genre 'Roman' *ou* 'Historique', *et* qui ont été publiés après 1900. Faites attention à l'ordre des opérateurs pour obtenir le résultat attendu.

**Exercice 3 : Exclure des résultats avec `NOT`**

1.  Affichez tous les livres qui ne sont *pas* du genre 'Science-fiction'.
2.  Listez les livres qui ne sont *pas* disponibles à la vente (la colonne `disponible` est à `FALSE`).
3.  Trouvez les livres dont l'auteur n'est *pas* 'George Orwell' et dont le stock est supérieur à 0.

**Exercice 4 : Trier les résultats avec `ORDER BY`**

1.  Affichez tous les livres, triés par prix croissant.
2.  Listez les livres du genre 'Fantasy', triés par année de publication décroissante.
3.  Sélectionnez tous les livres, triés d'abord par genre (ordre alphabétique), puis par titre (ordre alphabétique) pour les livres du même genre.
4.  Trouvez les livres dont le prix est entre 10.00€ et 20.00€, triés par auteur (alphabétique) puis par prix (décroissant).

**Exercice 5 : Combinaison avancée**

1.  Affichez les livres du genre 'Science-fiction' *ou* 'Fantasy', publiés après 1980, et dont le stock est supérieur à 50. Triez les résultats par titre.
2.  Listez les livres qui ne sont *pas* du genre 'Essai', qui ont un prix inférieur à 25.00€, et qui sont disponibles. Triez-les par genre (croissant) puis par prix (décroissant).
3.  Sélectionnez les livres dont l'auteur est 'George Orwell' *ou* 'Jane Austen', et qui ont été publiés avant 2000. Triez ces livres par année de publication croissante.

---

#### 4. Pour aller plus loin (Optionnel)

1.  **Recherche partielle :** Comment trouver tous les livres dont le titre contient le mot "Prince" ? (Indice : explorez l'opérateur `LIKE`).
2.  **Agrégation conditionnelle :** Combien de livres de 'Science-fiction' ont été publiés après 2000 ? (Indice : combinez `WHERE` avec une fonction d'agrégation comme `COUNT()`).
3.  **Complexité :** Écrivez une requête pour trouver les livres de 'Roman' ou 'Historique', publiés après 1950, dont le prix est supérieur à la moyenne de tous les livres de ces genres. (Ceci implique des sous-requêtes ou des CTEs, un concept plus avancé mais intéressant à explorer).

---

Prenez votre temps pour chaque exercice. L'important est de comprendre comment chaque clause modifie le jeu de résultats. Bonne pratique !
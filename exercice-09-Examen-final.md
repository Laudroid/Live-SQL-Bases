

# QCM SQL – Bases (40 questions - 30 minutes)

**1.** Qu’est-ce qu’une base de données relationnelle ?

A. Un fichier texte structuré

B. Un langage de programmation

C. Une application web

D. Un ensemble de tables liées entre elles

Réponse : 


**2.** Dans une table SQL, une ligne correspond à :

A. Une colonne

B. Un enregistrement

C. Une base de données

D. Une contrainte

Réponse : 


**3.** Une clé primaire permet :

A. D’identifier de manière unique une ligne

B. D’optimiser les requêtes

C. De lier deux tables

D. De trier les données

Réponse : 


**4.** Une clé étrangère sert à :

A. Supprimer des données

B. Faire des calculs

C. Relier deux tables

D. Trier les résultats

Réponse : 


**5.** Quel SGBD est utilisé dans ce cours ?

A. MySQL

B. Oracle

C. PostgreSQL

D. MongoDB

Réponse : 


**6.** Une colonne représente :

A. Un enregistrement

B. Une clé

C. Une table

D. Un attribut

Réponse : 


---

**7.** Quelle commande permet de lire des données ?

A. GET

B. SELECT

C. READ

D. FETCH

Réponse : 


**8.** Quelle commande permet d’ajouter des données ?

A. INSERT

B. ADD

C. CREATE

D. APPEND

Réponse : 


**9.** Quelle commande permet de modifier des données ?

A. CHANGE

B. MODIFY

C. UPDATE

D. ALTER

Réponse : 


**10.** Quelle commande permet de supprimer des données ?

A. REMOVE

B. ERASE

C. DROP

D. DELETE

Réponse : 


**11.** Quelle clause permet de filtrer les résultats ?

A. FILTER

B. WHERE

C. HAVING

D. ORDER

Réponse : 


**12.** Quel opérateur signifie “ET logique” ?

A. OR

B. NOT

C. AND

D. XOR

Réponse : 


**13.** ORDER BY permet :

A. Trier

B. Filtrer

C. Supprimer

D. Ajouter

Réponse : 


**14.** Quelle requête est correcte ?

A. SELECT FROM users

B. SELECT * users

C. SELECT * FROM users

D. GET * FROM users

Réponse : 


**15.** Quelle clause permet de trier en ordre décroissant ?

A. DESC

B. DOWN

C. REVERSE

D. LOW

Réponse : 


---

**16.** Le modèle E-A signifie :

A. Entité-Attribut

B. Entité-Association

C. Enregistrement-Attribut

D. Entité-Algorithme

Réponse : 


**17.** Une entité correspond à :

A. Une requête

B. Une colonne

C. Une clé

D. Une table

Réponse : 


**18.** Une association représente :

A. Une contrainte

B. Une relation entre entités

C. Une clé primaire

D. Un index

Réponse : 


**19.** L’intégrité référentielle garantit :

A. La cohérence des relations

B. La rapidité

C. Le tri

D. Le stockage

Réponse : 


**20.** Une table sans clé primaire :

A. Peut exister mais est déconseillée

B. Est impossible

C. Est plus rapide

D. Est obligatoire

Réponse : 


---

**21.** Une INNER JOIN retourne :

A. Toutes les lignes

B. Les lignes correspondantes uniquement

C. Les lignes de gauche uniquement

D. Les lignes de droite uniquement

Réponse : 


**22.** Une LEFT JOIN retourne :

A. Seulement les correspondances

B. Toutes les lignes de droite

C. Toutes les lignes de gauche + correspondances

D. Rien

Réponse : 


**23.** Une RIGHT JOIN retourne :

A. Toutes les lignes de gauche

B. Seulement les correspondances

C. Toutes les lignes de droite + correspondances

D. Rien

Réponse : 


**24.** Une FULL JOIN retourne :

A. Rien

B. Toutes les lignes des deux tables

C. Seulement les correspondances

D. Une seule table

Réponse : 


**25.** Une sous-requête est :

A. Une requête dans une requête

B. Une table

C. Une clé

D. Un index

Réponse : 


**26.** COUNT permet de :
A. Compter

B. Additionner

C. Trier

D. Filtrer

Réponse : 


**27.** Que fait la requête suivante ?
SELECT name FROM products ORDER BY price DESC;

A. Trie les produits par prix croissant

B. Trie les produits par prix décroissant

C. Affiche uniquement les prix

D. Supprime les produits les plus chers

Réponse : 


**28.** Que fait la requête suivante ?
SELECT COUNT(*) FROM orders;

A. Additionne les commandes

B. Trie les commandes

C. Supprime les commandes

D. Compte le nombre de commandes

Réponse : 


---

**29.** Un index sert à :

A. Stocker les données

B. Modifier les données

C. Supprimer les doublons

D. Accélérer les requêtes

Réponse : 


**30.** Un index est particulièrement utile sur :

A. Les petites tables

B. Les colonnes souvent recherchées

C. Les colonnes inutilisées

D. Les clés étrangères uniquement

Réponse : 


**31.** Un index peut ralentir :

A. INSERT

B. SELECT

C. Lecture

D. Affichage

Réponse : 


**32.** Le type d’index le plus courant est :

A. B-tree

B. Hash

C. Graph

D. XML

Réponse : 


**33.** Un plan d’exécution permet de :

A. Supprimer les données

B. Créer un index

C. Modifier une table

D. Analyser la performance d’une requête

Réponse : 


---

**34.** Le Full-Text Search permet :

A. Trier les nombres

B. Rechercher du texte intelligemment

C. Supprimer du texte

D. Compter les lignes

Réponse : 


**35.** Que fait la requête suivante ?

SELECT u.name, o.id
FROM users u
INNER JOIN orders o ON u.id = o.user_id;

A. Affiche les utilisateurs ayant des commandes

B. Affiche toutes les commandes

C. Affiche tous les utilisateurs

D. Affiche les commandes sans utilisateur

Réponse : 


**36.** Que fait la requête suivante ?

SELECT * FROM products WHERE name LIKE '%phone%';

A. Sélectionne les produits dont le nom contient "phone"

B. Sélectionne les produits dont le nom commence par "phone"

C. Supprime les produits "phone"

D. Trie les produits dont le nom contient "phone"

Réponse : 


**37.** Que retourne la requête suivante ?

SELECT * FROM users WHERE age = NULL;

A. Une erreur SQL

B. Aucun résultat

C. Tous les utilisateurs avec un âge NULL

D. Tous les utilisateurs

Réponse : 


**38.** Que fait réellement cette requête ?

SELECT u.name, o.id
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
WHERE o.id IS NULL;

A. Affiche tous les utilisateurs avec leurs commandes

B. Affiche uniquement les utilisateurs ayant des commandes

C. Affiche les utilisateurs sans commande

D. Affiche les utilisateurs sans identifiant

Réponse : 


**39.** Une limite du SQL classique est :

A. Trop rapide

B. Trop simple

C. Compliqué à utiliser

D. Difficulté sur recherche textuelle avancée

Réponse : 


**40.** Une bonne pratique SQL est :

A. Ne jamais utiliser d’index

B. Toujours utiliser SELECT *

C. Adapter les index aux requêtes

D. Supprimer les clés

Réponse : 


---


# EXAMEN SQL – PostgreSQL (Durée : 1h)

## Consignes

* Vous devez exécuter vos requêtes dans **pgAdmin4** après import de la base **France.sql** fournie.
* Toute réponse doit être une **requête SQL valide** (sauf mention contraire).

---

# 🔹 Partie 1 — Requêtes simples

**1.** Afficher toutes les régions.

Réponse : 


**2.** Afficher le nom et le code des départements.

Réponse : 


**3.** Afficher toutes les villes (`towns`) du département `'01'`.

Réponse : 


**4.** Afficher les villes dont le nom commence par `'Saint'`.

Réponse : 


**5.** Compter le nombre total de villes dans la table `towns`.

Réponse : 


**6.** Afficher les départements triés par nom (ordre alphabétique).

Réponse : 


**7.** Afficher les départements appartenant à la région `'82'`.

Réponse : 


---

# Partie 2 — Jointures

**8.** Afficher le nom des départements suivi du nom de leur région.

Réponse : 


---

**9.** Afficher les villes avec le nom de leur département.

Réponse : 


---

**10.** Afficher les villes avec le nom du département ET de la région.

Réponse : 


---

**11.** Afficher toutes les régions même celles sans département associé.

Réponse : 


---

# Partie 3 — Agrégations

**12.** Compter le nombre de départements par région. 

Réponse : 


---

**13.** Afficher les régions ayant plus de 3 départements.

Réponse : 


---

**14.** Trouver le département ayant le plus de villes.

Réponse : 


---

# Partie 4 — Requêtes intermédiaires

**15.** Afficher les villes dont le nom contient `'sur'`.

Réponse : 


---

**16.** Afficher les villes dont l’article (`article`) est NULL.

Réponse : 


---

**17.** Afficher les villes appartenant aux départements de la région `'82'`.

Réponse : 


---

# Partie 5 — Requêtes avancées

**18.** Afficher les départements qui n’ont aucune ville.

Réponse : 


---

**19.** Afficher les régions qui n’ont aucun département.

Réponse : 


---

**20.** Afficher les noms des départements qui ont au moins une ville.

Réponse : 


---

**21.** Afficher les villes appartenant au département ayant le plus de villes.

Réponse : 


---

**22.** Afficher les régions triées par nombre de villes (du plus grand au plus petit).

Réponse : 



# Partie 6 — Structure, index et performance

---

## Modification de structure

**23.** Ajouter une colonne `population` de type entier dans la table `towns`.

Réponse : 



---

**24.** Ajouter une contrainte pour que `population` soit toujours positive.

Réponse : 



---

**25.** Ajouter une colonne `created_at` de type `TIMESTAMP` avec une valeur par défaut correspondant à la date actuelle dans la table `departments`.

Réponse : 



---


**26.** Créer un index sur la colonne `name` de la table `towns`.

Réponse : 



---

**27.** Proposer un index pertinent pour optimiser les requêtes recherchant des villes par département.

Réponse : 



---

**28.** Expliquer en une phrase pourquoi un index peut ralentir certaines opérations.

Réponse : 



---

## Analyse de performance (EXPLAIN)

**29.** Donner la commande permettant d’analyser le plan d’exécution de la requête suivante :

```sql
SELECT * FROM towns WHERE name = 'Paris';
```

Réponse : 



Interprétez le résultat de l'analyse


Réponse : 




---

**30.** Quelle est la différence entre `EXPLAIN` et `EXPLAIN ANALYZE` ?

Réponse : 





-- Fin de l'examen --






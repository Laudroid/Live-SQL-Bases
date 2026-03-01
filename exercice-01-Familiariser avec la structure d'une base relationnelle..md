Bonjour à toutes et à tous,

Ce TP a pour objectif de vous familiariser avec les composants fondamentaux d'une base de données relationnelle. Comprendre comment les données sont organisées est la première étape pour interagir efficacement avec elles.

---

### **TP : Découverte de la Structure Relationnelle**

**Objectif du TP :**
Identifier et comprendre le rôle des tables, des colonnes, des clés primaires et des clés étrangères au sein d'un schéma de base de données relationnelle.

**Contexte :**
Nous allons travailler sur un schéma simplifié représentant une base de données pour une librairie en ligne. Ce schéma est présenté sous forme textuelle pour faciliter la lecture.

**Ressources à disposition :**
*   Vos connaissances sur les bases de données relationnelles.
*   Les outils d'IA générative (ChatGPT, Bard, Copilot, etc.) pour vous aider à clarifier des concepts, vérifier des définitions ou explorer des exemples. N'oubliez pas que l'objectif est votre compréhension : utilisez l'IA comme un assistant intelligent, pas comme un remplaçant de votre réflexion.

---

**Mise en situation : Schéma de la Librairie en Ligne**

Voici le schéma de notre base de données. Chaque ligne représente une table, suivie de ses colonnes. Les notations `(PK)` et `(FK)` indiquent respectivement une clé primaire et une clé étrangère.

```
-- Table : Livres
Livres (
    id_livre INT (PK),
    titre VARCHAR(255),
    auteur VARCHAR(255),
    annee_publication INT,
    isbn VARCHAR(13) UNIQUE,
    id_categorie INT (FK)
)

-- Table : Categories
Categories (
    id_categorie INT (PK),
    nom_categorie VARCHAR(100) UNIQUE
)

-- Table : Clients
Clients (
    id_client INT (PK),
    nom VARCHAR(100),
    prenom VARCHAR(100),
    email VARCHAR(255) UNIQUE,
    adresse VARCHAR(255)
)

-- Table : Commandes
Commandes (
    id_commande INT (PK),
    id_client INT (FK),
    date_commande DATETIME,
    statut VARCHAR(50) -- Ex: 'En attente', 'Expédiée', 'Livrée'
)

-- Table : Lignes_Commande
Lignes_Commande (
    id_ligne_commande INT (PK),
    id_commande INT (FK),
    id_livre INT (FK),
    quantite INT,
    prix_unitaire DECIMAL(10, 2)
)
```

---

**Exercices :**

Pour chaque question, justifiez brièvement vos réponses.

1.  **Identification des Tables et Colonnes :**
    *   Listez toutes les tables présentes dans ce schéma.
    *   Pour chaque table, listez l'ensemble de ses colonnes.

2.  **Clés Primaires (PK) :**
    *   Pour chaque table, identifiez la ou les colonnes qui constituent sa clé primaire.
    *   Expliquez en une phrase le rôle d'une clé primaire dans une table (pourquoi est-elle essentielle ?).

3.  **Clés Étrangères (FK) et Relations :**
    *   Identifiez toutes les clés étrangères présentes dans le schéma.
    *   Pour chaque clé étrangère identifiée, précisez :
        *   Dans quelle table elle se trouve.
        *   Quelle table elle référence (vers quelle table pointe-t-elle ?).
        *   Quelle colonne de la table référencée elle utilise.
    *   Décrivez la relation logique que chaque clé étrangère établit entre les deux tables concernées (par exemple : "La table `Commandes` est liée à la table `Clients` par `id_client`, signifiant qu'une commande est passée par un client.").

4.  **Représentation Visuelle (Optionnel mais recommandé) :**
    *   Dessinez un diagramme entité-relation (ERD) simple pour ce schéma. Vous pouvez utiliser un outil en ligne, un logiciel de dessin, ou même un simple papier-crayon. L'objectif est de visualiser les tables comme des entités et les clés étrangères comme des relations.

5.  **Scénario d'Interrogation (Réflexion) :**
    *   Imaginez que vous souhaitez récupérer tous les livres commandés par un client spécifique. Quelles tables devriez-vous utiliser et comment les lieriez-vous logiquement ? (Pas de code SQL attendu, juste une explication des étapes et des tables/clés impliquées).

---

**Conseils pour l'utilisation de l'IA :**

*   **Définitions :** Si un terme comme "clé primaire" ou "clé étrangère" n'est pas clair, demandez à l'IA une définition simple et un exemple.
*   **Vérification :** Une fois que vous avez identifié les clés, vous pouvez demander à l'IA : "Dans ce schéma [collez le schéma], est-ce que `id_livre` dans `Livres` est bien une clé primaire ? Pourquoi ?"
*   **Clarification des relations :** Si vous avez du mal à formuler la relation entre deux tables, demandez à l'IA de vous donner des exemples de phrases pour décrire une relation de clé étrangère.
*   **Soyez précis :** Plus votre question à l'IA est précise, plus sa réponse sera pertinente.

---

Bon courage pour ce TP ! N'hésitez pas à explorer et à poser des questions si des points restent flous. C'est en manipulant ces concepts que vous les maîtriserez le mieux.
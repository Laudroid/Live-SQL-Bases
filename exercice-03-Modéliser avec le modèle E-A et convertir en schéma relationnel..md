Bonjour à toutes et à tous,

Ce TP a pour objectif de vous permettre de pratiquer la modélisation de données, une étape fondamentale dans la conception de bases de données. Vous allez construire un modèle Entité-Association (E-A) pour un cas concret et simple, puis le traduire en un schéma relationnel prêt à être implémenté.

---

### **TP : Modélisation E-A et Conversion Relationnelle**

**Objectif du TP :**
*   Modéliser un cas d'usage simple à l'aide du modèle Entité-Association (E-A).
*   Convertir ce modèle E-A en un schéma relationnel (tables, colonnes, clés primaires et étrangères).

---

**Contexte : Gestion d'une bibliothèque de films simplifiée**

Imaginez que vous devez concevoir la base de données pour une application très simple de gestion de films. Cette application doit permettre de :
*   Stocker des informations sur les **films**.
*   Stocker des informations sur les **réalisateurs** de ces films.
*   Savoir quel réalisateur a dirigé quel film.

---

**Partie 1 : Modélisation Entité-Association (E-A)**

En vous basant sur le contexte ci-dessus, réalisez le modèle Entité-Association.

1.  **Identification des Entités :**
    *   Listez les entités principales nécessaires pour représenter les informations.
    *   Pour chaque entité, identifiez ses attributs pertinents (par exemple, un film a un titre, une année de sortie, etc.).
    *   Déterminez l'attribut qui servira de clé primaire pour chaque entité.

2.  **Identification des Relations :**
    *   Identifiez les relations entre vos entités.
    *   Pour chaque relation, précisez sa nature (par exemple, "réalise").
    *   Définissez les cardinalités de chaque côté de la relation (par exemple, un réalisateur peut réaliser plusieurs films, un film est réalisé par un seul réalisateur).

**Conseil :** N'hésitez pas à utiliser des outils de modélisation (comme un logiciel de dessin ou un outil en ligne) ou des assistants IA pour vous aider à visualiser ou à structurer votre pensée. L'important est de bien comprendre pourquoi vous faites certains choix.

---

**Partie 2 : Conversion en Schéma Relationnel**

Une fois votre modèle E-A établi, convertissez-le en un schéma relationnel.

1.  **Définition des Tables :**
    *   Pour chaque entité de votre modèle E-A, créez une table correspondante.
    *   Pour chaque attribut d'une entité, définissez une colonne dans la table, en choisissant un type de données approprié (par exemple, `VARCHAR(255)` pour un titre, `INT` pour une année).
    *   Indiquez clairement la clé primaire (`PK`) pour chaque table.

2.  **Gestion des Relations :**
    *   Appliquez les règles de transformation pour représenter les relations entre vos entités sous forme de clés étrangères (`FK`).
    *   Indiquez clairement les clés étrangères, en précisant la table de référence et la colonne de référence.

3.  **Script SQL (DDL) :**
    *   Rédigez le script SQL (Data Definition Language) complet pour créer ces tables.
    *   Assurez-vous que les clés primaires et étrangères sont correctement définies dans le script.

**Conseil :** Vous pouvez également solliciter l'IA pour générer le script SQL de création des tables. Cependant, votre rôle est de **vérifier, comprendre et justifier chaque ligne** du script. C'est en comprenant la logique derrière chaque instruction que vous maîtriserez la conversion.

---

**Livrables attendus :**

1.  **Modèle E-A :** Un diagramme E-A (image ou description textuelle claire des entités, attributs, relations et cardinalités).
2.  **Schéma Relationnel :** La liste des tables avec leurs colonnes, types de données, clés primaires et clés étrangères.
3.  **Script SQL DDL :** Le script SQL complet pour créer les tables de votre base de données.

---

**Quelques rappels pour vous guider :**

*   **Simplicité :** Restez simple. Le but est de maîtriser les bases de la modélisation.
*   **Cohérence :** Assurez-vous que votre schéma relationnel découle logiquement de votre modèle E-A.
*   **Précision :** Soyez précis sur les types de données et les contraintes (clés primaires/étrangères).

Bon travail ! L'IA est un excellent assistant, mais votre compréhension et votre capacité à justifier vos choix sont la clé de la réussite dans ce domaine.
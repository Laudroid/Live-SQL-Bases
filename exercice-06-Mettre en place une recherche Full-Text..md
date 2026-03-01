Bonjour à toutes et à tous,

Ce TP est conçu pour vous guider dans la mise en œuvre de la recherche Full-Text avec PostgreSQL. L'objectif est de dépasser les limitations des recherches `LIKE` et d'offrir une expérience de recherche plus pertinente et performante à vos utilisateurs.

---

# **TP : Maîtriser la Recherche Full-Text avec PostgreSQL**

**Objectif du TP :**
Mettre en place une recherche Full-Text efficace en configurant PostgreSQL et en utilisant les fonctions `to_tsvector` et `to_tsquery` pour interroger des données textuelles.

**Contexte :**
Imaginez que vous développez un système de gestion de contenu pour un site d'actualités ou un blog. Les utilisateurs ont besoin de trouver rapidement des articles pertinents en fonction de mots-clés, d'expressions ou de sujets. Une simple recherche par `LIKE '%mot%'` est souvent insuffisante : elle est lente sur de grands volumes de données, ne gère pas les synonymes, les pluriels, ou la pertinence des résultats. La recherche Full-Text de PostgreSQL est la solution pour adresser ces défis.

**Prérequis :**
*   PostgreSQL installé et accessible (version 12+ est recommandée pour les colonnes générées).
*   Connaissances de base en SQL (création de tables, insertion de données, requêtes `SELECT`).
*   Un client SQL de votre choix (psql, DBeaver, pgAdmin, etc.).

**Conseil IA :**
Votre assistant IA peut être un excellent allié tout au long de ce TP. N'hésitez pas à le solliciter pour :
*   Générer des données d'exemple variées pour vos articles.
*   Expliquer plus en détail des concepts spécifiques (ex: "stemming", "stop-words", "index GIN").
*   Vous aider à déboguer une requête SQL qui ne fonctionne pas comme prévu.
*   Suggérer des syntaxes alternatives ou des optimisations.

---

### **Exercices**

#### **Exercice 1 : Préparation de l'environnement et des données**

1.  **Création de la base de données :**
    *   Créez une nouvelle base de données nommée `actualites_db`.

2.  **Création de la table `articles` :**
    *   Créez une table `articles` avec les colonnes suivantes :
        *   `id` (clé primaire, auto-incrémentée)
        *   `titre` (VARCHAR, non nul)
        *   `contenu` (TEXT, non nul)
        *   `auteur` (VARCHAR)
        *   `date_publication` (DATE)

3.  **Insertion de données d'exemple :**
    *   Insérez au moins 5 articles avec des titres et des contenus variés. Assurez-vous que certains articles partagent des mots-clés ou des thèmes pour pouvoir tester la recherche.
    *   *Exemple de contenu :* "L'intelligence artificielle révolutionne le monde du travail. Les bases de données SQL restent fondamentales pour stocker ces informations." ou "Le développement web moderne utilise souvent des frameworks JavaScript. La performance des requêtes SQL est essentielle pour une bonne expérience utilisateur."
    *   **Astuce IA :** Demandez à votre IA de vous générer des exemples d'articles de blog sur des sujets technologiques, scientifiques ou d'actualité.

#### **Exercice 2 : Introduction à `to_tsvector`**

La fonction `to_tsvector` transforme un texte en un format optimisé pour la recherche Full-Text, appelé "tsvector". Elle effectue des opérations comme la suppression des mots vides (stop-words), la normalisation des mots (stemming) et l'enregistrement de leur position.

1.  **Expérimentation de base :**
    *   Exécutez la requête suivante et observez le résultat :
        ```sql
        SELECT to_tsvector('french', 'Ceci est un exemple de texte pour la recherche full-text en français.');
        ```
    *   Changez la configuration linguistique (`'english'`, `'spanish'`) et observez les différences.
    *   Testez avec des mots au pluriel, des verbes conjugués, et des mots vides (ex: "le", "la", "un").

2.  **Application aux données :**
    *   Sélectionnez le `titre` et le `contenu` de vos articles, et appliquez `to_tsvector` à chacun d'eux en utilisant la configuration linguistique `'french'`.
    *   *Question :* Pourquoi est-il important de choisir la bonne configuration linguistique ?

#### **Exercice 3 : Optimisation avec une colonne générée et un index GIN**

Stocker le `tsvector` directement dans la table et l'indexer améliore considérablement les performances de recherche.

1.  **Ajout d'une colonne `tsv_document` :**
    *   Ajoutez une nouvelle colonne nommée `tsv_document` de type `TSVECTOR` à votre table `articles`. Cette colonne sera une **colonne générée** (PostgreSQL 12+) à partir du `titre` et du `contenu`.
    *   Utilisez la fonction `setweight` pour donner plus d'importance aux mots trouvés dans le `titre` (poids 'A') par rapport au `contenu` (poids 'B').
    ```sql
    ALTER TABLE articles
    ADD COLUMN tsv_document TSVECTOR
    GENERATED ALWAYS AS (
        setweight(to_tsvector('french', titre), 'A') ||
        setweight(to_tsvector('french', contenu), 'B')
    ) STORED;
    ```
    *   *Note :* `STORED` signifie que la valeur est calculée et stockée physiquement, ce qui est essentiel pour l'indexation et la performance.

2.  **Création d'un index GIN :**
    *   Créez un index de type GIN (Generalized Inverted Index) sur la colonne `tsv_document`. Cet index est spécialement conçu pour la recherche Full-Text et est crucial pour des requêtes rapides.
    ```sql
    CREATE INDEX idx_articles_tsv ON articles USING GIN (tsv_document);
    ```
    *   *Question :* Sans cet index, comment se comporterait une recherche Full-Text sur une table de millions d'articles ?

#### **Exercice 4 : Utilisation de `to_tsquery` pour la recherche**

La fonction `to_tsquery` transforme une chaîne de caractères en un format de requête Full-Text (`tsquery`), permettant d'utiliser des opérateurs logiques.

1.  **Expérimentation de base :**
    *   Exécutez les requêtes suivantes et observez les `tsquery` générées :
        ```sql
        SELECT to_tsquery('french', 'recherche & texte');
        SELECT to_tsquery('french', 'intelligence | IA');
        SELECT to_tsquery('french', 'base & !données');
        SELECT to_tsquery('french', 'développement:*'); -- Recherche de préfixe
        ```
    *   Comprenez le rôle des opérateurs `&` (ET), `|` (OU), `!` (NON), et `:*` (préfixe).

2.  **Recherche d'articles :**
    *   Utilisez l'opérateur `@@` pour effectuer des recherches sur votre table `articles`.
    *   Trouvez tous les articles contenant les mots "intelligence" ET "artificielle".
    *   Trouvez tous les articles parlant de "SQL" OU de "PostgreSQL".
    *   Trouvez les articles qui contiennent "développement" mais PAS "web".
    *   Trouvez les articles dont un mot commence par "révolu" (ex: "révolution", "révolutionnaire").

#### **Exercice 5 : Améliorer la pertinence et la présentation des résultats**

1.  **Classement des résultats (`ts_rank`) :**
    *   Utilisez la fonction `ts_rank` (ou `ts_rank_cd` pour une version plus complexe) pour attribuer un score de pertinence à chaque article trouvé.
    *   Modifiez une de vos requêtes précédentes pour afficher le score de pertinence et trier les résultats par ordre décroissant de ce score.
    ```sql
    SELECT
        titre,
        ts_rank(tsv_document, to_tsquery('french', 'SQL & base')) AS score
    FROM
        articles
    WHERE
        tsv_document @@ to_tsquery('french', 'SQL & base')
    ORDER BY
        score DESC;
    ```
    *   *Question :* Comment les poids 'A' et 'B' définis dans votre `tsv_document` influencent-ils ce score de pertinence ?

2.  **Mise en évidence des mots-clés (`ts_headline`) :**
    *   Utilisez la fonction `ts_headline` pour afficher un extrait du `contenu` de l'article, avec les mots-clés de la recherche mis en évidence.
    ```sql
    SELECT
        titre,
        ts_headline('french', contenu, to_tsquery('french', 'intelligence & artificielle')) AS extrait
    FROM
        articles
    WHERE
        tsv_document @@ to_tsquery('french', 'intelligence & artificielle');
    ```
    *   Expérimentez avec les options de `ts_headline` (ex: `StartSel`, `StopSel`, `MaxWords`, `MinWords`).

#### **Exercice 6 : Requêtes avancées (Défi optionnel)**

1.  **Recherche de phrase exacte :**
    *   Comment rechercher l'expression exacte "base de données" (et non "base" et "données" séparément) ?
    *   *Indice :* Utilisez l'opérateur `<->`.

2.  **Recherche pondérée avancée :**
    *   Écrivez une requête qui recherche les articles où "performance" est dans le titre ET "optimisation" est dans le contenu.
    *   *Indice :* Vous pouvez construire des `tsquery` plus complexes ou utiliser des configurations de poids spécifiques.

3.  **Combinaison complexe :**
    *   Écrivez une requête qui trouve les articles parlant de "cloud" OU "serveur", mais qui mentionnent également "sécurité" dans le titre.

---

### **Validation du TP**

Pour valider ce TP, vous devez être capable de :
*   Créer une table avec une colonne `TSVECTOR` générée et un index GIN associé.
*   Insérer des données et les rendre "recherchables" par Full-Text.
*   Exécuter des requêtes de recherche Full-Text simples et complexes en utilisant `to_tsvector` et `to_tsquery`.
*   Classer les résultats par pertinence et afficher des extraits avec les mots-clés mis en évidence.

---

**Pour aller plus loin :**
*   Explorez la personnalisation des dictionnaires de recherche (ajout de synonymes, modification des stop-words).
*   Renseignez-vous sur les différentes configurations linguistiques disponibles et comment en créer de nouvelles.
*   Comparez les performances d'une recherche `LIKE` et d'une recherche Full-Text sur un grand volume de données.

Bon courage dans cette exploration de la recherche Full-Text !
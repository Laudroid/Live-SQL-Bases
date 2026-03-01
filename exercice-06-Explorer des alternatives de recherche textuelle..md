Bonjour !

Ce TP a pour objectif de vous familiariser avec des techniques de recherche textuelle avancées en SQL, au-delà du simple `LIKE`. Nous allons explorer les trigrammes pour la recherche de similarité et les expressions régulières pour des motifs plus complexes.

---

# TP : Recherche Textuelle Avancée - Trigrammes et Expressions Régulières

**Objectif du TP :**
*   Comprendre les limites de la recherche `LIKE` basique.
*   Découvrir et appliquer la recherche par trigrammes pour gérer les fautes de frappe et la similarité.
*   Maîtriser l'utilisation des expressions régulières pour des motifs de recherche complexes.
*   Évaluer la pertinence et les performances de chaque approche selon le scénario.

**Prérequis :**
*   Connaissances de base en SQL (création de tables, insertion de données, requêtes `SELECT` avec `WHERE`).
*   Un système de gestion de base de données (SGBD). **PostgreSQL est fortement recommandé** pour ce TP car il offre des fonctionnalités robustes et natives pour les trigrammes (`pg_trgm`) et les expressions régulières. Si vous utilisez un autre SGBD, les concepts restent valables, mais la syntaxe spécifique pourra varier (notamment pour les trigrammes qui sont souvent une extension).

---

#### **Partie 1 : Préparation de l'environnement**

1.  **Création de la base de données et de la table :**
    Créez une base de données nommée `tp_recherche_textuelle`.
    Dans cette base, créez une table `documents` avec les colonnes suivantes :
    *   `id` (SERIAL PRIMARY KEY)
    *   `titre` (VARCHAR(255))
    *   `contenu` (TEXT)

2.  **Insertion de données :**
    Insérez au moins 10-15 enregistrements variés dans la table `documents`. Incluez des titres et contenus avec :
    *   Des mots similaires mais pas identiques (ex: "développement web", "développement mobile", "développemnt web").
    *   Des fautes de frappe intentionnelles (ex: "programation", "algorithme", "algorythme").
    *   Des numéros de version (ex: "v1.0", "version 2.3").
    *   Des adresses email fictives (ex: "utilisateur@domaine.com").
    *   Des mots avec des caractères spéciaux ou des formats spécifiques.

    *Exemples d'insertions :*
    ```sql
    INSERT INTO documents (titre, contenu) VALUES
    ('Introduction au développement web', 'Ce document couvre les bases du développemnt web, incluant HTML, CSS et JavaScript.'),
    ('Développement mobile avec React Native', 'Explorez la création d''applications mobiles performantes avec React Native.'),
    ('Optimisation des bases de données SQL', 'Apprenez les techniques d''optimisation pour vos requêtes SQL et la structure de vos bases de données.'),
    ('Programmation Python pour débutants', 'Un guide complet pour la programation en Python, de la syntaxe de base aux concepts avancés.'),
    ('Algorithmes de tri et de recherche', 'Étude des principaux algorithmes de tri (bulle, rapide) et de recherche (binaire).'),
    ('Sécurité informatique v1.0', 'Les fondamentaux de la sécurité informatique, version 1.0.'),
    ('Contact support technique', 'Pour toute question, contactez-nous à support@monentreprise.com ou au 01-23-45-67-89.'),
    ('SQL Avancé : Fenêtrage et CTE', 'Plongez dans les fonctions de fenêtrage et les Common Table Expressions (CTE) en SQL.'),
    ('Les nouveautés de la version 2.3', 'Découvrez les améliorations et nouvelles fonctionnalités de notre logiciel en version 2.3.'),
    ('Gestion de projet Agile', 'Méthodologies Agile pour une gestion de projet efficace et flexible.'),
    ('Développement front-end avec Vue.js', 'Apprenez à construire des interfaces utilisateur dynamiques avec Vue.js.'),
    ('Bases de données NoSQL', 'Introduction aux bases de données NoSQL : MongoDB, Cassandra, Redis.'),
    ('Intelligence Artificielle et Machine Learning', 'Exploration des concepts clés de l''IA et du Machine Learning.'),
    ('Rédaction web et SEO', 'Optimisez votre contenu pour le web et les moteurs de recherche.'),
    ('Cloud Computing : AWS et Azure', 'Comparaison des services Cloud majeurs : Amazon Web Services et Microsoft Azure.');
    ```

3.  **Activation de l'extension `pg_trgm` (PostgreSQL uniquement) :**
    Pour utiliser les trigrammes, vous devez activer l'extension.
    ```sql
    CREATE EXTENSION pg_trgm;
    ```

---

#### **Partie 2 : Recherche avec `LIKE` (Le point de départ)**

1.  **Recherche exacte ou partielle :**
    *   Trouvez tous les documents dont le titre contient le mot "développement".
    *   Trouvez tous les documents dont le contenu contient "SQL".

2.  **Limites :**
    *   Tentez de trouver le document sur la "programmation" en cherchant "programation" (avec la faute de frappe). Que constatez-vous ?
    *   Comment trouveriez-vous un document qui parle de "développement" ou de "développemnt" (avec une faute) avec `LIKE` ?

---

#### **Partie 3 : Recherche par Trigrammes (Similarité)**

Les trigrammes permettent de mesurer la similarité entre deux chaînes de caractères en comparant leurs séquences de trois caractères. C'est idéal pour la recherche "floue" ou la correction de fautes de frappe.

1.  **Mesure de similarité :**
    *   Utilisez la fonction `SIMILARITY()` pour comparer la chaîne `'développement'` avec `'développemnt'` et `'développement web'`.
    *   Comparez `'programmation'` avec `'programation'`.
    *   Quel est le seuil de similarité par défaut ? (Indice : `SHOW pg_trgm.similarity_threshold;`)

2.  **Recherche de documents similaires :**
    *   Trouvez tous les documents dont le titre est *similaire* à `'développemnt web'` (avec la faute de frappe) avec un seuil de similarité raisonnable (par exemple, supérieur à 0.3).
    *   Trouvez tous les documents dont le contenu est similaire à `'algorythme'` (avec la faute de frappe).
    *   Utilisez l'opérateur `<->` (distance de trigrammes) pour trouver les 3 documents les plus similaires à `'SQL Avancé'` dans le titre.

3.  **Optimisation avec un index GIN :**
    La recherche par trigrammes peut être lente sur de grandes tables sans index.
    *   Créez un index GIN sur la colonne `titre` de votre table `documents` pour l'opérateur `gin_trgm_ops`.
        ```sql
        CREATE INDEX idx_titre_trgm ON documents USING GIN (titre gin_trgm_ops);
        ```
    *   Exécutez à nouveau une des requêtes de recherche par similarité sur le titre.
    *   Utilisez `EXPLAIN ANALYZE` sur votre requête avant et après la création de l'index. Que remarquez-vous en termes de performance ?

---

#### **Partie 4 : Recherche avec Expressions Régulières (Motifs complexes)**

Les expressions régulières (regex) permettent de rechercher des motifs complexes dans les chaînes de caractères.

1.  **Recherche de mots spécifiques (insensible à la casse) :**
    *   Trouvez tous les documents dont le titre contient le mot "SQL" ou "sql" (insensible à la casse). Utilisez l'opérateur `~*`.
    *   Trouvez tous les documents dont le contenu contient le mot "Python" mais pas "Java".

2.  **Recherche de numéros de version :**
    *   Trouvez tous les documents qui mentionnent un numéro de version au format "vX.Y" ou "version X.Y" (où X et Y sont des chiffres).
        *Indice : `\d` pour un chiffre, `\.` pour un point littéral.*

3.  **Recherche d'adresses email :**
    *   Trouvez tous les documents qui contiennent une adresse email simple (ex: `quelquechose@domaine.com`).
        *Indice : `[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}` est une regex courante pour les emails.*

4.  **Extraction de données (PostgreSQL `REGEXP_MATCHES`) :**
    *   Pour les documents contenant une adresse email, extrayez l'adresse email elle-même.
    *   Pour les documents mentionnant une version, extrayez le numéro de version complet (ex: "v1.0" ou "2.3").

---

#### **Partie 5 : Réflexion et Comparaison**

1.  **Scénarios d'utilisation :**
    *   Dans quels scénarios privilégieriez-vous la recherche par trigrammes plutôt que les expressions régulières ?
    *   Dans quels scénarios les expressions régulières sont-elles indispensables et les trigrammes inefficaces ?
    *   Quand le simple `LIKE` est-il suffisant ?

2.  **Performance et Indexation :**
    *   Quel est l'impact de l'index GIN sur les requêtes utilisant les trigrammes ?
    *   Existe-t-il des index spécifiques pour optimiser les recherches par expressions régulières (en PostgreSQL ou d'autres SGBD) ? Faites une petite recherche si nécessaire.

3.  **Combinaison des techniques :**
    *   Imaginez un scénario où vous pourriez combiner trigrammes et expressions régulières pour une recherche plus robuste. Décrivez-le et proposez une ébauche de requête.

---

N'hésitez pas à utiliser la documentation de votre SGBD ou des ressources en ligne pour vous aider à construire vos requêtes. L'objectif est de comprendre les outils à votre disposition et de savoir quand les utiliser.

Bonne exploration !
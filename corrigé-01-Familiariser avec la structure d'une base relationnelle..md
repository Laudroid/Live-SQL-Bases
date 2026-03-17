Voici la solution détaillée du TP sur la découverte de la structure relationnelle d'une base de données.

---

### **Solution du TP : Découverte de la Structure Relationnelle d'une Base de Données**

#### **1. Identification des Tables**

Les tables présentes dans cette base de données sont :

*   `Clients`
*   `Produits`
*   `Commandes`
*   `Lignes_Commande`

#### **2. Identification des Colonnes**

Voici les colonnes pour chaque table :

*   **Table : `Clients`**
    *   `id_client`
    *   `nom`
    *   `email`
    *   `adresse`

*   **Table : `Produits`**
    *   `id_produit`
    *   `nom_produit`
    *   `description`
    *   `prix_unitaire`

*   **Table : `Commandes`**
    *   `id_commande`
    *   `id_client`
    *   `date_commande`
    *   `statut`

*   **Table : `Lignes_Commande`**
    *   `id_ligne`
    *   `id_commande`
    *   `id_produit`
    *   `quantite`
    *   `prix_total_ligne`

#### **3. Identification des Clés Primaires (PK)**

Voici les clés primaires pour chaque table :

*   **Table : `Clients`** : `id_client`
*   **Table : `Produits`** : `id_produit`
*   **Table : `Commandes`** : `id_commande`
*   **Table : `Lignes_Commande`** : `id_ligne`

**Rôle d'une Clé Primaire (PK) :**
Une clé primaire est une colonne (ou un ensemble de colonnes) qui identifie de manière unique chaque enregistrement (ligne) dans une table. Elle garantit que chaque ligne est distincte et peut être référencée sans ambiguïté. Une clé primaire ne peut pas contenir de valeurs nulles et chaque valeur doit être unique. Elle est essentielle pour l'intégrité des données et pour établir des relations avec d'autres tables.

#### **4. Identification des Clés Étrangères (FK)**

Voici les clés étrangères présentes dans le schéma :

*   **Clé Étrangère 1 :**
    *   **Table :** `Commandes`
    *   **Colonne FK :** `id_client`
    *   **Référence à :** `Clients.id_client` (Clé primaire de la table `Clients`)

*   **Clé Étrangère 2 :**
    *   **Table :** `Lignes_Commande`
    *   **Colonne FK :** `id_commande`
    *   **Référence à :** `Commandes.id_commande` (Clé primaire de la table `Commandes`)

*   **Clé Étrangère 3 :**
    *   **Table : `Lignes_Commande`**
    *   **Colonne FK :** `id_produit`
    *   **Référence à :** `Produits.id_produit` (Clé primaire de la table `Produits`)

**Rôle d'une Clé Étrangère (FK) :**
Une clé étrangère est une colonne (ou un ensemble de colonnes) dans une table qui fait référence à la clé primaire d'une autre table (ou parfois de la même table). Son rôle principal est d'établir et de maintenir des liens entre les tables, assurant ainsi l'intégrité référentielle. Cela signifie qu'une valeur dans la colonne de la clé étrangère doit exister comme valeur de clé primaire dans la table référencée. Les clés étrangères sont fondamentales pour modéliser les relations entre les entités dans une base de données relationnelle.

#### **5. Relations entre les Tables**

Décrivons les liens entre les tables `Clients`, `Commandes` et `Lignes_Commande` :

*   **Relation entre `Clients` et `Commandes` :**
    *   La table `Commandes` contient la clé étrangère `id_client`, qui fait référence à la clé primaire `id_client` de la table `Clients`.
    *   Cela signifie qu'une commande est toujours associée à un client spécifique. Un client peut passer plusieurs commandes, mais chaque commande est passée par un seul client. C'est une relation de type "un à plusieurs" (un client a plusieurs commandes).

*   **Relation entre `Commandes` et `Lignes_Commande` :**
    *   La table `Lignes_Commande` contient la clé étrangère `id_commande`, qui fait référence à la clé primaire `id_commande` de la table `Commandes`.
    *   Cela indique que chaque ligne de commande appartient à une commande spécifique. Une commande peut contenir plusieurs lignes de commande (par exemple, si un client achète plusieurs articles différents dans une seule commande), mais chaque ligne de commande est liée à une seule commande. C'est également une relation de type "un à plusieurs" (une commande a plusieurs lignes de commande).

**Intérêt de ces liens pour une base de données :**

L'intérêt principal de ces liens est de structurer les données de manière logique et efficace, en évitant la redondance et en garantissant la cohérence.

1.  **Éviter la redondance des données :** Au lieu de répéter toutes les informations du client (nom, email, adresse) dans chaque enregistrement de commande, nous stockons ces informations une seule fois dans la table `Clients` et nous y faisons référence via `id_client` dans la table `Commandes`.
2.  **Assurer l'intégrité des données :** Les clés étrangères garantissent qu'une commande ne peut pas être associée à un `id_client` qui n'existe pas dans la table `Clients`. De même, une ligne de commande ne peut pas être associée à un `id_commande` inexistant. Cela maintient la validité et la fiabilité des informations.
3.  **Faciliter la récupération des données :** Ces liens permettent de joindre facilement les tables pour obtenir des informations complètes. Par exemple, on peut récupérer le nom du client qui a passé une commande spécifique, ou tous les produits inclus dans une commande, en reliant les tables entre elles.
4.  **Modéliser le monde réel :** Les relations entre les tables reflètent les relations naturelles entre les entités du monde réel (un client passe des commandes, une commande contient des produits). Cela rend la base de données plus intuitive et plus facile à comprendre et à gérer.

En résumé, ces liens sont la pierre angulaire des bases de données relationnelles, permettant une organisation robuste et une manipulation flexible des informations.
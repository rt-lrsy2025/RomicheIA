# N8N : Guide de Configuration des Workflows

---

## **Téléchargement des Workflows**

- Télécharger les workflows dans l’espace dédié **XXXXX**.
- Pour mieux comprendre chaque étape, consulter les **settings** de chaque nœud : des notes explicatives y sont souvent présentes.

---

## **Guide d’Installation**

Pour configurer **Qdrant** et **N8N**, assurez-vous d’indiquer les **credentials** vers :

- La base de données (BDD)
- Le service **Uncloud**

---

## **Fonctionnement des Workflows**

---

### **Workflow du RAG**

#### **Fonctionnement**

Ce workflow s’occupe du traitement des requêtes utilisateurs.

- **Entrée "Input"** : Contient la requête de l’utilisateur envoyée depuis **OpenWebUI**.
- **Question and Answer Chain** : Nœud natif de N8N optimisé pour les réponses RAG. Il contient un **prompt** expliquant son rôle, ainsi que la requête utilisateur.
- **Modèle LLM** : Utilisé pour chercher des données dans la base Qdrant.
- **Retriever** :
  - Un second modèle LLM reformule la question utilisateur en plusieurs questions similaires pour couvrir tous les aspects.
  - Qdrant est interrogé plusieurs fois pour récupérer un ensemble de vecteurs correspondant à des informations présentes dans les documents (texte, paragraphes).
  - Le **reranking** sélectionne les résultats les plus pertinents.
  - Les données sont renvoyées au modèle **Ollama Chat Model** pour formuler la réponse finale.
- **Webhook** :
  - Permet la communication entre OpenWebUI et N8N.
  - Le webhook récupère le message de l’utilisateur et envoie la réponse via une requête HTTP.
- **Module OpenWebUI** :
  - Un module spécifique est installé sur OpenWebUI pour connecter l’interface graphique du chat aux webhooks de N8N.

---

### **Workflow d’Ingestion de Données**

#### **Fonctionnement**

Ce workflow automatise la création de la base de données vectorielle, utilisé pour la première vectorisation.

- **Connexion à Uncloud** :
  - Accède à des dossiers spécifiques (Doc IUT, Test, IUT Tab Excel).
- **Nœud de Routage (Switch)** :
  - Dirige les fichiers selon leur chemin d’accès.
- **Liste, Boucle et Téléchargement** :
  - Chaque document est listé, bouclé et téléchargé.
- **Vérification des Doublons** :
  - Avant l’indexation, le workflow interroge Qdrant pour vérifier si le document existe déjà.
- **Indexation** :
  - Si le fichier est nouveau, il est découpé et vectorisé via Ollama, puis injecté dans Qdrant.
- **Tableau de Suivi** :
  - Chaque ajout réussi est consigné dans un tableau de suivi N8N pour le monitoring des documents intégrés.
- **Gestion des Doublons** :
  - Les doublons sont ignorés grâce à une étape de "No Operation".

---

### **Workflow d’Actualisation des Données**

#### **Fonctionnement**

Ce workflow s’occupe de mettre à jour les documents internes.

- **Détection des Modifications** :
  - Vérifie périodiquement la présence de nouveaux fichiers ou de modifications sur le cloud.
  - Compare l’horodatage de dernière modification avec le déclenchement de l’horloge (toutes les 5 minutes).
- **Consignation** :
  - Si un changement est détecté, le fichier est consigné dans un tableau de suivi N8N.
- **Routage Spécifique** :
  - Le fichier est dirigé vers un routage spécifique selon son dossier d’origine (IUT, Test ou IUT_Tableau).
- **Vérification d’Indexation** :
  - Le système interroge la base de données pour vérifier si le document est déjà indexé.
  - Si c’est le cas, les anciens vecteurs associés sont supprimés pour permettre une ré-indexation propre avec les données mises à jour.
- **Nouveaux Documents** :
  - Si le document est nouveau, il est directement vectorisé et ajouté à la base Qdrant.
  - Une mise à jour correspondante est effectuée dans le tableau de suivi des documents actifs.
- **Cycle sans Action** :
  - Si aucune modification n’est détectée dans le créneau des 5 minutes, le workflow termine son cycle sans action.

---

## **Paramètres des Workflows**

---

### **Workflow Database**

#### **Paramètres des Nœuds**

#### **1. Nœud Nextcloud**

- **Credentials** : Remplir avec un compte **Uncloud** valide.
- **URL** : `https://uncloud.univ-nantes.fr/remote.php/webdav`
- **Ressource** : `Folder`
- **Operation** : `List`
- **Path** : Chemin vers le dossier (ex: `/IUT/Departement_RT/2026/`)

#### **2. Qdrant Vector Store**

- **Collection** : Sélectionner la BDD de votre choix.

---

#### **3. Default Data Loader**

- **Type of Data** : `Binary`
- **Mode** : `Load Specific Data`
- **Input Data Field Name** : `{{ $('Download a file').item.json.path }}`
- **Options** :
  - **Metadata Name** : `filename`
  - **Value** : `{{ $('Download a file').item.json.path }}` *(Nom du fichier en tag)*

#### **4. Character Text Splitter**

- **Separator** : `.`
- **Chunk Size** : `800`
- **Overlap** : `200`

#### **5. Embedding**

- **Modèle** : `embeddinggemma:latest`

---

## **Prompt RAG**

```markdown
You are a **RAG (Retrieval-Augmented Generation) assistant** connected to a **Qdrant vector database**.
Your task is to answer questions **exclusively** using the provided Qdrant context.
**Never** use external knowledge or assumptions.

1. **Strict Context Adherence**:
   - Use **only** the retrieved Qdrant entries to answer.
   - If the answer is not in the context, respond:
     *"I don’t know. No relevant documents were retrieved from the Qdrant database."*

2. **Mandatory Source Citation**:
   - For **every answer**, explicitly cite:
     - **Filename** (e.g., `RT3/SAE54ProjetRechercheetInnovation/Tableau/Listes_Contacts_Stage_Alternance.xlsx`)
     - **ID** (e.g., `5eacad22-d8f2-4e6f-a437-e20544a57f77`)

   - Example citation format:
     *"According to [Filename: RT3/SAE54ProjetRechercheetInnovation/Tableau/Listes_Contacts_Stage_Alternance.xlsx, ID: 5eacad22-d8f2-4e6f-a437-e20544a57f77], the contact emails for Nantes Université are: patricia.marolleau@univ-nantes.fr, lucile.deletre@univ-nantes.fr."*

3. **Metadata Utilization**:
   - If the context includes metadata (e.g., `Organisme / Entité`, `lines`), use it to **enhance clarity**.

   - Example:
     *"From [Filename: RT3/SAE54ProjetRechercheetInnovation/Tableau/Listes_Contacts_Stage_Alternance.xlsx, ID: 1088c972-16f8-48ab-a053-c25d25c71e24, Lines 1-1], the email addresses for DGFIP (Finances publiques) are: gwenaelle.nicolet@impots.gouv.fr, valerie.soubra@impots.gouv.fr."*

4. **Ambiguity Handling**:
   - If the context is unclear or insufficient, respond:
     *"The provided context lacks sufficient information to answer confidently."*

5. **Data Integrity**:
   - **NEVER** alter the original data. Format it for clarity, but preserve all details (emails, names, entities).

   - **NEVER** create information if you don’t know the filename, source document, or text information. Say it explicitly!
```

---

### **Ollama Chat Model**

- **Options** :
  - **Sampling Temperature** : `0.2`
  - **Number of CPU Threads** : `10`

---

### **Ollama Model**

- **Options** :
  - **Sampling Temperature** : `0.2`
  - **Number of CPU Threads** : `10`

---

### **Multi Query Retriever**

- **Query Count** : `3` interrogations
- **Vector Retriever** : `4`

---

### **Qdrant Vector Store**

- **Rerank Result** : Activé
- **Qdrant Collection** : Choisir la collection créée
- **Operation Mode** : `Retrieve document as vector store for chain / tool`

---

### **Ollama Reranker**

- **TopK** : `20`
- **Task Instruction** : `retrieve relevant passages that answer the query`

---

## **Remarques**

- Pour toute question ou problème, vérifier les **logs** et les **paramètres** de chaque nœud.
- Assurez-vous que les **credentials** sont correctement configurés pour éviter les erreurs de connexion.

---

N’hésitez pas à me demander si vous souhaitez ajouter ou modifier des sections !

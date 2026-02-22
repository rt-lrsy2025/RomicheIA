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

## **Workflow Database**

### **Paramètres des Nœuds**

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

## **Workflow RAG**

### **Prompt RAG**

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

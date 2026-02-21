# RomicheIA
<img width="300" height="300" alt="image" src="https://github.com/user-attachments/assets/2e46e77c-1980-45f7-b3f6-b5f7fb74b0ee" />

# Romiche IA
Version : 1.0 (Bêta)
Dernière mise à jour : 21 février 2026

**Prototype de RAG IA autonome et local**  
Développé par des étudiants de l'**IUT de La Roche-sur-Yon**.

---

## Description

Romiche IA est un projet visant à créer un système de **Retrieval-Augmented Generation (RAG)** entièrement local et autonome. Ce prototype permet de gérer des workflows, des bases de données vectorielles, et des modèles de langage, le tout hébergé en local pour une utilisation sécurisée et personnalisable.

---

## Structure du projet

### Dossiers


| Dossier  | Description                                     |
| -------- | ----------------------------------------------- |
| `Backup` | Contient les sauvegardes des workflows **N8N**. |


---

# Ce que la solution est capable de faire aujourd'hui


| Catégorie                               | Essentiel                                                          | Important                                                                                      |
| --------------------------------------- | ------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------- |
| **Traitement de Documents & Résumé**    | ✅ Synthèse de documents                                            | 🟠 Interface facile d’utilisation                                                              |
|                                         | ✅ Obtenir un résumé rapide de documents longs                      | 🟠 Réaliser une analyse synthétique de réflexion de ces documents                              |
| **Recherche et Extraction Précise**     | ✅ Traitement de Documents & Résumé                                 | ✅ Accéder rapidement à une information fiable sur le domaine de l’IUT                          |
|                                         | ✅ Recherche et Extraction Précise de données au sein d’un document | (exemple : Informations sur l’IUT siège social, Informations sur l’offre de l’IUT...)  |
| **Analyse, Modélisation & Décisionnel** | ❌ Non testé                                                        | ❌ Analyse, Modélisation & Décisionnel (Demandes Complexes)                                     |
| **(Demandes Complexes)**                |                                                                    | ❌ Déterminer la capacité d’accueil, des alternants, étudiant à l’étranger |
|                                         |                                                                    | ❌ En fonction des capacités d’accueil, définir le nombre de professeurs à avoir                |


### Légende :

- ✅ Validé
- 🟠 Résultat Moyen
- ❌ Non testé

---

## Applications Docker

Le projet utilise **Docker Compose** pour déployer les applications suivantes :

- **Qdrant** : Base de données vectorielle pour le stockage et la recherche d'embeddings.
- **N8N** : Outil d'automatisation des workflows.
- **OpenLDAP** : Service d'annuaire pour la gestion des utilisateurs et des permissions.
- **OpenWebUI** : Interface graphique pour interagir avec les modèles de langage.

---

## Détails techniques

### Modèles utilisés


| Type          | Modèle                                          |
| ------------- | ----------------------------------------------- |
| **Reranking** | BGE-Reranker                                    |
| **LLM**       | Mistral3B, Mistral 8B, Llama3.2:3B, Llama3.2:1B |
| **Embedding** | embedding-gemma                                 |


### Machine de test


| Composant        | Spécifications                                    |
| ---------------- | ------------------------------------------------- |
| **Rôle**         | Serveur                                           |
| **Applications** | Docker (Ollama, OpenLDAP, OpenWebUI, N8N, Qdrant) |
| **CPU**          | Intel® Core™ i5-12500 (3.00 GHz - 4.60 GHz)       |
| **RAM**          | 32 Go - DDR5                                      |
| **GPU**          | Intel UHD Graphics 770                            |
| **Stockage**     | SSD SATA 512 Go 2.5"                              |
| **OS**           | Windows 11                                        |


---

## Infrastructure

L'infrastructure repose sur une architecture locale avec :

- **Docker** pour la conteneurisation des services.
- **Ollama** pour le déploiement des modèles de langage.
- **Qdrant** pour la gestion des embeddings et la recherche vectorielle.
- **OpenLDAP** pour la gestion centralisée des utilisateurs.

---

## Interface graphique

**OpenWebUI** est utilisée comme interface principale pour :

- Interagir avec les modèles de langage.
- Visualiser et gérer les workflows.
- Accéder aux fonctionnalités de recherche et de génération.

<img width="668" height="435" alt="image" src="https://github.com/user-attachments/assets/c99a93f2-45c9-4562-91bc-b376a7ccd030" />

---

## Tutoriel d'installation

*(PROCHAINEMENT...)*

---

## Sources

*(PROCHAINEMENT...)*

---

## Crédits

Projet réalisé par les étudiants de l'**IUT de La Roche-sur-Yon**.

---

## Qualification de la maquette

Ce prototype est une **maquette fonctionnelle** destinée à démontrer les capacités d'un système RAG local. Il est conçu pour des tests et des évaluations techniques, et peut être amélioré pour une utilisation en production.
🚧 PROCHAINEMENT... 🚧 (AVRIL 2026)

---


---
## Manipulations hors docker compose
- Rajouter les utilisateurs LDAP
- Les valider par le compte administrateur OpenWebUI

dans un dossier util.ldif

```
dn: ou=users,dc=reprive,dc=rt  
objectClass: organizationalUnit  
ou: users  
  
dn: uid=util1,ou=users,dc=rtprive,dc=rt  
objectClass: inetOrgPerson  
cn: util1  
sn: util1
uid: util1  
mail: util1@rtprive.rt  
userPassword: {PLAIN}rtlry
```

Pour rajouter l'utilisateur :
```
docker cp seed.ldif openldap:/seed.ldif  
docker exec openldap ldapadd -x -D "cn=admin,dc=example,dc=org" -w admin -f /seed.ldif
```

si le mot de passe ne marche pas on peut le changer dans le conteneur docker OpenLDAP
```
ldappasswd -x -D "cn=admin,dc=rtprive,dc=rt" -W -S "uid=util1,ou=users,dc=rtprive,dc=rt"
```
---
#CREDITS 
Projet universitaire IUT La Roche-sur-Yon
2025-2026
<img width="150" height="150" alt="image" src="https://www.google.com/imgres?q=iut%20la%20roche%20sur%20yon%20logo&imgurl=https%3A%2F%2Fpbs.twimg.com%2Fprofile_images%2F1511274108432101379%2FsqZG5nLE_400x400.png&imgrefurl=https%3A%2F%2Fx.com%2FIUT_LRSY&docid=J0PViPoMK9wiGM&tbnid=sYkuRY0KU8hADM&vet=12ahUKEwiXlrnro9ySAxVDK_sDHXAWEzYQnPAOegQIGBAB..i&w=400&h=400&hcb=2&ved=2ahUKEwiXlrnro9ySAxVDK_sDHXAWEzYQnPAOegQIGBAB"  />

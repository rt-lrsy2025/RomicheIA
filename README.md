# RomicheIA
<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/2e46e77c-1980-45f7-b3f6-b5f7fb74b0ee" />

Romiche IA est un prototype de RAG IA autonome et local créer par des étudiants de l'IUT de la Roche-sur-Yon.
Vous êtes sur la page principal du projet.

Les dossiers : 
Backup --> Contient les backup des workflows N8N.


Que contient docker compose comme application ?
- Qdrant
- N8N
- Openldap
- OpenWebUI


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

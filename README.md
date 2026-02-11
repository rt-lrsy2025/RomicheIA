# RomicheIA
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
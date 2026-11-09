# REDIS-N3-07 — Incident clients, ACL & sécurité

**Quand ?** `NOAUTH`, `NOPERM`, refus de connexions, buffers anormaux.

### Indicateurs globaux (nœud ciblé)
```text
INFO clients
INFO stats
ACL WHOAMI
```

### Diagnostics sensibles : autorisation et sortie expurgée
```text
ACL LOG 5
CLIENT LIST TYPE NORMAL
```

### Lecture et décision
- `ACL WHOAMI` désigne **la session interrogée**, pas le client en échec.
- `ACL LOG` distingue erreurs d'authentification et refus ACL ; ne pas exécuter `ACL LOG RESET`.
- `rejected_connections` reflète des refus liés à **maxclients** ; ne pas assimiler à des erreurs de mot de passe.
- `CLIENT LIST` est O(N), sensible (IP, noms, buffers) ; adapter à la charge et aux droits.
- Vérifier dérive ACL, rotation de certificat, TLS, pool client et déploiements ; pas d'élargissement de droits en urgence sans validation.
- **Action N3 :** changement de compte/ACL/secret uniquement sous contrôle de sécurité, avec rollback approuvé.
- **Validation :** connexions applicatives et droits attendus, disparition des erreurs, aucune permission excessive.

**Escalade :** incident de sécurité, secrets compromis, indisponibilité massive ou rotation critique.

[Sources : ACL LOG](https://redis.io/docs/latest/commands/acl-log/) · [CLIENT LIST](https://redis.io/docs/latest/commands/client-list/)

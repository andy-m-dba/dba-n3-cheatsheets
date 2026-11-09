# REDIS-N3-06 — Sauvegardes, RPO/RTO & PRA

**Quand ?** Perte de clés, crash majeur ou restauration envisagée.

### Contexte de persistance (nœud concerné)
```text
INFO persistence
INFO replication
LASTSAVE
```

### Décision de reprise
- `LASTSAVE` = date du dernier **RDB réussi sur ce nœud** ; ne prouve ni export ni restauration testée.
- AOF et RDB sont des mécanismes de persistance **locaux**, non une sauvegarde externe indépendante.
- Définir source, point cible, RPO autorisé, RTO, politique TTL et risque de données expirées à la reprise.
- En Cluster, vérifier la couverture de **tous les shards**, les rôles, la cohérence des copies et le remappage prévu.
- Redis managé : utiliser les mécanismes de backup/restauration propres à l'offre ; ne pas présumer d'un PITR universel.
- **Action N3 :** restauration sur environnement isolé d'abord, puis validation formelle de la bascule et du retour.
- **Validation :** RTO réel, perte constatée, clés métier attendues et parcours applicatifs validés.

**Escalade :** backup incomplet, données divergentes, perte non autorisée ou restauration Cluster non maîtrisée.

[Sources : persistance et backups](https://redis.io/docs/latest/management/persistence/) · [LASTSAVE](https://redis.io/docs/latest/commands/lastsave/)

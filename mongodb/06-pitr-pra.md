# MDB-N3-06 — Backups, PITR & PRA

**Quand ?** Corruption, erreur humaine, perte de cluster, RPO/RTO à tenir.

### Identifier la topologie (si connexion possible)
```javascript
const h = db.adminCommand({ hello: 1 });
({ replicaSet: h.setName, mongos: h.msg === "isdbgrid" })
```

### Vérifier dans l'outil de sauvegarde
Atlas / Ops Manager / solution self-managed : point valide, fréquence et rétention, copie distante, fenêtre PITR, versions, chiffrage, couverture de chaque shard.

### Décision
- Snapshot `Completed` ≠ restauration testée. Le `mongodump` seul ne fournit pas un PITR automatique.
- Atlas **Continuous Cloud Backup**, s'il est activé et disponible sur l'offre, conserve l'historique nécessaire dans sa fenêtre PIT.
- En cluster sharded, vérifier la cohérence inter-shards **et la compatibilité de topologie** du snapshot cible.
- **Avant restauration :** cible horodatée, fuseau, RPO accepté, accès aux clés, capacité et plan de retour.
- **Action N3 :** répétition en environnement isolé puis décision formelle pour la production.
- **Validation :** RTO observé, données métier cohérentes et connexions applicatives vérifiées.

**Escalade :** point de restauration absent, chiffrement inaccessible, sharding incompatible ou perte non autorisée.

[Sources : Atlas Backup](https://www.mongodb.com/docs/atlas/backup/cloud-backup/overview/) · [PITR](https://www.mongodb.com/docs/atlas/recover-pit-continuous-cloud-backup/)

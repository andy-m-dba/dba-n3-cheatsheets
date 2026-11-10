# CASS-N3-07 — Snapshots, restauration & PRA

**Quand ?** Perte de données, cluster à restaurer, RPO/RTO à confirmer.

### Contrôles par nœud (JMX autorisé)
```bash
nodetool -h HOST listsnapshots
nodetool -h HOST statusbackup
nodetool -h HOST status
```

### Décision de reprise
- `listsnapshots` affiche des snapshots **locaux** ; ne prouve ni export externe ni couverture de tous les replicas/DC.
- `statusbackup` indique si l'incrémental est **activé**, pas si un backup a réussi.
- Inventorier snapshots, SSTables incrémentales, schéma/keyspaces, versions, tokens et éventuels commitlogs utiles.
- Une copie de snapshots non coordonnée entre les nœuds peut laisser des états temporels différents ; établir la cohérence de la restauration.
- Définir point de reprise réaliste, perte admise, topologie cible et procédure de remise en réplication ; **ne pas promettre un PITR natif universel**.
- **Action N3 :** rejouer le plan en environnement isolé avant autorisation de production. Aucun `clearsnapshot`, remplacement de fichiers ou démarrage forcé par réflexe.
- **Validation :** RTO mesuré, RPO constaté, couverture des données et cohérence métier vérifiées.

**Escalade :** backups manquants, topologie incompatible, corruption ou perte non autorisée.

[Sources : sauvegardes](https://cassandra.apache.org/doc/stable/cassandra/managing/operating/backups.html) · [listsnapshots](https://cassandra.apache.org/doc/latest/cassandra/managing/tools/nodetool/listsnapshots.html)

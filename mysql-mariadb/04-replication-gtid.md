# MYSQL-N3-04 — Réplication, GTID & divergence

**Quand ?** Erreur SQL/IO de réplication, GTID manquants, réplica incohérent.

### MySQL 8.4 — sur le réplica
```sql
SHOW REPLICA STATUS\G
SELECT @@GLOBAL.gtid_executed, @@GLOBAL.gtid_purged;
```

### MariaDB 10.11/11.x — canaux et GTID
```sql
SHOW ALL SLAVES STATUS\G
SELECT @@GLOBAL.gtid_current_pos, @@GLOBAL.gtid_slave_pos,
       @@GLOBAL.gtid_binlog_pos;
```

### Lecture et décision
- Les **formats GTID sont différents** : MySQL `UUID:interval`, MariaDB `domain-server-sequence`. Ne jamais les comparer directement.
- `Seconds_Behind_*` à zéro n'établit pas la complétude, surtout si le thread IO est en retard.
- Confronter erreurs expurgées, canaux, binlogs disponibles, GTID et transactions exécutées localement.
- **Action N3 :** qualifier données manquantes/errantes avant reconstruction. Pas de skip, reset GTID ou modification de source sans décision documentée.
- **Validation :** réception/application actives, positions/GTID cohérents et données métier confirmées.

**Escalade :** transactions errantes, GTID purgés, binlogs absents ou réinitialisation de réplica.

[Sources : MySQL GTID](https://dev.mysql.com/doc/refman/8.4/en/replication-options-gtids.html) · [MariaDB GTID](https://mariadb.com/docs/server/ha-and-performance/standard-replication/gtid)

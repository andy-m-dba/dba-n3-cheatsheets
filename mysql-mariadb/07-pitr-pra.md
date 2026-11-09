# MYSQL-N3-07 — Backups, binlogs, PITR & PRA

**Quand ?** Restauration, erreur humaine, données corrompues, RPO/RTO à valider.

### Binlog disponible ? (droits de supervision adaptés)
```sql
SHOW GLOBAL VARIABLES LIKE 'log_bin';
SHOW BINARY LOGS;
```

### Position sur le primaire MySQL 8.4
```sql
SHOW BINARY LOG STATUS;
```

### Position sur le primaire MariaDB 10.11/11.x
```sql
SHOW BINLOG STATUS;
```

### Décision de reprise
- Définir **point cible**, fuseau, RPO/RTO acceptés et périmètre des bases/tableaux.
- Vérifier la chaîne complète : full/incr physique ou logique, **binlogs**, GTID/positions, cohérence, stockage externe et clés.
- Un job « réussi » ne prouve pas la restaurabilité ; répéter dans un **environnement isolé**.
- `mysqlbinlog` / `mariadb-binlog` peuvent rejouer des écritures ; **jamais en copier-coller sur production**. Pour une PITR fiable, valider les limites par **positions d'événements**, pas le seul horaire.
- **Action N3 :** simulation complète, plan de bascule approuvé, retour documenté.
- **Validation :** RTO observé, RPO réel, cohérence et parcours applicatif testés.

**Escalade :** binlog/backup manquant, position ambiguë ou perte métier non autorisée. Aucune purge de journaux.

[Sources : MySQL PITR](https://dev.mysql.com/doc/refman/8.4/en/point-in-time-recovery-positions.html) · [MariaDB PITR](https://mariadb.com/docs/server/server-usage/backup-and-restore/mariadb-backup/point-in-time-recovery-pitr-mariadb-backup)

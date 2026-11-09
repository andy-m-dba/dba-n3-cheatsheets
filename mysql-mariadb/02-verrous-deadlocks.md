# MYSQL-N3-02 — Deadlocks & transactions bloquantes

**Quand ?** Erreurs 1213/1205 répétées, sessions bloquées, rollback long.

### MySQL 8.x — dépendances de verrous InnoDB
```sql
SELECT REQUESTING_ENGINE_TRANSACTION_ID AS attente,
       BLOCKING_ENGINE_TRANSACTION_ID AS bloqueur
FROM performance_schema.data_lock_waits LIMIT 15;
```

### MariaDB 10.11/11.x — verrous InnoDB
```sql
SELECT requesting_trx_id AS attente, blocking_trx_id AS bloqueur
FROM information_schema.INNODB_LOCK_WAITS LIMIT 15;
```

### Dernier deadlock (avec privilège PROCESS, sortie sensible)
```sql
SHOW ENGINE INNODB STATUS\G
```

### Lecture et décision
- Un deadlock InnoDB annule normalement **la transaction victime** ; un lock wait timeout annule en général **la seule instruction**, selon configuration.
- `SHOW ENGINE` donne un dernier deadlock, pas un historique exhaustif ; expurger SQL et identifiants.
- **Action N3 :** reconstruire le graphe, l'ordre des verrous et l'isolation ; corriger requête/transaction avec l'application, sans `KILL` réflexe.
- **Validation :** erreurs 1213/1205 et durée des transactions en baisse sous charge comparable.

**Escalade :** XA, transactions critiques, changement d'isolation global ou rollback à impact élevé.

[Sources : locks MySQL](https://dev.mysql.com/doc/refman/8.4/en/performance-schema-data-lock-waits-table.html) · [MariaDB](https://mariadb.com/docs/server/reference/system-tables/information-schema/information-schema-tables/information-schema-innodb-tables/information-schema-innodb_lock_waits-table)

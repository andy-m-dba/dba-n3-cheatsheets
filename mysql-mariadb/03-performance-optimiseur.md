# MYSQL-N3-03 — RCA performance & plans SQL

**Quand ?** Régression de plan, hausse CPU/I/O ou latence p99 durable.

### Si Performance Schema est activé
```sql
SHOW GLOBAL VARIABLES LIKE 'performance_schema';
SELECT DIGEST, COUNT_STAR,
       ROUND(SUM_TIMER_WAIT/1e12,1) AS total_s
FROM performance_schema.events_statements_summary_by_digest
WHERE DIGEST IS NOT NULL
ORDER BY SUM_TIMER_WAIT DESC LIMIT 15;
```

### Exemple de plan estimé (requête de catalogue)
```sql
EXPLAIN FORMAT=JSON
SELECT * FROM information_schema.TABLES
WHERE TABLE_SCHEMA = 'mysql';
```

### Lecture et décision
- `SUM_TIMER_WAIT` est en **picosecondes** et cumulatif ; comparer des deltas horodatés.
- Instrumentation absente ou désactivée : pas de conclusion ; MariaDB peut la désactiver par défaut.
- `EXPLAIN` = estimations. MySQL `EXPLAIN ANALYZE` et MariaDB `ANALYZE` **exécutent** la requête : éviter en production sans accord.
- Un full scan n'est pas nécessairement la cause ; confronter index, volumes, cardinalités et changements.
- **Action N3 :** tester un seul index/plan/hypothèse après approbation et rollback.
- **Validation :** latence applicative, temps SQL et consommation sous trafic comparable.

**Escalade :** changement massif de schéma, régression inexpliquée ou instrumentation à modifier.

[Sources : MySQL](https://dev.mysql.com/doc/refman/8.4/en/performance-schema-statement-summary-tables.html) · [MariaDB](https://mariadb.com/docs/server/reference/sql-statements/administrative-sql-statements/analyze-and-explain-statements/)

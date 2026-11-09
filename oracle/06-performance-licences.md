# ORA-N3-06 — RCA performance & licences

**Quand ?** Régression SQL, waits persistants, problème RAC/I/O.

### Sessions actives par attente (RAC ou mono-instance)
```sql
SELECT inst_id, wait_class, event, COUNT(*) AS sessions
FROM gv$session WHERE status = 'ACTIVE' AND type = 'USER'
GROUP BY inst_id, wait_class, event
ORDER BY sessions DESC FETCH FIRST 15 ROWS ONLY;
```

### SQL en cache, sans texte applicatif
```sql
SELECT sql_id, plan_hash_value, executions,
       ROUND(elapsed_time/1000000,1) AS total_s,
       ROUND(cpu_time/1000000,1) AS cpu_s
FROM v$sqlstats
WHERE executions > 0
ORDER BY elapsed_time DESC FETCH FIRST 15 ROWS ONLY;
```

### Lecture et décision
- `ELAPSED_TIME` et `CPU_TIME` : microsecondes **cumulées**, pas durée d'un seul appel.
- Une attente `User I/O` ou `Cluster` ne prouve pas, à elle seule, un défaut de stockage/interconnexion.
- **AWR, ASH et ADDM nécessitent Diagnostics Pack ; SQL Tuning Advisor nécessite Tuning Pack**. Un paramètre activé ne prouve pas la licence.
- **Action N3 :** comparer intervalles, plan, volume et changements ; tester une seule hypothèse avec retour arrière.
- **Validation :** latence p95/p99, charge et erreurs sur trafic comparable, application confirmée.

**Escalade :** changement de plan ou d'architecture risqué, licence incertaine.

[Sources : V$SQLSTATS](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/V-SQLSTATS.html) · [Licences](https://docs.oracle.com/en/database/oracle/oracle-database/19/dblic/Licensing-Information.html)

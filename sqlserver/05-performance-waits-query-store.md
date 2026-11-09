# MSSQL-N3-05 — RCA performance, waits & Query Store

**Quand ?** Latence persistante, plan instable, régression non expliquée.

### Attentes cumulées depuis démarrage/reset
```sql
SELECT TOP (15) wait_type, waiting_tasks_count,
       wait_time_ms, signal_wait_time_ms
FROM sys.dm_os_wait_stats ORDER BY wait_time_ms DESC;
```

### I/O cumulées par fichier
```sql
SELECT TOP (15) DB_NAME(v.database_id) AS base, m.type_desc,
       v.num_of_reads, v.io_stall_read_ms,
       v.num_of_writes, v.io_stall_write_ms
FROM sys.dm_io_virtual_file_stats(NULL,NULL) AS v
JOIN sys.master_files AS m
  ON v.database_id=m.database_id AND v.file_id=m.file_id
ORDER BY v.io_stall_read_ms+v.io_stall_write_ms DESC;
```

### Lecture et décision
- Mesurer les **deltas** et leur période ; un total élevé ne prouve pas un goulot actuel.
- Query Store, si activé : comparer plans et intervalles de charge, pas une moyenne globale seule.
- `avg_duration` de Query Store est en **microsecondes**, non millisecondes.
- Corréler waits, CPU, I/O, déploiements et p95/p99 applicatifs ; une attente `PAGEIOLATCH` n'établit pas seule une panne disque.
- **Action N3 :** test d'une hypothèse à la fois, retour arrière prévu. **Validation :** latence et erreurs sur charge comparable.

**Escalade :** architecture, stockage ou changement de schéma à fort impact.

[Sources : waits](https://learn.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) · [Query Store](https://learn.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql)

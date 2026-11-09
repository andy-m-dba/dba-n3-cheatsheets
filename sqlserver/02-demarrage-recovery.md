# MSSQL-N3-02 — Démarrage & recovery bloqué

**Quand ?** Base `RECOVERY_PENDING`, `SUSPECT`, instance instable.

### État des bases et des fichiers
```sql
SELECT name, state_desc, recovery_model_desc
FROM sys.databases ORDER BY name;
SELECT DB_NAME(database_id) AS base, type_desc, state_desc
FROM sys.master_files WHERE state_desc <> 'ONLINE';
```

### Opérations de récupération visibles
```sql
SELECT session_id, command, percent_complete,
       total_elapsed_time AS duree_ms, wait_type
FROM sys.dm_exec_requests
WHERE command IN (N'RECOVERY', N'ROLLBACK', N'DB STARTUP');
```

### Lecture et décision
- `RECOVERY_PENDING` peut signaler ressource/fichier inaccessible ; `SUSPECT` demande un diagnostic, **pas** une réparation immédiate.
- `percent_complete` n'est pas toujours disponible ni une estimation fiable du délai.
- Rechercher erreur SQL Server, stockage, permissions, journaux Windows et opérations récentes.
- **Action N3 :** préserver preuves, sauvegardes et chaîne log ; choisir récupération/reconstruction validée. Pas d'`EMERGENCY` ni de `REPAIR` improvisé.
- **Validation :** base `ONLINE`, contrôles d'intégrité adaptés et application opérationnelle.

**Escalade :** corruption, journal manquant ou récupération susceptible de perdre des données.

[Sources : états des bases](https://learn.microsoft.com/en-us/sql/relational-databases/databases/database-states) · [recovery DMV](https://learn.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)

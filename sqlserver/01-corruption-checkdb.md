# MSSQL-N3-01 — Corruption & CHECKDB

**Quand ?** Erreurs 823/824/825, pages suspectes, intégrité menacée.

### État des bases
```sql
SELECT name, state_desc, page_verify_option_desc
FROM sys.databases WHERE state_desc <> 'ONLINE';
```

### Pages suspectes (historique msdb)
```sql
SELECT TOP (20) DB_NAME(database_id) AS base, file_id,
       page_id, event_type, error_count, last_update_date
FROM msdb.dbo.suspect_pages ORDER BY last_update_date DESC;
```

### Préparer CHECKDB (sans le lancer)
```sql
DBCC CHECKDB (N'BASE_CIBLE') WITH ESTIMATEONLY;
```

### Lecture et décision
- `suspect_pages` est incomplet et historique ; sortie vide ≠ absence de corruption.
- `ESTIMATEONLY` estime les ressources `tempdb`, **ne vérifie pas** l'intégrité.
- **Sous fenêtre et autorisation :** CHECKDB complet ou `PHYSICAL_ONLY` selon charge ; impact I/O, snapshot interne et ressources.
- Priorité à l'intégrité du stockage et à une **restauration depuis un backup sain**. Ne jamais déclencher `REPAIR_ALLOW_DATA_LOSS` par réflexe.
- **Validation :** CHECKDB adapté, stockage sain et cohérence métier vérifiée.

**Escalade :** corruption confirmée, fichiers systèmes touchés ou restauration d'urgence.

[Sources : CHECKDB](https://learn.microsoft.com/en-us/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) · [Corruption](https://learn.microsoft.com/en-us/troubleshoot/sql/database-engine/database-file-operations/troubleshoot-dbcc-checkdb-errors)

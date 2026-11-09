# MSSQL-N3-04 — Transaction log, VLF & rétention

**Quand ?** Journal saturé, croissance LDF, recovery long.

### Contexte de la base cible
```sql
SELECT name, recovery_model_desc, log_reuse_wait_desc
FROM sys.databases WHERE name = N'BASE_CIBLE';
```

### État détaillé du log (base primaire)
```sql
SELECT total_log_size_mb, active_log_size_mb,
       total_vlf_count, active_vlf_count,
       log_truncation_holdup_reason, log_backup_time
FROM sys.dm_db_log_stats(DB_ID(N'BASE_CIBLE'));
```

### Lecture et décision
- `log_truncation_holdup_reason` : raison déclarée de la rétention, **pas** cause racine confirmée.
- Un nombre élevé de VLF peut nuire au recovery, mais aucun seuil isolé ne prouve l'incident.
- Sur une secondaire AG, `sys.dm_db_log_stats` renvoie des champs limités.
- Corréler transactions longues, backups log, AG, CDC/réplication et espace OS.
- **Action N3 :** traiter la cause avec RPO/PITR documenté. Pas de `SHRINK`, de passage en `SIMPLE` ni de suppression LDF réflexes.
- **Validation :** croissance maîtrisée, chaîne des backups log intacte et service stable.

**Escalade :** volume critique, chaîne PITR rompue ou restauration nécessaire.

[Source : sys.dm_db_log_stats](https://learn.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-objects/sys-dm-db-log-stats-transact-sql)

# MSSQL-N3-07 — PITR, backups & PRA

**Quand ?** Reprise de données, corruption ou RPO/RTO à valider.

### Chaîne de sauvegarde — historique msdb
```sql
SELECT TOP (30) type, is_copy_only, backup_start_date,
       backup_finish_date, first_lsn, last_lsn,
       checkpoint_lsn, database_backup_lsn
FROM msdb.dbo.backupset
WHERE database_name=N'BASE_CIBLE'
ORDER BY backup_finish_date DESC;
```

### Lecture et décision
- Types : `D` full, `I` différentiel, `L` log ; contrôler la **continuité des LSN**, copie seule et recovery model.
- Historique `msdb` incomplet ≠ sauvegarde absente ; vérifier outil externe et médias.
- `RESTORE VERIFYONLY` vérifie la lisibilité du backup, **pas** toute la cohérence des données ; test de restauration obligatoire.
- Recenser certificats/clefs TDE, backups chiffrés, destinations et ordre de restauration.
- **Action N3 :** préciser `STOPAT`, RPO accepté, environnement isolé et plan de bascule avant toute restauration en production.
- **Validation :** reprise testée, RTO mesuré, cohérence et parcours métier confirmés.

**Escalade :** LSN manquant, clef indisponible, fork divergent ou perte de données non autorisée.

[Sources : backupset](https://learn.microsoft.com/en-us/sql/relational-databases/system-tables/backupset-transact-sql) · [VERIFYONLY](https://learn.microsoft.com/en-us/sql/t-sql/statements/restore-statements-verifyonly-transact-sql)

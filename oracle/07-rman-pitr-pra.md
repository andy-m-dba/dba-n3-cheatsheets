# ORA-N3-07 — RMAN, PITR & PRA

**Quand ?** Restauration envisagée, archive manquante, RPO/RTO à confirmer.

### Derniers jobs RMAN
```sql
SELECT input_type, status, start_time, end_time
FROM v$rman_backup_job_details
ORDER BY start_time DESC FETCH FIRST 20 ROWS ONLY;
```

### DBID, rôle et incarnation
```sql
SELECT dbid, database_role, resetlogs_change#,
       resetlogs_time, current_scn FROM v$database;
```

### Destinations d'archive (instance courante)
```sql
SELECT dest_id, status, error FROM v$archive_dest
WHERE status <> 'INACTIVE' ORDER BY dest_id;
```

### Décision de reprise
- Définir point cible **SCN/heure**, incarnation, portée CDB/PDB, RPO autorisé et RTO.
- Vérifier chaîne backup + archivelogs, controlfile/SPFILE, catalogues externes, stockage et wallet/clefs TDE.
- Job `COMPLETED` ≠ preuve de restaurabilité ; `RESTORE VALIDATE` et `BACKUP VALIDATE` consomment des ressources et nécessitent autorisation.
- **Action N3 :** répéter la restauration en environnement isolé avant décision en production ; documenter bascule et retour.
- **Validation :** temps de reprise mesuré, cohérence et transactions métier vérifiées.

**Escalade :** archives/clefs indisponibles, incarnation incertaine ou perte de données non autorisée.

[Sources : RMAN jobs](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/V-RMAN_BACKUP_JOB_DETAILS.html) · [validation](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/validating-database-files-backups.html)

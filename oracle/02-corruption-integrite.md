# ORA-N3-02 — Corruption & intégrité

**Quand ?** ORA-01578, ORA-01110, bloc endommagé ou I/O suspecte.

### Blocs signalés par Oracle
```sql
SELECT con_id, file#, block#, blocks, corruption_type
FROM v$database_block_corruption
ORDER BY file#, block#;
```

### Fichiers nécessitant une récupération
```sql
SELECT file#, online_status, error, change#
FROM v$recover_file ORDER BY file#;
```

### Lecture et décision
- `V$DATABASE_BLOCK_CORRUPTION` contient les corruptions **détectées** ; sortie vide ≠ base saine.
- Une corruption interblocs peut ne pas apparaître dans cette vue.
- Conserver alert log, identifiants de blocs et preuves I/O ; identifier le fichier, le conteneur et la sauvegarde exploitable.
- **Action N3 :** définir l'étendue ; `RMAN VALIDATE` peut être coûteux. Block media recovery ou restauration uniquement sous procédure validée, jamais par supposition.
- **Validation :** nouveaux contrôles d'intégrité ciblés, stockage sain et vérification des données applicatives.

**Escalade :** corruption systémique, fichier critique ou sauvegarde de récupération indisponible. Ne jamais supprimer ou recréer des fichiers sans plan de restauration.

[Sources : V$DATABASE_BLOCK_CORRUPTION](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/V-DATABASE_BLOCK_CORRUPTION.html) · [validation](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/validating-database-files-backups.html)

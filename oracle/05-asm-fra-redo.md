# ORA-N3-05 — ASM, FRA & pression redo

**Quand ?** ORA-19809, FRA pleine, diskgroup ASM sous pression.

### FRA — depuis l'instance de base
```sql
SELECT ROUND(space_limit/1048576) AS limite_mio,
       ROUND(space_used/1048576) AS utilise_mio,
       ROUND(space_reclaimable/1048576) AS recuperable_mio
FROM v$recovery_file_dest;
```

### ASM — depuis l'instance +ASM autorisée
```sql
SELECT name, state, type, total_mb, free_mb, usable_file_mb
FROM v$asm_diskgroup_stat ORDER BY name;
```

### Destinations d'archivage
```sql
SELECT dest_id, status, error FROM v$archive_dest
WHERE status <> 'INACTIVE' ORDER BY dest_id;
```

### Lecture et décision
- `SPACE_USED` FRA peut inclure des fichiers d'anciennes destinations ; n'est pas le disque libre réel.
- `USABLE_FILE_MB` tient compte de la redondance ASM ; `FREE_MB` seul n'est pas une capacité utile garantie.
- `V$ASM_DISKGROUP_STAT` évite la découverte des disques, contrairement à `V$ASM_DISKGROUP`.
- **Action N3 :** corréler retentions RMAN/Data Guard, archives et ASM ; extension/purge uniquement selon politique et RPO validés.
- **Validation :** archivage fonctionnel, marge utile restaurée et stabilité sur plusieurs relevés.

**Escalade :** capacité critique ou archives requises à une reprise. Ne jamais supprimer de redo/archivelog manuellement.

[Sources : FRA](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/V-RECOVERY_FILE_DEST.html) · [ASM](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/V-ASM_DISKGROUP_STAT.html)

# ORA-N3-01 — Démarrage & recovery impossible

**Quand ?** ORA au démarrage, instance `MOUNTED`, PDB inaccessible, fichier à récupérer.

### État de l'instance (si connexion SYSDBA autorisée)
```sql
SELECT instance_name, status, database_status
FROM v$instance;
```

### Après montage de la base uniquement
```sql
SELECT name, open_mode, database_role, controlfile_type
FROM v$database;
SELECT file#, online_status, error, change#
FROM v$recover_file ORDER BY file#;
```

### Si CDB accessible
```sql
SELECT con_id, name, open_mode, restricted
FROM v$pdbs ORDER BY con_id;
```

### Lecture et décision
- `STARTED`, `MOUNTED`, `OPEN` désignent des étapes différentes ; ne pas conclure à une corruption sur le seul état.
- `V$RECOVER_FILE` identifie des fichiers nécessitant une récupération ; une ligne `OFFLINE NORMAL` peut ne pas demander de recovery.
- Corréler ORA, alert log, état OS/ASM, redo et controlfile **avant** de choisir le scénario.
- **Action N3 :** stratégie de reprise approuvée, sauvegardes et RPO vérifiés. Aucun `RESETLOGS`, recréation de controlfile ou ouverture forcée par réflexe.
- **Validation :** rôle/mode attendus, fichiers accessibles, service et cohérence métier confirmés.

**Escalade :** redo/controlfile absent, recovery incomplet ou timeline incohérente.

[Sources : V$RECOVER_FILE](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/V-RECOVER_FILE.html) · [V$PDBS](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/V-PDBS.html)

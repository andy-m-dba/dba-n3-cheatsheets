# MYSQL-N3-06 — Stockage, redo & purge InnoDB

**Quand ?** Disque saturé, purge ralentie, checkpoint/redo sous pression.

### Indicateurs communs
```sql
SHOW GLOBAL STATUS WHERE Variable_name IN
 ('Innodb_os_log_written','Innodb_log_waits',
  'Created_tmp_disk_tables');
SHOW ENGINE INNODB STATUS\G
```

### MySQL 8.4 — capacité redo
```sql
SHOW GLOBAL VARIABLES LIKE 'innodb_redo_log_capacity';
```

### MariaDB 10.11/11.x — taille des fichiers redo
```sql
SHOW GLOBAL VARIABLES LIKE 'innodb_log_file_size';
```

### Disques (hôte autorisé)
```bash
df -h
df -i
```

### Lecture et décision
- `Innodb_os_log_written` est **cumulatif** ; redo ≠ binlog ≠ undo.
- `History list length` longue : vérifier transactions anciennes/purge, sans conclure à une fuite.
- Le nombre de fichiers redo et leur dimensionnement diffèrent entre versions et moteurs.
- **Action N3 :** corréler volumes, écriture, checkpoints et rétention ; aucun effacement redo/undo, shrink ou changement de paramètres sans plan.
- **Validation :** marge disque, génération redo et temps de réponse stabilisés.

**Escalade :** disque critique, transaction longue non interrompable, recovery menacé ou corruption.

[Sources : MySQL redo](https://dev.mysql.com/doc/refman/8.4/en/innodb-redo-log.html) · [MariaDB InnoDB](https://mariadb.com/docs/server/server-usage/storage-engines/innodb/innodb-redo-log)

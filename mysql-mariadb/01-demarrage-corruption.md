# MYSQL-N3-01 — Démarrage, crash & corruption InnoDB

**Quand ?** `mysqld` indisponible, crash recovery bloqué, pages suspectes.

### Si SQL répond
```sql
SELECT VERSION() AS version, @@global.read_only AS read_only;
SHOW GLOBAL VARIABLES LIKE 'innodb_force_recovery';
SHOW GLOBAL STATUS LIKE 'Uptime';
```

### Depuis l'hôte autorisé (self-managed)
```bash
df -h
df -i
```

### Lecture et décision
- Recouper **error log**, stockage/I/O, fichiers manquants, version et arrêt récent.
- InnoDB applique normalement redo et rollback après un crash ; lenteur ≠ corruption confirmée.
- `innodb_force_recovery > 0` correspond à un **mode d'urgence** ; jamais un réglage de disponibilité permanent.
- `CHECK TABLE` peut être coûteux et ne détecte pas toutes les corruptions : fenêtre validée uniquement.
- **Action N3 :** préserver preuves et copie exploitable ; choisir diagnostic/restauration. Ne supprimer **aucun** redo/undo/ibdata/ibd ni forcer recovery par réflexe.
- **Validation :** démarrage stable, intégrité vérifiée selon procédure, lecture/écriture métier confirmées.

**Escalade :** data dictionary/tablespace endommagé, recovery impossible ou backup incohérent.

[Sources : recovery](https://dev.mysql.com/doc/refman/8.4/en/innodb-recovery.html) · [force recovery](https://dev.mysql.com/doc/refman/8.4/en/forcing-innodb-recovery.html)

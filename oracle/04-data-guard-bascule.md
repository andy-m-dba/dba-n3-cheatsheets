# ORA-N3-04 — Data Guard & décision de bascule

**Quand ?** Standby en retard, gap redo, switchover/failover envisagé.

### Rôle et mode (sur chaque base concernée)
```sql
SELECT database_role, open_mode, protection_mode,
       switchover_status FROM v$database;
```

### Sur la standby
```sql
SELECT name, value, time_computed, datum_time
FROM v$dataguard_stats
WHERE name IN ('transport lag','apply lag');
```

### Sur la primaire
```sql
SELECT dest_id, status, error
FROM v$archive_dest
WHERE status <> 'INACTIVE' ORDER BY dest_id;
```

### Lecture et décision
- `DATUM_TIME` qui ne progresse pas : **lag potentiellement obsolète**, même affiché à zéro.
- Transport redo ≠ application du redo. Contrôler Broker, gaps, synchronisation et protection attendue.
- **Avant bascule :** confirmer source de vérité, ancien primaire isolé si nécessaire, perte de données maximale autorisée et quorum/décision PRA.
- Un failover peut nécessiter la réintégration ou reconstruction de l'ancien primaire.
- **Validation :** rôle unique attendu, redo reçu/appliqué, services routés et tests métier réussis.

**Escalade :** redo manquant, divergence ou RPO non accepté. Pas de commande de promotion automatique depuis cette fiche.

[Sources : V$DATAGUARD_STATS](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/V-DATAGUARD_STATS.html) · [Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/19/haovw/redo-apply-troubleshooting-and-tuning.html)

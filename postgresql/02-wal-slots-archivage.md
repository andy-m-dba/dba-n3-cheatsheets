# PG-N3-02 — WAL, slots & archivage

**Quand ?** `pg_wal` grossit, slot invalidé, archive interrompue.

### Sur le primaire uniquement
```sql
SELECT slot_name, slot_type, active, wal_status, safe_wal_size,
       pg_wal_lsn_diff(pg_current_wal_lsn(), restart_lsn) AS ecart_lsn_octets
FROM pg_replication_slots
WHERE restart_lsn IS NOT NULL
ORDER BY ecart_lsn_octets DESC;
```

### Contrôler l'archiver
```sql
SELECT archived_count, failed_count, last_archived_time, stats_reset
FROM pg_stat_archiver;
```

### Lecture et décision
- `wal_status='lost'` : slot inutilisable ; `unreserved` : WAL potentiellement supprimés au checkpoint.
- `safe_wal_size IS NULL` peut signifier limite illimitée **ou** slot perdu.
- Écart LSN ≠ octets actuellement présents sur le disque ; comparer à `df` et à la supervision.
- `failed_count` est cumulatif ; un dernier WAL archivé ne prouve pas une archive complète.
- **Action N3 :** confirmer le consommateur (réplica, CDC, décodage logique) et le RPO avant toute décision.
- **Validation :** archivage continu, consommateurs synchronisés, espace stabilisé.

**Escalade :** WAL indispensable absent ou reprise des consommateurs impossible. **Ne pas** supprimer de WAL ni de slot à l'aveugle.

[Sources : slots](https://www.postgresql.org/docs/18/view-pg-replication-slots.html) · [archivage](https://www.postgresql.org/docs/18/continuous-archiving.html)

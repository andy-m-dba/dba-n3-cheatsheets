# PG-N3-03 — Réplication & décision de failover

**Quand ?** Primaire indisponible, standby en retard, promotion envisagée.

### Sur le primaire, s'il répond
```sql
SELECT application_name, state, sync_state,
       sent_lsn, replay_lsn, write_lag, flush_lag, replay_lag
FROM pg_stat_replication;
```

### Sur la standby
```sql
SELECT status, last_msg_receipt_time FROM pg_stat_wal_receiver;
SELECT pg_last_wal_receive_lsn(), pg_last_wal_replay_lsn(),
       pg_is_wal_replay_paused();
```

### Critères de décision
- WAL reçu ≠ rejoué ; les colonnes de lag peuvent être `NULL` sans être une preuve de synchronisation.
- Confirmer timeline, LSN, topologie, état des slots et capacité à reconstruire l'ancien primaire.
- **Avant toute promotion :** vérifier le *fencing* de l'ancien primaire, le RPO accepté et l'autorité de bascule. Sinon risque de **split-brain**.
- La promotion modifie la timeline ; retour au primaire initial non trivial.
- **Validation :** un seul primaire autorisé à écrire, routage correct, réplication reconstruite et tests applicatifs.

**Escalade :** perte de WAL, divergence, ancien primaire non isolé ou RPO non validé. Aucune promotion automatique depuis cette fiche.

[Sources : vues de réplication](https://www.postgresql.org/docs/18/monitoring-stats.html) · [HA](https://www.postgresql.org/docs/18/high-availability.html)

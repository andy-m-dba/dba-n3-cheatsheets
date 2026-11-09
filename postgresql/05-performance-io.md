# PG-N3-05 — RCA performance, waits & I/O

**Quand ?** Latence persistante, régression non expliquée par N2.

### Répartition des attentes actives
```sql
SELECT wait_event_type, wait_event, count(*) AS sessions
FROM pg_stat_activity WHERE state = 'active'
GROUP BY wait_event_type, wait_event
ORDER BY sessions DESC;
```

### PostgreSQL 16+ uniquement : I/O par type de backend
```sql
SELECT backend_type, object, context, reads, read_time,
       writes, write_time
FROM pg_stat_io ORDER BY reads + writes DESC LIMIT 15;
```

### Vérifier la collecte des durées
```sql
SHOW track_io_timing;
SHOW track_wal_io_timing;
```

### Lecture et décision
- `pg_stat_io` **absent en PostgreSQL 14/15** ; comparer les compteurs sur un intervalle.
- `read_time` / `write_time` peuvent être nuls si le timing I/O n'est pas activé.
- Attente `IO` ≠ preuve de lenteur stockage ; confronter aux métriques OS, plans, requêtes et changements.
- **Action N3 :** tester une hypothèse à la fois, mesurer l'effet et prévoir le rollback du changement.
- **Validation :** p95/p99 applicatifs, CPU/I/O et taux d'erreur sur charge comparable.

**Escalade :** dégradation persistante ou changement d'architecture/stockage nécessaire.

[Sources : pg_stat_io](https://www.postgresql.org/docs/18/monitoring-stats.html) · [timing](https://www.postgresql.org/docs/18/runtime-config-statistics.html)

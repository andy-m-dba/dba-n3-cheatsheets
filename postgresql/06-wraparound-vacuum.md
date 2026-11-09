# PG-N3-06 — XID/MXID wraparound & VACUUM

**Quand ?** Alerte wraparound, XID ancien, autovacuum inefficace.

### Risque par base (toutes les bases)
```sql
SELECT datname, age(datfrozenxid) AS age_xid,
       mxid_age(datminmxid) AS age_mxid
FROM pg_database ORDER BY age(datfrozenxid) DESC;
```

### Seuils et retenues potentielles
```sql
SELECT name, setting FROM pg_settings
WHERE name IN ('autovacuum_freeze_max_age',
               'autovacuum_multixact_freeze_max_age');
SELECT pid, backend_xmin, xact_start
FROM pg_stat_activity WHERE backend_xmin IS NOT NULL
ORDER BY xact_start NULLS LAST LIMIT 15;
```

### Lecture et décision
- XID et MXID ont des mécanismes et limites **distincts**. Les âges doivent être comparés à la configuration et à la progression réelle.
- Chercher aussi transactions préparées, slots avec `xmin`/`catalog_xmin` anciens et VACUUM bloqués.
- Un vieux slot peut retenir XID, mais sa suppression peut imposer la reconstruction d'un réplica/consommateur.
- **Si écritures refusées pour wraparound :** incident critique. Préserver les garde-fous PostgreSQL ; diagnostiquer les retenues et coordonner un `VACUUM` adapté.
- Ne pas recourir par réflexe à `VACUUM FULL`, au mode single-user ou à un arrêt forcé.
- **Validation :** âges diminués, VACUUM terminé et écritures applicatives rétablies.

**Escalade :** progression vers la limite de wraparound ou intervention sur transaction/slot critique.

[Source : prévention du wraparound](https://www.postgresql.org/docs/18/routine-vacuuming.html)

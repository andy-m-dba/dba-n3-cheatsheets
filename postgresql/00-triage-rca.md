# PG-N3-00 — Triage expert & RCA

**Quand ?** Incident critique persistant après N1/N2, cause racine inconnue.

### Contexte (2 min)
Heure et fuseau, impact métier, topologie, version, rôle, déploiements, dernière sauvegarde valide.

### État du nœud (SQL accessible)
```sql
SELECT now() AS collecte, current_setting('server_version') AS version,
       pg_is_in_recovery() AS standby, pg_postmaster_start_time();
SELECT wait_event_type, wait_event, count(*) AS sessions
FROM pg_stat_activity WHERE state = 'active'
GROUP BY wait_event_type, wait_event ORDER BY sessions DESC;
```

### Indicateurs à comparer
```sql
SELECT datname, deadlocks, temp_bytes, stats_reset
FROM pg_stat_database ORDER BY temp_bytes DESC LIMIT 10;
```
`stats_reset` délimite les compteurs ; une valeur seule n'établit pas la cause.

### Décision
- **Preuves :** timeline, métriques avant/après, logs expurgés, nœuds concernés.
- **Hypothèses :** classées et vérifiables ; distinguer corrélation et causalité.
- **Action :** uniquement après évaluation RPO/RTO, validation et retour arrière.
- **Validation :** indicateurs stabilisés **et** confirmation applicative.

**Escalade :** risque de perte de données ou incertitude sur la cohérence. **RCA :** déclencheur établi, facteurs contributifs, preuves, correction, prévention et points non résolus.

[Source : statistiques](https://www.postgresql.org/docs/18/monitoring-stats.html)

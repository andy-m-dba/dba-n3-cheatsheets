# ORA-N3-00 — Triage expert & RCA

**Quand ?** Incident Oracle critique sans cause confirmée après N1/N2.

### 1. Cadrer
Heure/fuseau, service, CDB/PDB, RAC/Data Guard, impact, ORA-xxxxx, changements, sauvegarde et RPO/RTO attendus.

### 2. État et rôle (connexion autorisée)
```sql
SELECT instance_name, status, database_status, startup_time
FROM v$instance;
SELECT name, open_mode, database_role, log_mode
FROM v$database;
```
La seconde requête suppose une base au moins `MOUNTED`.

### 3. Anomalies récentes (ADR accessible)
```sql
SELECT originating_timestamp, message_text
FROM v$diag_alert_ext
WHERE originating_timestamp >= SYSTIMESTAMP - INTERVAL '1' HOUR
  AND message_type IN (2,3,4)
ORDER BY originating_timestamp DESC FETCH FIRST 15 ROWS ONLY;
```

### Décision
- **Preuves :** timeline, alert log expurgé, métriques comparées, portée par instance/PDB.
- **Hypothèses :** vérifiables ; distinguer déclencheur, facteur contributif et cause établie.
- **Action :** autorisation, impact/RPO, scénario de retour et sauvegarde confirmés.
- **Validation :** état technique stable et service applicatif rétabli.

**Escalade :** corruption, perte de données possible, rôle incertain ou reprise non maîtrisée. **RCA :** preuves, cause si établie, correction, prévention, incertitudes.

[Sources : V$INSTANCE](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/V-INSTANCE.html) · [V$DATABASE](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/V-DATABASE.html)

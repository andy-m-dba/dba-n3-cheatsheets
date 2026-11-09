# MYSQL-N3-00 — Triage expert & RCA

**Quand ?** Incident critique MySQL/MariaDB non résolu par N1/N2.

### Cadrer
Heure/fuseau, version, instance, rôle, service, impact, changements, RPO/RTO et dernier backup vérifié.

### Identifier l'instance (SQL autorisé)
```sql
SELECT VERSION() AS version, @@hostname AS hote,
       @@global.read_only AS lecture_seule;
SHOW GLOBAL STATUS WHERE Variable_name IN
 ('Uptime','Threads_connected','Threads_running','Aborted_connects');
```

### Orienter
[Crash](01-demarrage-corruption.md) · [Deadlocks](02-verrous-deadlocks.md) · [SQL](03-performance-optimiseur.md) · [GTID](04-replication-gtid.md) · [HA](05-ha-quorum-bascule.md) · [Stockage](06-stockage-redo-undo.md) · [PITR](07-pitr-pra.md).

### Décision
- **Preuves :** chronologie, journaux expurgés, état de chaque nœud et mesures comparées.
- **Hypothèses :** vérifiables ; un changement récent n'est pas une cause prouvée.
- **Action N3 :** autorisation, impact, sauvegarde, scénario de retour, puis une correction ciblée.
- **Validation :** stabilité des indicateurs **et** confirmation applicative.

**Escalade :** corruption, topologie incertaine, perte potentielle ou reprise non maîtrisée. **RCA :** cause établie ou inconnue, faits, facteurs contributifs et prévention.

[Sources : MySQL](https://dev.mysql.com/doc/refman/8.4/en/show-status.html) · [MariaDB](https://mariadb.com/docs/server/reference/sql-statements/administrative-sql-statements/show/show-status)

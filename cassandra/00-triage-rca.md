# CASS-N3-00 — Triage expert & RCA

**Quand ?** Incident Cassandra critique sans cause confirmée après N1/N2.

### Cadrer
Heure/fuseau, cluster/DC, keyspace, nœuds touchés, consistency level, impact, changements, RPO/RTO et dernier backup validé.

### État depuis un nœud autorisé (JMX)
```bash
nodetool -h HOST status
nodetool -h HOST describecluster
nodetool -h HOST tpstats
nodetool -h HOST netstats
```

### Décision
- `nodetool` utilise **JMX**, distinct du CQL ; adapter TLS/identifiants sans mot de passe dans la ligne de commande.
- `status` est une **vue locale** ; recouper les nœuds/DC et les métriques applicatives.
- `Pending` est instantané, `Dropped` cumulatif : comparer des relevés horodatés.
- **Preuves :** chronologie, logs expurgés, périmètre, versions et mesures avant/après.
- **Hypothèses :** vérifier séparément réseau, disque, réplication, driver, schéma et charge.
- **Action N3 :** autorisation, impact/RPO et plan de retour avant toute modification.
- **Validation :** état attendu sur plusieurs nœuds et service métier rétabli.

**Escalade :** perte de données possible, cohérence non garantie ou intervention de topologie risquée. **RCA :** cause prouvée ou inconnue, preuves, facteurs et prévention.

[Sources : status](https://cassandra.apache.org/doc/latest/cassandra/managing/tools/nodetool/status.html) · [tpstats](https://cassandra.apache.org/doc/latest/cassandra/managing/tools/nodetool/tpstats.html)

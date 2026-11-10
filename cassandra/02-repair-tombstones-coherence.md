# CASS-N3-02 — Repair, tombstones & cohérence

**Quand ?** Lectures incohérentes, repair échoué, nœud indisponible longtemps ou données supprimées réapparues.

### État sans déclencher de repair (JMX)
```bash
nodetool -h HOST status
nodetool -h HOST netstats
nodetool -h HOST compactionstats
```

### Paramètres de tables (cqlsh autorisé)
```sql
SELECT keyspace_name, table_name, gc_grace_seconds
FROM system_schema.tables
WHERE keyspace_name = 'KEYSPACE';
```

### Lecture et décision
- `netstats` vide ≠ cluster cohérent. Hinted handoff **ne remplace pas** un repair complet.
- `gc_grace_seconds` borne la grâce des tombstones ; un nœud absent au-delà peut contribuer à une **résurrection** de données supprimées.
- Repair incrémental et full repair ont des effets et couvertures différents ; vérifier l'historique **par plage de tokens, table, nœud et DC**.
- **Action N3 :** fixer la portée et le coût réseau/disque, tester le plan, puis autoriser le repair ; `--preview` peut aussi solliciter le cluster.
- Ne pas réduire `gc_grace_seconds` ou forcer une compaction pour masquer l'anomalie.
- **Validation :** repairs terminés, métriques stables et cohérence applicative constatée.

**Escalade :** données divergentes, ancien nœud > période de grâce, corruption ou repair à reprendre intégralement.

[Sources : repair](https://cassandra.apache.org/doc/stable/cassandra/managing/operating/repair.html) · [tombstones](https://cassandra.apache.org/doc/latest/cassandra/managing/operating/compaction/tombstones.html)

# CASS-N3-04 — SSTables, compactions & capacité

**Quand ?** Saturation disque, backlog de compaction, tombstones ou I/O élevés.

### Table et nœud ciblés (JMX)
```bash
nodetool -h HOST tablestats KEYSPACE.TABLE
nodetool -h HOST compactionstats
nodetool -h HOST getcompactionthroughput
```

### Système de fichiers autorisé
```bash
df -h
df -i
```

### Lecture et décision
- `Space used (live/total)` couvre les SSTables ; **pas** le filesystem, les snapshots ni les commitlogs.
- `getcompactionthroughput` est un **plafond configuré**, pas le débit observé.
- `compactionstats` reflète un instant ; un temps restant annoncé est une estimation.
- Les tombstones expirées ne disparaissent pas sans compaction appropriée ; suivre la stratégie STCS/LCS/TWCS et les repairs.
- **Action N3 :** mesurer marge nécessaire et effet sur les lectures avant tout ajustement ; ne pas lancer `compact`, `cleanup` ni `scrub` par réflexe.
- **Validation :** marge disque, files de compaction et latence stabilisées sur plusieurs relevés.

**Escalade :** disque critique, SSTables suspectes, changement de stratégie ou risque de suppression de données.

[Sources : compactions](https://cassandra.apache.org/doc/stable/cassandra/managing/operating/compaction/overview.html) · [tablestats](https://cassandra.apache.org/doc/latest/cassandra/managing/tools/nodetool/tablestats.html)

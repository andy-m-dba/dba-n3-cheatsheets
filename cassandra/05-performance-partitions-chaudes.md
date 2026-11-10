# CASS-N3-05 — RCA latence, partitions & timeouts

**Quand ?** p99 élevé, timeouts CQL, hot partitions ou coordinateurs saturés.

### Observer sur les nœuds concernés (JMX)
```bash
nodetool -h HOST tpstats
nodetool -h HOST proxyhistograms
nodetool -h HOST tablehistograms KEYSPACE TABLE
```

### Lecture et décision
- `proxyhistograms` mesure la distribution **côté coordinateur**, pas la latence de bout en bout.
- `tablehistograms` fournit latences locales et taille/nombre de cellules des partitions de la table ciblée.
- `Pending` instantané et `Dropped` cumulatif ; comparer intervalles avant d'inférer une saturation.
- Corréler CPU, heap/GC, stockage, réseau, consistency level, speculative retry et driver.
- Une grosse partition ou un `ALLOW FILTERING` n'est une cause que si la charge et le plan de requête le confirment.
- **Action N3 :** isoler la requête/table et expérimenter une seule hypothèse, avec test et rollback. Éviter `toppartitions`/profiling lourd sans évaluation.
- **Validation :** p95/p99, refus CQL et charge redevenus normaux à trafic comparable.

**Escalade :** changement de modèle de partitions, GC critiques ou architecture multi-DC à revoir.

[Sources : proxyhistograms](https://cassandra.apache.org/doc/latest/cassandra/managing/tools/nodetool/proxyhistograms.html) · [tablehistograms](https://cassandra.apache.org/doc/latest/cassandra/managing/tools/nodetool/tablehistograms.html)

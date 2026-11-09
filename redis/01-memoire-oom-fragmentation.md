# REDIS-N3-01 — OOM, évictions & fragmentation

**Quand ?** `OOM`, mémoire système critique, évictions inattendues.

### Sur chaque nœud concerné
```text
INFO memory
INFO stats
MEMORY STATS
```

### Lecture et décision
- `used_memory` = allocation Redis ; `used_memory_rss` = RSS OS ; `maxmemory` = limite Redis, **pas** limite système.
- `mem_not_counted_for_evict` et buffers de réplication/AOF peuvent accroître la mémoire hors calcul d'éviction.
- Une différence RSS/allocation peut refléter fragmentation ou mémoire libérée non restituée ; corréler `allocator_frag_*`, `used_memory_peak` et l'OS.
- `evicted_keys` est **cumulatif** : comparer deux relevés et la politique `maxmemory_policy`.
- En Cluster, **une saturation locale** est possible malgré la marge globale.
- **Action N3 :** vérifier croissance, TTL, charge et capacité avant réglage/extension validé ; aucune purge ou `FLUSHALL`.
- **Validation :** marge mémoire, taux d'évictions et latence stabilisés sans perte métier.

**Escalade :** OOM système, données évincées critiques ou changement structurel du cache.

[Sources : INFO](https://redis.io/docs/latest/commands/info/) · [MEMORY STATS](https://redis.io/docs/latest/commands/memory-stats/)

# REDIS-N3-04 — Cluster, slots & migrations

**Quand ?** `CLUSTERDOWN`, `MOVED` persistants, slots ou nœuds incohérents.

### Sur un nœud Redis Cluster (ACL autorisée)
```text
CLUSTER INFO
CLUSTER SHARDS
```

### Lecture et décision
- Redis Cluster répartit **16 384 slots** ; vérifier `cluster_slots_assigned`, `cluster_slots_ok`, `cluster_slots_pfail`, `cluster_slots_fail`.
- `PFAIL` est une suspicion locale de panne ; `FAIL` ne se déduit pas d'un seul timeout client.
- `CLUSTER SHARDS` (Redis 7+) expose répartition et rôles ; ne pas publier les adresses internes.
- `cluster_state:ok` est la **vue du nœud interrogé**, pas une preuve que toutes les applications fonctionnent.
- Corréler majorité de primaires, réseau du cluster bus, routage des clients et slots en migration.
- **Action N3 :** choisir reprise de nœud, remappage ou resharding **après** validation RPO, sauvegardes et topologie. Aucun `ADDSLOTS`, `SETSLOT` ou `FAILOVER` réflexe.
- **Validation :** 16 384 slots servis selon la politique, états cohérents sur plusieurs nœuds et trafic applicatif rétabli.

**Escalade :** partitions, slots perdus, divergence de données ou quorum indisponible.

[Sources : CLUSTER INFO](https://redis.io/docs/latest/commands/cluster-info/) · [CLUSTER SHARDS](https://redis.io/docs/latest/commands/cluster-shards/)

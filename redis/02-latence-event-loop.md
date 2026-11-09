# REDIS-N3-02 — RCA latence, event loop & scripts

**Quand ?** Pics p99, Redis bloqué, commandes très coûteuses.

### Sur le nœud concerné (ACL adaptées)
```text
INFO latencystats
INFO commandstats
INFO cpu
LATENCY HISTOGRAM
```

### Enquête ciblée, seulement si autorisé
```text
LATENCY LATEST
SLOWLOG GET 5
```

### Lecture et décision
- `latencystats` et `LATENCY HISTOGRAM` mesurent **l'exécution des commandes**, pas le réseau de bout en bout.
- `LATENCY HISTOGRAM` nécessite le suivi de latence actif ; l'absence d'entrée ne prouve pas zéro latence.
- `SLOWLOG` exclut les échanges réseau et peut exposer **arguments et clés** : expurger ; seuil configurable.
- `commandstats` est **cumulatif**. Examiner scripts, grosses valeurs, blocages, `fork`, CPU et clients.
- Ne jamais lancer `MONITOR` ou une analyse du keyspace complète pendant un incident sans évaluation de charge.
- **Action N3 :** optimiser une commande/script après mesure, test et autorisation ; prévoir retour arrière.
- **Validation :** p95/p99 applicatifs et temps Redis sur charge comparable.

**Escalade :** blocage récurrent, script critique ou tuning d'architecture requis.

[Sources : histogramme](https://redis.io/docs/latest/commands/latency-histogram/) · [SLOWLOG](https://redis.io/docs/latest/commands/slowlog-get/)

# REDIS-N3-03 — Réplication, Sentinel & bascule

**Quand ?** Primaire indisponible, replica déconnecté, promotion envisagée.

### Sur primaire et replicas (session redis-cli autorisée)
```text
INFO replication
```

### Sur le processus Sentinel uniquement (si déployé)
```text
SENTINEL MASTERS
```

### Lecture et décision
- `master_repl_offset`, `slave_repl_offset` : positions en **octets**, pas un retard en secondes.
- Identifier les replication IDs, l'état du lien, l'historique backlog et les replicas éligibles.
- Redis réplique **asynchroniquement** : une écriture acquittée peut être perdue après failover ; même `WAIT` n'assure pas une cohérence forte.
- Sentinel est distinct de Redis Cluster ; vérifier nombre de Sentinels, quorum/majorité et découverte par les clients.
- **Avant bascule :** isoler l'ancien écrivain, fixer RPO accepté, vérifier le routage et la procédure d'autorité.
- **Action N3 :** failover/reconfiguration uniquement après décision PRA, jamais en copier-coller.
- **Validation :** une source d'écriture cohérente, replicas synchronisés et tests applicatifs.

**Escalade :** split-brain, divergence d'offsets ou perte non autorisée.

[Sources : réplication](https://redis.io/docs/latest/manual/replication/) · [Sentinel](https://redis.io/docs/latest/operate/oss_and_stack/management/sentinel/)

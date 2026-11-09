# MYSQL-N3-05 — Haute disponibilité & décision de bascule

**Quand ?** Primaire indisponible, perte de quorum, bascule envisagée.

### MySQL Group Replication uniquement (si déployé)
```sql
SELECT MEMBER_HOST, MEMBER_STATE, MEMBER_ROLE
FROM performance_schema.replication_group_members;
```

### MariaDB Galera uniquement (si déployé)
```sql
SHOW GLOBAL STATUS WHERE Variable_name IN
 ('wsrep_cluster_status','wsrep_cluster_size',
  'wsrep_ready','wsrep_local_state_comment');
```

### Lecture et décision
- Ces commandes **ne décrivent pas** une réplication primaire/réplica classique ni toute solution de cluster managé.
- Group Replication : `MEMBER_ROLE` dépend du mode (single/multi-primary) ; contrôler quorum et routage.
- Galera : composant `Primary` signifie **quorum du composant**, pas « seul serveur écrivain » ; confronter `Synced` et `wsrep_ready` sur chaque nœud.
- **Avant failover :** isoler l'ancien écrivain (*fencing*), établir la source de vérité, accepter le RPO et vérifier les clients.
- **Action N3 :** bascule/bootstrapping selon procédure d'architecture validée ; aucun `force`, `bootstrap` ou promotion réflexe.
- **Validation :** un chemin d'écriture cohérent, quorum/routage corrects, transactions métier et réintégration vérifiés.

**Escalade :** split-brain, quorum perdu ou divergence de données. Le rollback d'une bascule n'est pas automatique.

[Sources : Group Replication](https://dev.mysql.com/doc/refman/8.4/en/group-replication-replication-group-members.html) · [Galera](https://mariadb.com/docs/galera-cluster/high-availability/monitoring-mariadb-galera-cluster)

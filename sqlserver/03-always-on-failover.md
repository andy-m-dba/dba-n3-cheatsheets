# MSSQL-N3-03 — Always On & décision de failover

**Quand ?** AG sans primaire, secondaire en retard, bascule envisagée.

### Rôles et connexion des réplicas
```sql
SELECT r.replica_server_name, r.availability_mode_desc,
       s.role_desc, s.connected_state_desc,
       s.synchronization_health_desc
FROM sys.availability_replicas AS r
JOIN sys.dm_hadr_availability_replica_states AS s
  ON s.replica_id = r.replica_id;
```

### État par base
```sql
SELECT DB_NAME(database_id) AS base, is_local,
       synchronization_state_desc, is_suspended,
       log_send_queue_size AS envoi_ko, redo_queue_size AS redo_ko,
       last_commit_time
FROM sys.dm_hadr_database_replica_states;
```

### Décision
- Files en **Ko** ; `last_commit_time` peut stagner sans écritures. La visibilité dépend du nœud.
- Vérifier mode synchrone/asynchrone, état **de chaque base**, quorum WSFC et routage listener.
- **Failover forcé** : perte de données possible. Décision PRA formelle, RPO accepté et ancien primaire maîtrisé avant action.
- **Validation :** un primaire cohérent, listener fonctionnel, réplicas réintégrés, tests applicatifs.

**Escalade :** quorum perdu, forks divergents, RPO incertain. Aucun failover forcé en copier-coller.

[Sources : Failover](https://learn.microsoft.com/en-us/sql/database-engine/availability-groups/windows/failover-and-failover-modes-always-on-availability-groups) · [DMV AG](https://learn.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-objects/sys-dm-hadr-database-replica-states-transact-sql)

# ORA-N3-03 — RAC, services & blocages globaux

**Quand ?** Incident limité à une instance RAC, sessions bloquées, service instable.

### Instances du cluster (droits de lecture autorisés)
```sql
SELECT inst_id, instance_name, status, database_status
FROM gv$instance ORDER BY inst_id;
```

### Attentes de blocage inter-instances
```sql
SELECT inst_id, sid, serial#, event,
       blocking_instance, blocking_session,
       final_blocking_instance, final_blocking_session
FROM gv$session
WHERE blocking_session_status = 'VALID'
FETCH FIRST 20 ROWS ONLY;
```

### Lecture et décision
- `INST_ID + SID + SERIAL#` désigne la session ; un SID seul est ambigu en RAC.
- Le bloqueur peut avoir quitté le cluster entre deux mesures ; corréler au moment exact de l'incident.
- Comparer l'état des **services** dans Clusterware (`srvctl status service` via procédure autorisée), le listener et la supervision.
- **Action N3 :** confirmer propriétaire et transaction avant toute interruption ou relocalisation de service ; impact des sessions et rollback à prévoir.
- **Validation :** distribution des services et connexions conforme à l'architecture, blocages résolus et application stable.

**Escalade :** éviction d'instance, défaut d'interconnexion, split-brain suspecté ou changement Clusterware risqué.

[Sources : V$INSTANCE](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/V-INSTANCE.html) · [V$SESSION](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/V-SESSION.html)

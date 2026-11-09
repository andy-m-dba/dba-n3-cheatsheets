# DBA N3 — CheatSheets

**But :** diagnostiquer les incidents complexes, établir les preuves, choisir une stratégie sûre et produire une RCA factuelle. **Une fiche = un problème.**

**Méthode N3 :** impact → faits horodatés → hypothèses vérifiables → risque & autorisation → action encadrée → validation → RCA. Ne jamais confondre symptôme et cause confirmée.

## PostgreSQL

| Situation | Fiche |
|---|---|
| Incident critique non résolu | [00 — Triage expert & RCA](postgresql/00-triage-rca.md) |
| Crash / démarrage impossible | [01 — Démarrage & recovery](postgresql/01-demarrage-recovery.md) |
| WAL saturé / slots | [02 — WAL, slots & archivage](postgresql/02-wal-slots-archivage.md) |
| Standby / failover | [03 — Réplication & bascule](postgresql/03-replication-failover.md) |
| Corruption suspectée | [04 — Intégrité & corruption](postgresql/04-integrite-corruption.md) |
| Incident de performance | [05 — Wait events & I/O](postgresql/05-performance-io.md) |
| Risque wraparound | [06 — XID / MXID & vacuum](postgresql/06-wraparound-vacuum.md) |
| Reprise / PITR | [07 — Sauvegarde & PRA](postgresql/07-pitr-pra.md) |

**Versions :** PostgreSQL 14–18 ; la version 14 atteint sa fin de support le **12 novembre 2026**. Adapter les vues aux versions (ex. `pg_stat_io` à partir de 16).

**Sécurité :** les diagnostics SQL sont en lecture seule ; les outils de contrôle peuvent néanmoins consommer des ressources ou exiger un arrêt préalable. Toute correction ou opération de reprise exige validation humaine, sauvegarde cohérente, contrôle de l'impact et stratégie de retour.

## SQL Server

| Situation | Fiche |
|---|---|
| Incident critique / RCA | [00 — Triage expert & RCA](sqlserver/00-triage-rca.md) |
| Corruption suspectée | [01 — CHECKDB & intégrité](sqlserver/01-corruption-checkdb.md) |
| Démarrage impossible | [02 — Recovery & base SUSPECT](sqlserver/02-demarrage-recovery.md) |
| AG sans primaire / bascule | [03 — Always On & failover](sqlserver/03-always-on-failover.md) |
| LDF / journal saturé | [04 — Transaction log & VLF](sqlserver/04-log-vlf-recovery.md) |
| Performance critique | [05 — Waits & Query Store](sqlserver/05-performance-waits-query-store.md) |
| Deadlocks récurrents | [06 — Extended Events](sqlserver/06-deadlocks-extended-events.md) |
| Reprise / restauration | [07 — PITR & PRA](sqlserver/07-pitr-pra.md) |

**Versions :** SQL Server 2019, 2022 et 2025 (instances classiques). Pour les DMV, vérifier les droits de la vue : en général `VIEW SERVER STATE` (2019) et `VIEW SERVER PERFORMANCE STATE` (2022+). Azure SQL Database et les configurations cloud peuvent différer.

**Sécurité :** diagnostics SQL non destructifs, mais certains contrôles sont coûteux. Bascule, réparation et restauration exigent autorisation formelle et stratégie de retour adaptée.

## Oracle

| Situation | Fiche |
|---|---|
| Incident critique / RCA | [00 — Triage expert & RCA](oracle/00-triage-rca.md) |
| Instance / PDB inaccessible | [01 — Démarrage & recovery](oracle/01-demarrage-recovery.md) |
| Corruption ORA-01578 | [02 — Intégrité & corruption](oracle/02-corruption-integrite.md) |
| Services RAC / blocages | [03 — RAC & verrous](oracle/03-rac-services-verrous.md) |
| Standby / bascule | [04 — Data Guard & failover](oracle/04-data-guard-bascule.md) |
| ASM / FRA / redo | [05 — ASM & capacité](oracle/05-asm-fra-redo.md) |
| Régression SQL | [06 — Performance & licences](oracle/06-performance-licences.md) |
| Restauration / PRA | [07 — RMAN & PITR](oracle/07-rman-pitr-pra.md) |

**Versions :** Oracle Database 19c, avec RAC, ASM et Data Guard uniquement lorsqu'ils sont déployés. Contrôles selon le rôle, le conteneur et les privilèges réels ; licence Diagnostics/Tuning Pack à vérifier avant toute utilisation des fonctionnalités concernées.

**Sécurité :** diagnostics en lecture seule ; les contrôles lourds et toute action corrective nécessitent autorisation, évaluation du RPO/RTO, plan de reprise et validation applicative.

## MongoDB

| Situation | Fiche |
|---|---|
| Incident critique / RCA | [00 — Triage expert & RCA](mongodb/00-triage-rca.md) |
| Primaire / élections / rollback | [01 — Élections & rollback](mongodb/01-elections-rollback.md) |
| Oplog / CDC | [02 — Oplog & change streams](mongodb/02-oplog-change-streams.md) |
| Crash / corruption | [03 — Intégrité & WiredTiger](mongodb/03-integrite-wiredtiger.md) |
| Shards / catalogue | [04 — Sharding & métadonnées](mongodb/04-sharding-metadonnees.md) |
| Latence / mémoire / plans | [05 — Performance & WiredTiger](mongodb/05-performance-wiredtiger.md) |
| Reprise / PITR | [06 — Backups & PRA](mongodb/06-pitr-pra.md) |
| Transactions multi-shards | [07 — Transactions distribuées](mongodb/07-transactions-distribuees.md) |

**Versions :** MongoDB 7.0/8.x ; les commandes et leur disponibilité varient selon Atlas, self-managed, le rôle et les privilèges. `mongos` et `mongod` ne sont pas interchangeables.

**Sécurité :** diagnostics non destructifs privilégiés ; `validate` peut bloquer et modifier certaines métadonnées, les reprises/topologies sont soumises à décision formelle. Expurger noms internes, logs, clés et données métier.

## MySQL / MariaDB

| Situation | Fiche |
|---|---|
| Incident critique / RCA | [00 — Triage expert & RCA](mysql-mariadb/00-triage-rca.md) |
| Crash / corruption InnoDB | [01 — Démarrage & corruption](mysql-mariadb/01-demarrage-corruption.md) |
| Deadlocks / transactions | [02 — Verrous & deadlocks](mysql-mariadb/02-verrous-deadlocks.md) |
| Régression SQL | [03 — Performance & optimiseur](mysql-mariadb/03-performance-optimiseur.md) |
| GTID / réplication | [04 — Réplication & divergence](mysql-mariadb/04-replication-gtid.md) |
| Perte de primaire / quorum | [05 — HA & failover](mysql-mariadb/05-ha-quorum-bascule.md) |
| Saturation disque / undo | [06 — Redo & stockage](mysql-mariadb/06-stockage-redo-undo.md) |
| Reprise / restauration | [07 — PITR & PRA](mysql-mariadb/07-pitr-pra.md) |

**Versions :** MySQL 8.0/8.4 et MariaDB 10.11/11.x ; les commandes GTID et les topologies **Group Replication / Galera** sont distinctes. Les vues de supervision demandent des droits adaptés ; Performance Schema peut être désactivé.

**Sécurité :** diagnostics en lecture seule et sorties expurgées ; récupération forcée, reconfiguration, failover, modification de GTID et restauration soumis à autorisation formelle, RPO/RTO et plan de retour.

**Statut :** documentation préparée et contrôlée statiquement, non exécutée sur une instance réelle. Ne jamais publier d'identifiants, chemins internes ou logs bruts.

Documentation : [PostgreSQL](https://www.postgresql.org/docs/18/) · [SQL Server](https://learn.microsoft.com/en-us/sql/sql-server/) · [Oracle Database](https://docs.oracle.com/en/database/oracle/oracle-database/19/) · [MongoDB](https://www.mongodb.com/docs/manual/) · [MySQL](https://dev.mysql.com/doc/refman/8.4/en/) · [MariaDB](https://mariadb.com/docs/server/) · [Versions PostgreSQL](https://www.postgresql.org/support/versioning/).

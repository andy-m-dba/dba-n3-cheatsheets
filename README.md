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

**Statut :** documentation préparée et contrôlée statiquement, non exécutée sur une instance réelle. Ne jamais publier d'identifiants, chemins internes ou logs bruts.

Documentation : [PostgreSQL](https://www.postgresql.org/docs/18/) · [SQL Server](https://learn.microsoft.com/en-us/sql/sql-server/) · [Versions PostgreSQL](https://www.postgresql.org/support/versioning/).

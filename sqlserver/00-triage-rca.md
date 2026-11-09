# MSSQL-N3-00 — Triage expert & RCA

**Quand ?** Incident critique SQL Server sans cause établie après N1/N2.

### 1. Faits
Heure/fuseau, version, instance, bases, impact, topologie, changements récents, dernière sauvegarde valide.

### 2. État de l'instance
```sql
SELECT SYSDATETIMEOFFSET() AS collecte,
       SERVERPROPERTY('ProductVersion') AS version,
       sqlserver_start_time
FROM sys.dm_os_sys_info;

SELECT name, state_desc, log_reuse_wait_desc
FROM sys.databases ORDER BY name;
```

### 3. Requêtes et attentes actuelles
```sql
SELECT TOP (15) session_id, command, wait_type,
       blocking_session_id, total_elapsed_time AS duree_ms
FROM sys.dm_exec_requests
WHERE session_id <> @@SPID ORDER BY total_elapsed_time DESC;
```

### Décision
- **Preuves :** timeline, compteurs horodatés, alertes et journaux expurgés.
- **Hypothèses :** testables ; un changement récent ne prouve pas une causalité.
- **Action :** évaluer RPO/RTO, approbation, plan de retour, validation applicative.
- **Validation :** métriques revenues au niveau attendu et impact métier levé.

**Escalade :** corruption, perte de données possible, cluster instable ou diagnostic incertain. **RCA :** cause prouvée, facteurs contributifs, correction, prévention, preuves manquantes.

[Source : sys.dm_exec_requests](https://learn.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)

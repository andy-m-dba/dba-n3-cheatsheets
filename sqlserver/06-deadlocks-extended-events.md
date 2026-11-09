# MSSQL-N3-06 — Deadlocks & Extended Events

**Quand ?** Erreurs 1205 répétées, deadlock applicatif suspecté.

### Événements récents de system_health (ring buffer)
```sql
;WITH data AS (
  SELECT CAST(t.target_data AS xml) AS contenu
  FROM sys.dm_xe_session_targets AS t
  JOIN sys.dm_xe_sessions AS s
    ON s.address=t.event_session_address
  WHERE s.name=N'system_health' AND t.target_name=N'ring_buffer'
)
SELECT TOP (10) e.value('@timestamp','datetime') AS heure_utc
FROM data
CROSS APPLY contenu.nodes(
  'RingBufferTarget/event[@name="xml_deadlock_report"]'
) AS n(e)
ORDER BY heure_utc DESC;
```

### Lecture et décision
- `system_health` capte normalement `xml_deadlock_report` ; le ring buffer est **borné** et peut ne plus contenir l'événement.
- Pour le graphe complet, utiliser l'`event_file` autorisé ou SSMS ; **expurger** SQL, paramètres et identités.
- Analyser ordre des verrous, isolation, index et durée des transactions ; une victime n'est pas forcément la cause.
- **Action N3 :** proposer correction SQL/transaction après reproduction et validation. Pas de `KILL` ou de trace globale réflexe.
- **Validation :** erreurs 1205 et temps de réponse diminués sur période comparable.

**Escalade :** deadlocks systèmes ou changement d'isolation à large portée.

[Source : Deadlocks Guide](https://learn.microsoft.com/en-us/sql/relational-databases/sql-server-deadlocks-guide)

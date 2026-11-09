# MDB-N3-05 — RCA performance, plans & WiredTiger

**Quand ?** p99 élevé, CPU/I/O sous pression, changement de plan suspect.

### Mesures du nœud
```javascript
const s = db.serverStatus();
({
  operations: s.opcounters,
  cacheActuel: s.wiredTiger?.cache?.["bytes currently in the cache"],
  cacheMaximum: s.wiredTiger?.cache?.["maximum bytes configured"]
})
```

### Plan estimé d'une requête ciblée
```javascript
db.getSiblingDB("BASE").getCollection("COLLECTION")
  .find({ CHAMP: "VALEUR_TEST" }).explain("queryPlanner")
```

### Lecture et décision
- `opcounters` est **cumulatif** ; comparer les variations sur deux relevés par nœud.
- Cache WiredTiger ≠ RSS total ; confronter page faults, disque, CPU, index et requêtes.
- `queryPlanner` n'exécute pas la requête ; `executionStats` l'exécute et peut consommer des ressources.
- Un `COLLSCAN` n'est pas automatiquement fautif : comparer sélectivité, cardinalités et charge.
- **Action N3 :** une hypothèse à la fois ; index, hint ou réglage seulement après test, autorisation et rollback.
- **Validation :** p95/p99 et throughput sous charge comparable, sans hausse d'erreurs.

**Escalade :** régression structurelle, pression stockage ou optimisation à large portée.

[Sources : serverStatus](https://www.mongodb.com/docs/manual/reference/command/serverstatus/) · [explain](https://www.mongodb.com/docs/manual/reference/command/explain/)

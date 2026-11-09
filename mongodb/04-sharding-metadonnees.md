# MDB-N3-04 — Sharding, balancer & métadonnées

**Quand ?** Routes incohérentes, migrations bloquées, shards déséquilibrés.

### Uniquement via `mongos` (droits autorisés)
```javascript
db.adminCommand({ hello: 1 }).msg  // attendu: "isdbgrid"
db.adminCommand({ listShards: 1 }).shards.map(s => ({
  id: s._id, draining: s.draining
}))
db.adminCommand({ balancerStatus: 1 })
```

### Lecture et décision
- `listShards` décrit les shards **configurés**, pas leur santé.
- `mode=full` n'implique pas une migration active ; regarder `inBalancerRound`.
- Corréler erreurs de migration, shards, config servers et chunks jumbo.
- MongoDB 7+ : `checkMetadataConsistency` via `mongos` peut détecter des incohérences ; **contrôle potentiellement coûteux**, à planifier. Une vérification des index est distincte.
- **Action N3 :** décider correction des métadonnées, migration ou changement de topologie avec sauvegardes et autorisation. Aucun `removeShard` ni `reshardCollection` automatique.
- **Validation :** métadonnées cohérentes, routage, migrations et tests applicatifs.

**Escalade :** config server dégradé, données orphelines, chunk bloqué ou divergence de catalogue.

[Sources : listShards](https://www.mongodb.com/docs/manual/reference/command/listshards/) · [métadonnées](https://www.mongodb.com/docs/manual/reference/command/checkmetadataconsistency/)

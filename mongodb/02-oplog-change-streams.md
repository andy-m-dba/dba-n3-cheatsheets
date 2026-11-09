# MDB-N3-02 — Oplog, initial sync & change streams

**Quand ?** Secondary trop en retard, initial sync répété, CDC non reprenable.

### Sur un `mongod` du replica set
```javascript
rs.printReplicationInfo()
const r = rs.status();
r.members.map(m => ({
  id: m._id, etat: m.stateStr,
  dernierOp: m.optimeDate, source: m.syncSourceHost
}))
```

### Lecture et décision
- Fenêtre oplog = intervalle **constaté**, variable avec le débit d'écritures ; pas durée de rétention garantie.
- Un optime ancien sur tous les nœuds peut refléter l'absence d'écritures.
- Initial sync interrompue : confronter aux logs, au stockage et à la fenêtre du donneur.
- **Change streams :** un resume token n'est réutilisable que si l'historique oplog nécessaire subsiste ; `resumeAfter` ne fonctionne pas après un événement `invalidate`.
- En sharding, examiner les shards et la reprise CDC via `mongos`.
- **Action N3 :** décider réinitialisation d'un secondaire ou reconstruction CDC **avec validation du RPO et du rattrapage métier**.
- **Validation :** réplication stable, reprise CDC et absence de perte/duplication applicative.

**Escalade :** trou d'oplog, token expiré, resynchronisation lourde ou incohérence des consommateurs.

[Sources : oplog](https://www.mongodb.com/docs/manual/reference/method/rs.printreplicationinfo/) · [change streams](https://www.mongodb.com/docs/manual/changestreams/)

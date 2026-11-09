# MDB-N3-01 — Élections, quorum & rollback

**Quand ?** Primaire absent, élections répétées ou rollback suspecté.

### Sur un membre du replica set (pas `mongos`)
```javascript
const r = rs.status();
({
  term: r.term, votesMajorite: r.majorityVoteCount,
  membres: r.members.map(m => ({
    id: m._id, etat: m.stateStr,
    sante: m.health, optime: m.optimeDate
  }))
})
```

### Lecture et décision
- `PRIMARY` est une vue **locale et instantanée** ; croiser plusieurs membres.
- Examiner votes, arbitres, priorités, partition réseau et causes d'élection.
- Un ancien primaire peut **rollback** des écritures non durablement répliquées ; `w:1` ne garantit pas leur conservation.
- Comparer journaux de rollback et `writeConcern` réels avant tout changement de rôle.
- **Action N3 :** rétablissement/reconfiguration seulement sous décision PRA ; ne jamais lancer `rs.stepDown`, `rs.reconfig` ou `force` par réflexe.
- **Validation :** primaire unique, majorité disponible, écritures durables et application rétablie.

**Escalade :** données divergentes, quorum absent, ancien primaire incontrôlé ou perte potentielle.

[Sources : status](https://www.mongodb.com/docs/manual/reference/command/replsetgetstatus/) · [rollbacks](https://www.mongodb.com/docs/v8.0/core/replica-set-rollbacks/)

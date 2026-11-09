# MDB-N3-07 — Transactions distribuées & aborts

**Quand ?** Transactions interrompues, erreurs de commit, saturation multi-shards.

### Sur le `mongos` ou `mongod` concerné
```javascript
const t = db.serverStatus().transactions;
({
  ouvertes: t?.currentOpen, actives: t?.currentActive,
  demarrees: t?.totalStarted, validees: t?.totalCommitted,
  annulees: t?.totalAborted, causes: t?.abortCause
})
```

### Lecture et décision
- `totalStarted/Committed/Aborted` : compteurs **cumulatifs** du processus, pas une photographie des transactions en cours.
- `abortCause` est disponible sur **`mongos`**, pas de manière identique sur `mongod`.
- Transactions multi-shards : distinguer timeouts, conflits, migrations, pertes réseau et retries côté driver.
- Résultat de `commitTransaction` incertain ≠ transaction forcément annulée ; vérifier les labels d'erreur et la stratégie de retry de l'application.
- **Action N3 :** vérifier `writeConcern`, `readConcern` et timeouts ; aucune interruption de transaction en production sans analyse métier.
- **Validation :** chute des aborts par intervalle, flux transactionnels cohérents et validation applicative.

**Escalade :** état de commit incertain, forte contention distribuée ou correction du modèle transactionnel nécessaire.

[Sources : transactions serverStatus](https://www.mongodb.com/docs/manual/reference/command/serverstatus/) · [transactions](https://www.mongodb.com/docs/manual/core/transactions/)

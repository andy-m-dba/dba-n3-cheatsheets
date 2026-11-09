# MDB-N3-00 — Triage expert & RCA

**Quand ?** Incident MongoDB critique non résolu par N1/N2.

### Cadrer
Heure/fuseau, cluster/tenant, nœud ou `mongos`, RPO/RTO, impact, changements, alertes, sauvegarde connue.

### Topologie et indicateurs (mongosh autorisé)
```javascript
const h = db.adminCommand({ hello: 1 });
const s = db.adminCommand({ serverStatus: 1 });
({
  version: s.version, replicaSet: h.setName,
  primary: h.isWritablePrimary, secondary: h.secondary,
  mongos: h.msg === "isdbgrid",
  connexions: s.connections?.current
})
```

### Décision
- `hello` décrit **cette connexion**, pas la disponibilité du cluster complet.
- Recouper nœuds, Atlas/Ops Manager, journaux expurgés et mesures horodatées.
- **Preuves :** chronologie, symptômes, métriques avant/après et services concernés.
- **Hypothèses :** testables ; ne pas confondre corrélation, déclencheur et cause confirmée.
- **Action N3 :** impact/RPO, autorisation, scénario de retour et validation applicative.
- **Validation :** état technique stable et parcours métier rétabli.

**Escalade :** perte de données possible, topologie incertaine ou récupération non maîtrisée. **RCA :** cause prouvée (sinon inconnue), facteurs, preuves et prévention.

[Sources : hello](https://www.mongodb.com/docs/manual/reference/command/hello/) · [serverStatus](https://www.mongodb.com/docs/manual/reference/command/serverstatus/)

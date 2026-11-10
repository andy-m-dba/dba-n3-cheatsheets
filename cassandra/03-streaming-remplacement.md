# CASS-N3-03 — Bootstrap, streaming & remplacement

**Quand ?** Nouveau nœud bloqué en `UJ`, remplacement incertain ou streaming échoué.

### État local et sources de flux (JMX)
```bash
nodetool -h HOST status
nodetool -h HOST netstats
nodetool -h HOST info
```

### Vérifier la capacité hôte
```bash
df -h
df -i
```

### Lecture et décision
- `UJ` = Up/Joining ; `UL` = Up/Leaving ; `UM` = Up/Moving, pas des états stables attendus.
- `netstats` montre les flux **du nœud interrogé** ; croiser donneurs/receveurs et logs.
- Confronter les tokens, DC/rack, seed configuration, version, RF et capacité disque **avant** de modifier la topologie.
- Un redémarrage pendant bootstrap n'est pas une procédure universelle de reprise.
- **Action N3 :** valider remplacement, reconstruction ou retrait dans une fenêtre approuvée, avec risques de disponibilité, streaming et plan de retour.
- Aucun `rebuild`, `removenode`, `decommission` ou `assassinate` automatique.
- **Validation :** nœuds attendus en `UN`, streaming terminé et accès CQL métier conformes.

**Escalade :** token/ring incohérent, source de streaming insuffisante, perte de réplicas ou réparation destructive envisagée.

[Sources : netstats](https://cassandra.apache.org/doc/latest/cassandra/managing/tools/nodetool/netstats.html) · [status](https://cassandra.apache.org/doc/latest/cassandra/managing/tools/nodetool/status.html)

# CASS-N3-01 — Gossip, ring & divergence de schéma

**Quand ?** Nœud `DN`, incohérence de ring, schéma divergent ou service partiellement disponible.

### Croiser plusieurs perspectives JMX
```bash
nodetool -h NODE_A status
nodetool -h NODE_B status
nodetool -h NODE_A describecluster
nodetool -h NODE_A gossipinfo
```

### Lecture et décision
- `UN` = Up/Normal ; `DN` = Down/Normal ; `UJ/UL/UM` signalent des mouvements.
- `status` reflète les informations du **nœud interrogé** ; des divergences entre nœuds sont un indice, pas une preuve de split-brain.
- `describecluster` expose notamment les versions de schéma ; confronter aux migrations en cours.
- `gossipinfo` contient des identités et adresses internes : expurger les preuves.
- Identifier les DC/racks, seed nodes, snitch, état des réseaux et changements de topologie prévus.
- **Action N3 :** correction de configuration/seed/réseau selon procédure et étude de quorum ; aucun `assassinate`, `removenode` ni `decommission` réflexe.
- **Validation :** perspectives concordantes, migrations de schéma achevées et requêtes métiers rétablies.

**Escalade :** partition réseau, nœud irréconciliable, divergence durable ou perte de réplicas.

[Sources : status](https://cassandra.apache.org/doc/latest/cassandra/managing/tools/nodetool/status.html) · [describecluster](https://cassandra.apache.org/doc/latest/cassandra/managing/tools/nodetool/describecluster.html)

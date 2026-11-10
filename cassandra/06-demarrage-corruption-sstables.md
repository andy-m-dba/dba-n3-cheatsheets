# CASS-N3-06 — Démarrage, corruption & SSTables

**Quand ?** Service incapable de démarrer, checksum SSTable invalide, erreurs disque.

### État si JMX répond encore
```bash
nodetool -h HOST info
nodetool -h HOST status
```

### État du système de fichiers (hôte autorisé)
```bash
df -h
df -i
```

### Lecture et décision
- Collecter logs Cassandra/JVM et erreurs OS **expurgés** ; distinguer permissions, disque, JVM, commitlog et SSTables.
- Une erreur SSTable peut concerner un **fichier local** sans démontrer une corruption globale du cluster.
- `nodetool verify` lit les SSTables mais peut être très coûteux ; certaines options (`--dfp`, `--rsc`) **changent l'état** : jamais sans procédure.
- `scrub`, suppression de SSTables ou effacement de commitlogs peuvent provoquer une perte de données.
- **Action N3 :** confirmer source saine, RF, backups et portée ; décider validation ciblée/reconstruction/restauration sous approbation.
- **Validation :** démarrage stable, intégrité contrôlée par méthode approuvée, réplication et application conformes.

**Escalade :** commitlog endommagé, SSTables multiples corrompues, RF insuffisante ou copie saine indisponible.

[Sources : verify](https://cassandra.apache.org/doc/latest/cassandra/managing/tools/nodetool/verify.html) · [nodetool](https://cassandra.apache.org/doc/latest/cassandra/managing/tools/nodetool/nodetool.html)

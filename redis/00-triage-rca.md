# REDIS-N3-00 — Triage expert & RCA

**Quand ?** Incident Redis critique sans cause confirmée après N1/N2.

### Cadrer
Heure/fuseau, service, rôle et topologie (standalone, Sentinel, Cluster, managé), impact, RPO/RTO, changements.

### État du nœud (redis-cli, session ACL autorisée)
```text
PING
INFO server
INFO clients
INFO stats
INFO replication
INFO persistence
```

### Décision
- `PONG` n'atteste ni cohérence des données ni disponibilité de toute la topologie.
- Collecter la **même période** pour le nœud, ses pairs, les clients et l'hôte ; expurger adresses, clés et identifiants.
- **Preuves :** chronologie, mesures avant/après, journaux et changements vérifiés.
- **Hypothèses :** distinguer déclencheur, cause démontrée et facteurs contributifs.
- **Action N3 :** fixer RPO/RTO et seuils, autoriser une correction et documenter le retour.
- **Validation :** stabilité technique et tests applicatifs sur toute la topologie.

**Escalade :** split-brain, perte de clés possible, reprise non maîtrisée. **RCA :** cause confirmée ou inconnue, preuves et prévention.

[Source : INFO](https://redis.io/docs/latest/commands/info/)

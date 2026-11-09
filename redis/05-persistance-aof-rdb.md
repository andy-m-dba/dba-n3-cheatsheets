# REDIS-N3-05 — Persistance, AOF/RDB & démarrage

**Quand ?** `MISCONF`, échec RDB/AOF, instance impossible à relancer.

### Redis répond (sur chaque nœud concerné)
```text
INFO persistence
INFO memory
```

### Stockage self-managed (hôte autorisé)
```bash
df -h
df -i
```

### Lecture et décision
- `rdb_last_bgsave_status` et `aof_last_write_status` renseignent un **état récent**, pas la restaurabilité d'un backup.
- Un `fork` RDB/AOF consomme potentiellement de la mémoire **copy-on-write** et de l'I/O.
- Depuis Redis 7, AOF peut utiliser plusieurs fichiers et un **manifest** ; ne pas corriger un fichier isolé sans maîtriser la chaîne.
- Distinguer données volatiles, RDB, AOF, FS plein, permissions et paramètres de fsync.
- **Action N3 :** conserver une copie de preuve et choisir restauration/contrôle hors ligne approuvé. `redis-check-aof --fix` peut tronquer des données ; ne pas le lancer par défaut.
- **Validation :** redémarrage sain, écriture durable attendue, données et application vérifiées.

**Escalade :** corruption AOF/RDB, données manquantes ou RPO compromis. Ne jamais désactiver une protection pour masquer `MISCONF`.

[Sources : persistance](https://redis.io/docs/latest/management/persistence/) · [INFO](https://redis.io/docs/latest/commands/info/)

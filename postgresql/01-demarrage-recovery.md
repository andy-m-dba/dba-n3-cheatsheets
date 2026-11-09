# PG-N3-01 — Démarrage, crash & recovery

**Quand ?** Instance inaccessible, redémarrages répétés, recovery bloqué.

### Connexion (hôte et port de l'inventaire)
```bash
pg_isready -h HOST -p PORT
```

### Si SQL répond
```sql
SELECT pg_is_in_recovery() AS recovery,
       pg_postmaster_start_time() AS demarrage;
```

### Si SQL est inaccessible (hôte autorisé)
```bash
pg_controldata -D "$PGDATA"
```
Outil **en lecture** du fichier de contrôle : relever état du cluster, checkpoint et timeline. Pendant que le serveur tourne, certaines valeurs peuvent évoluer ; utiliser la version binaire correspondante.

### Lecture et décision
- Recouper journaux, disque, permissions, timeline, recovery attendu et changements récents.
- `pg_isready` teste l'acceptation de connexions, **pas** l'intégrité des données.
- **Ne jamais** effacer `postmaster.pid`, `backup_label`, `standby.signal` ou des WAL pour débloquer le démarrage.
- Interdire `pg_resetwal` ou recovery forcé sans procédure exceptionnelle, sauvegarde de sécurité et décision formelle.
- **Validation :** démarrage stable, checkpoints/WAL cohérents, contrôles SQL et applicatifs.

**Escalade :** checkpoint incohérent, WAL manquant, fichiers critiques absents ou corruption suspectée.

[Sources : pg_controldata](https://www.postgresql.org/docs/18/app-pgcontroldata.html) · [recovery](https://www.postgresql.org/docs/18/wal-recovery.html)

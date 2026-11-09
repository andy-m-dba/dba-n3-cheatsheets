# PG-N3-07 — PITR, sauvegardes & PRA

**Quand ?** Restauration envisagée, RPO/RTO à confirmer, incident de données.

### Vérifier une copie de pg_basebackup munie d'un manifest
```bash
pg_verifybackup /CHEMIN/COPIE_BACKUP
```
Utiliser les binaires de la **version du backup** pour la vérification WAL. Sur archive tar, la vérification WAL n'est pas prise en charge : adapter les options selon la documentation.

### État de l'archivage (si SQL accessible)
```sql
SELECT archived_count, failed_count, last_archived_time, stats_reset
FROM pg_stat_archiver;
```

### Décision de reprise
- Définir cible de restauration, instant/LSN, timeline et perte de données maximale acceptée.
- Établir la chaîne **backup physique + WAL nécessaires** ; vérifier aussi tablespaces et stockage externe.
- `pg_verifybackup` compare fichiers/manifest ; **ne remplace pas** une restauration testée.
- Backups gérés par pgBackRest/Barman/cloud : utiliser leur vérification et leur méthode de restauration, pas supposer `pg_verifybackup` compatible.
- **Action N3 :** répéter la reprise en environnement isolé, puis faire valider le plan de bascule et de retour.
- **Validation :** temps RTO réel, RPO constaté, cohérence et parcours métier testés.

**Escalade :** chaîne WAL incomplète, timeline ambiguë, ou restauration production sans décision métier.

[Sources : pg_verifybackup](https://www.postgresql.org/docs/18/app-pgverifybackup.html) · [PITR](https://www.postgresql.org/docs/18/continuous-archiving.html)

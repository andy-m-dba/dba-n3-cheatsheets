# PG-N3-04 — Intégrité & corruption

**Quand ?** Erreurs de page, index suspect, checksum invalide, I/O anormal.

### Constater la configuration
```sql
SHOW data_checksums;
```
`on` signifie vérification des pages lues, **pas** absence de corruption globale.

### Contrôle ciblé en ligne — fenêtre et autorisation requises
```bash
pg_amcheck --dbname=DATABASE --table='public.TABLE'
```
Vérification potentiellement coûteuse (lectures, locks, ressources) ; requiert les privilèges adaptés. N'exécuter que sur une cible et une fenêtre validées.

### Contrôle hors ligne — cluster arrêté proprement uniquement
```bash
pg_checksums --check -D "$PGDATA"
```
**Ne pas exécuter sur un cluster actif.** Requiert les checksums activés et l'accès au répertoire de données.

### Décision
- Préserver les journaux et le diagnostic stockage. Établir l'étendue avant toute réparation.
- `pg_amcheck` ne couvre pas tous les objets ; une absence d'erreur n'est pas une preuve absolue.
- Ne pas utiliser `zero_damaged_pages`, `ignore_checksum_failure` ni reconstruire aveuglément les fichiers en production.
- **Validation :** contrôles adaptés, réplica/backup vérifié et données métier confirmées.

**Escalade :** corruption confirmée ou fichier critique atteint ; stratégie de restauration soumise à décision formelle.

[Sources : pg_amcheck](https://www.postgresql.org/docs/18/app-pgamcheck.html) · [pg_checksums](https://www.postgresql.org/docs/18/app-pgchecksums.html)

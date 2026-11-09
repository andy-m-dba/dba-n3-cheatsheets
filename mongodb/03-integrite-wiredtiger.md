# MDB-N3-03 — Démarrage & intégrité WiredTiger

**Quand ?** `mongod` ne démarre pas, erreur stockage ou corruption suspectée.

### Si le service répond (diagnostic limité)
```javascript
const h = db.adminCommand({ hello: 1 });
({ primaire: h.isWritablePrimary, secondaire: h.secondary })
db.adminCommand({ getLog: "startupWarnings" }).log?.length
```

### Si inaccessible (self-managed, hôte autorisé)
```bash
df -h
df -i
```
Lire les logs `mongod`/stockage selon la procédure, sans partager de données ou chemins internes.

### Lecture et décision
- `startupWarnings` vide ≠ absence de corruption ; `getLog` lit un cache récent, pas les fichiers complets.
- Vérifier version/FCV, filesystem, checkpoints, disque et sauvegarde avant tentative de démarrage.
- `validate` peut être **lourd, prendre des verrous et corriger certaines incohérences selon la version** : pas un contrôle neutre à lancer sans fenêtre.
- **Action N3 :** définir diagnostic/restauration ciblé, autorisations et copie de sécurité ; `mongod --repair` peut entraîner une perte de données.
- **Validation :** service stable, tests d'intégrité approuvés, données et accès applicatifs confirmés.

**Escalade :** fichiers WiredTiger corrompus, stockage défaillant ou backup incertain. Aucune suppression manuelle de fichiers.

[Sources : validate](https://www.mongodb.com/docs/v8.0/reference/command/validate/) · [getLog](https://www.mongodb.com/docs/v8.0/reference/command/getlog/)

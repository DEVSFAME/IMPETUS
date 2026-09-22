# Transfert du dépôt vers github.com/DEVSFAME/IMPETUS

**Date** : 2026-09-22  
**Auteur** : Cline (Expert DevOps)  
**Contexte** : Migration du projet depuis `github.com/DEVSFAME/MAIL-AUTO` vers le nouveau dépôt vide `git@github.com:DEVSFAME/IMPETUS.git`, avec assainissement complet de l'historique (secrets + fichier volumineux).

---

## 🔍 Diagnostic initial

- Le dossier était déjà un dépôt Git pointant vers `MAIL-AUTO` (branches `V2`, `V3`, `V4`, `V5`, `main`).
- **Secrets présents dans l'historique** :
  - `env1` → copie EXACTE du `.env` (`SMTP_PASS`, `GOOGLE_CLIENT_SECRET`, `OUTLOOK_CLIENT_SECRET`, `DATABASE_URL` Neon, ...).
  - `dev.db` (racine) et `prisma/dev.db` (SQLite : tokens OAuth, contacts, users).
- **Fichiers générés suivis** : `src/generated/` (client Prisma), `graphify-out/cache/` (cache AST).
- **Fichier volumineux** : `Automatisation MAIL.zip` (**66 MB**) ajouté dans le commit `V3`.
- ✅ `*.env` (dont `.env`), `*.xlsx`, `*.pdf` : jamais committés → données personnelles préservées.

---

## ✅ Actions réalisées

1. **Sauvegardes de sécurité** : bundle Git complet (`/tmp/IMPETUS-backup.bundle`, 71 MB) + copie des fichiers sensibles (`/tmp/IMPETUS-safety/`).
2. **`.gitignore` complété** : `env1`, `*.db` (+ `.db-journal/-wal/-shm`), `src/generated/`, `uploads/`, `graphify-out/cache/`, `.obsidian/workspace.json`, `*.code-workspace`, `*.backup`, `*.zip`, `*.rar`, `*.7z`, `*.tar`, `*.tar.gz`, `*.tgz`.
3. **Retrait du suivi** (`git rm --cached`) : 92 fichiers (env1, dev.db, prisma/dev.db, src/generated/, graphify-out/cache/, .obsidian/workspace.json, IMPETUS.code-workspace).
4. **Purge d'historique** (`git-filter-repo`, sur TOUTES les branches) :
   - Passe 1 : `env1`, `dev.db`, `prisma/dev.db`, `src/generated`, `graphify-out/cache`.
   - Passe 2 : `Automatisation MAIL.zip` (66 MB).
5. **Remote** : `origin` → `git@github.com:DEVSFAME/IMPETUS.git`.
6. **Push** : toutes les branches (`V2`, `V3`, `V4`, `V5`, `main`) poussées.

---

## 📁 Fichiers modifiés

| Fichier | Changement |
|---------|-----------|
| `.gitignore` | Ajout des exclusions (secrets, bases DB, Prisma généré, uploads, cache, archives) |
| Historique Git | 2 réécritures via `git-filter-repo` (secrets + zip 66 MB) |

**Aucun fichier source applicatif modifié** (`server.js`, `src/*`, `public/*` inchangés).

---

## 📊 État actuel

- [x] Dépôt transféré vers `github.com/DEVSFAME/IMPETUS`
- [x] 5 branches présentes sur le distant (V2, V3, V4, V5, main)
- [x] 0 secret dans l'historique (vérifié : `git log --all -- env1 dev.db prisma/dev.db` vide)
- [x] Taille `.git` : **66 MB → 2.4 MB**
- [x] Working tree propre et synchronisé (`origin/V5`)
- [ ] ⚠️ **ROTATION DES SECRETS À FAIRE** : les identifiants exposés dans `MAIL-AUTO` doivent être régénérés (SMTP_PASS, GOOGLE_CLIENT_SECRET, OUTLOOK_CLIENT_SECRET, DATABASE_URL).
- [ ] `graphify update` : commande indisponible sur le poste actuel (à relancer une fois l'outil installé ; le graphe reste celui du 2026-08-03).
- [ ] Branche `main` (par défaut) : contient l'état initial "MailCandid", pas la dernière version (V5).

---

**Note importante** : la branche `main` du nouveau dépôt (branche par défaut GitHub) pointe sur l'ancien commit initial. Pour qu'un clone donne directement la dernière version, faire pointer `main` sur `V5` ou basculer la branche par défaut dans les paramètres GitHub.

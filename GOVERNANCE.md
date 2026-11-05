# 🏛️ Governance & CI/CD Setup

**Status:** Infrastructure opérationnelle ✅

## Architecture

```
main (Production)
  ↑
  └─── PR Review + CI ✅

develop (Integration)
  ↑
  └─── feature/* branches
       ├─ Pre-commit: ESLint + Prettier ✅
       ├─ CI: GitHub Actions ✅
       └─ Protection: Rulesets ✅
```

## Protections Activées

### 🔒 Branche `main`

- Pas de push direct
- PR obligatoire
- All CI checks verts (ESLint, Prettier, Build)
- Toutes les branches à jour

### 🔒 Branche `develop`

- Pas de push direct
- PR obligatoire
- All CI checks verts
- Toutes les branches à jour

## Quality Checks

### Local (Pre-commit Hook)

```bash
npm run lint:fix      # ESLint auto-fix
npm run format        # Prettier formatting
```

Fichier de config: `.lintstagedrc.json`

### CI Pipeline

Déclenché à chaque push et PR vers `main`/`develop`:

1. **Lint** → `npm run lint`
2. **Format Check** → `npm run format:check`
3. **Build** → `npm run build`

Workflow: `.github/workflows/ci.yml`

## Workflow Quotidien

### Démarrer une feature

```bash
git checkout develop
git pull origin develop
git checkout -b feature/my-feature
```

### Avant commit

```bash
npm run lint:fix
npm run format
```

### Push & PR

```bash
git push -u origin feature/my-feature
# Ouvrez PR sur GitHub
# Attendez: ESLint ✅ → Prettier ✅ → Build ✅
# Merge quand tous les checks sont verts
```

### Intégration en main

```bash
# PR from develop → main (une fois par release)
```

## Escalade: Ce qui est Bloqué

| Action                              | Bloqué? | Raison                        |
| ----------------------------------- | ------- | ----------------------------- |
| `git push origin develop` (direct)  | ✅ YES  | Ruleset: PR required          |
| Commit avec erreur ESLint           | ✅ YES  | Pre-commit hook               |
| Merger PR avec ESLint rouge         | ✅ YES  | Ruleset: CI check requis      |
| `git push --force` sur main/develop | ✅ YES  | Ruleset: Force pushes bloqués |
| Supprimer main ou develop           | ✅ YES  | Ruleset: Deletions bloqués    |

## Teams & Roles

| Team       | Purpose               | Permissions |
| ---------- | --------------------- | ----------- |
| tech-leads | Architecture, reviews | Admin       |
| data       | Data pipelines        | Maintain    |
| product    | Features, specs       | Triage      |
| evaluation | Assessment            | Read        |

Assignés dans [Teams](https://github.com/orgs/Hetic-Hackathon-Givaudan-2026-Ousmane/teams)

## Troubleshooting

### Pre-commit hook refuse mon commit

```
✖ prettier --write
```

**Solution:**

```bash
npm run format
npm run lint:fix
git add .
git commit -m "..."
```

### CI check échoue

1. Rendez-vous sur **Actions** → Dernier workflow
2. Vérifiez les logs (ESLint errors affichés)
3. Corrigez en local:

```bash
npm run lint:fix
npm run format
git add .
git commit --amend
git push origin feature/... --force-with-lease
```

### "Cannot merge: required status checks not passing"

- Attendez que le CI finisse
- Cliquez "Re-run all jobs" si c'est bloqué
- Vérifiez les logs du workflow

---

**Setup Date:** 2026-11-05
**Next Review:** Post-hackathon evaluation

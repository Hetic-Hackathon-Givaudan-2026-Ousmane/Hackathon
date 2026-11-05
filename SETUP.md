# Setup & Governance Guide

## Branchement & Fusion

- **main**: Production. Créée depuis `develop` via PR approuvée.
- **develop**: Intégration. Branche par défaut pour les features.
- **feature/XXX**: Votre travail. Créée depuis `develop`, fusionnée via PR.

## Règles de Fusion

1. **Pas de push direct** sur `main` ou `develop`
2. **Tous les commits** passent par une PR
3. **Tous les checks** doivent être verts (ESLint, Prettier, Build)
4. **Au minimum 1 review** avant fusion (CODEOWNERS appliqué)

## Linting Local

```bash
npm run lint       # Vérifier
npm run lint:fix   # Corriger
npm run format     # Formater avec Prettier
```

## Pre-commit Hook

Le hook `husky/pre-commit` lance automatiquement:

- `eslint --fix`
- `prettier --write`

Si votre commit contient une erreur ESLint, il sera refusé.

**Testez-le:**

```bash
# Ajoutez une erreur exprès
echo "console.log('hello')" >> api/src/index.ts
git add api/src/index.ts
git commit -m "test: add intentional error"  # ❌ Commit refusé
```

## CI Workflow

Le workflow `.github/workflows/ci.yml` exécute à chaque push et PR:

- Lint avec ESLint
- Vérification Prettier
- Build du projet

**État du workflow:** Allez sur Actions > CI

## Escalade de Rupture

Si quelqu'un essaie de:

- ❌ Pousser directement sur `main`
- ❌ Merger une PR sans check vert
- ❌ Committer du code mal formaté

Tout sera **automatiquement bloqué par les rulesets**.

---

**Intervenant:** Appelez-nous une fois que cette stack est validée! 🚀

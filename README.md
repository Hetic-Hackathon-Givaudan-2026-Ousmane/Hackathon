# 🍷 Hackathon Givaudan 2026

> **Infrastructure GitHub sécurisée avec ESLint, Prettier, CI/CD et branch protection**

## 📋 Table des matières

- [Architecture](#architecture)
- [Démarrage Rapide](#démarrage-rapide)
- [Commandes](#commandes)
- [Gouvernance GitHub](#gouvernance-github)
- [Qualité de Code](#qualité-de-code)
- [Workflow Quotidien](#workflow-quotidien)
- [Base de Données](#base-de-données)
- [Troubleshooting](#troubleshooting)

---

## 🏗️ Architecture

```
Monolithe Full-Stack:
├── API (Express + TypeScript)
├── Front (Vite + TypeScript)
├── Base de données (PostgreSQL 17)
└── Dev Container (Docker)
```

### Structure du Projet

```
.devcontainer/          Dev container configuration
.github/
  ├── workflows/ci.yml  GitHub Actions CI/CD
  ├── CODEOWNERS        Code ownership rules
  ├── SECURITY.md       Security & governance
  └── pull_request_template.md
.husky/                 Git hooks (pre-commit)
api/                    Express API (TypeScript)
web/                    Vite Frontend (TypeScript)
db/                     Database init scripts
eslint.config.js        ESLint configuration (v9)
.prettierrc.json        Prettier configuration
package.json            Root workspace config
```

---

## 🚀 Démarrage Rapide

### Prérequis

- VS Code avec Docker
- Git

### Installation

1. **Ouvrir dans Dev Container**

   ```bash
   # VS Code: Cmd+Shift+P → "Dev Containers: Reopen in Container"
   ```

2. **Configurer l'environnement**

   ```bash
   cp api/.env.example api/.env
   ```

3. **Installer les dépendances**

   ```bash
   npm ci
   ```

4. **Démarrer le serveur**

   ```bash
   npm run dev
   ```

5. **Accéder à l'application**
   - **Front:** http://localhost:5173
   - **API Health:** http://localhost:3000/api/health
   - **API Questions:** http://localhost:3000/api/questions

---

## 💻 Commandes

### Développement

```bash
npm run dev              # Lance API + Frontend (concurrent)
npm run dev -w api      # Lance seulement l'API
npm run dev -w web      # Lance seulement le Frontend
```

### Linting & Formatage

```bash
npm run lint            # Vérifier le code avec ESLint
npm run lint:fix        # Auto-corriger les erreurs ESLint
npm run format          # Formater avec Prettier
npm run format:check    # Vérifier le formatage Prettier
```

### Build

```bash
npm run build           # Compiler API + Frontend
npm run build -w api    # Compiler seulement l'API
npm run build -w web    # Compiler seulement le Frontend
```

---

## 🔐 Gouvernance GitHub

### Branches Protégées

- **`main`** - Production (protected)
- **`develop`** - Integration (protected)

### Protections Activées

✅ **Local (Pre-commit Hook)**

- ESLint validation
- Prettier formatting
- Commit refusé si erreur

✅ **CI/CD (GitHub Actions)**

- Lint check (ESLint)
- Format check (Prettier)
- Build check (npm run build)

✅ **Remote (Rulesets)**

- PR obligatoire avant merge
- Tous les checks CI doivent passer
- Branch à jour avant merge
- Force push bloqué
- Deletion bloquée

### Code Owners

Fichier: `.github/CODEOWNERS`

Les reviewers sont assignés automatiquement selon les rules définies. Consultez le fichier pour les détails.

---

## ✨ Qualité de Code

### ESLint

Configuration: `eslint.config.js`

- **Parser:** TypeScript
- **Extends:** ESLint Recommended + TypeScript Recommended
- **Plugins:** Prettier integration
- **Rules:** No unused vars (warn), No `any` (warn)

```bash
npm run lint:fix  # Auto-correct all errors
```

### Prettier

Configuration: `.prettierrc.json`

```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 80
}
```

```bash
npm run format    # Format all files
```

### Husky Pre-commit Hook

Déclenché automatiquement avant chaque commit:

1. ESLint check
2. Prettier format
3. Si erreur → **commit refusé** ❌

Fichier: `.husky/pre-commit`
Config: `.lintstagedrc.json`

---

## 📝 Workflow Quotidien

### 1. Créer une Feature

```bash
git checkout develop
git pull origin develop
git checkout -b feature/ma-feature
```

### 2. Développer

```bash
npm run dev              # Lancez le serveur
# ... écrivez du code ...
npm run lint:fix         # Avant de committer
npm run format           # Formatter
```

### 3. Committer

```bash
git add .
git commit -m "feat: description courte"
# ✅ Pre-commit hook lance ESLint + Prettier
# ❌ Si erreur → commit refusé
```

### 4. Pousser & PR

```bash
git push -u origin feature/ma-feature
# Ouvrez une PR sur GitHub
```

### 5. Validation PR

- ✅ ESLint check vert
- ✅ Prettier check vert
- ✅ Build réussi
- ✅ Approval d'au moins 1 reviewer (CODEOWNERS)
- ✅ Branch à jour

### 6. Merger

```bash
# Cliquez "Squash and merge" sur GitHub
```

---

## 🗄️ Base de Données

### Architecture

```
PostgreSQL 17 dans Docker (compose.yaml)
Scripts init: db/init/
  ├── 001-schema.sql      (Tables & relations)
  └── 002-seed.sql        (Données de test)
```

### Au Premier Démarrage

Les scripts `db/init/` sont joués automatiquement si `db/data/` est vide.

### Rejouer les Scripts Manuellement

```bash
psql postgresql://app:app@localhost:5432/app \
  -f db/init/001-schema.sql \
  -f db/init/002-seed.sql
```

### Connection

```bash
# Dans le Dev Container:
psql postgresql://app:app@localhost:5432/app

# Requête rapide:
psql postgresql://app:app@localhost:5432/app -c "SELECT * FROM users;"
```

---

## 🐛 Troubleshooting

### Pre-commit hook refuse mon commit

**Symptôme:** `✖ prettier --write` or `✖ eslint --fix`

**Solution:**

```bash
npm run format
npm run lint:fix
git add .
git commit -m "..."
```

### Build échoue localement mais passe en CI

**Cause:** Différences d'environnement ou cache npm

**Solution:**

```bash
rm -rf node_modules package-lock.json
npm ci
npm run build
```

### Port 5173 ou 3000 déjà utilisé

**Solution:**

```bash
# Vérifier les processus
lsof -i :5173
lsof -i :3000

# Tuer le processus
kill -9 <PID>
```

### Dev container ne démarre pas

**Solution:**

1. Cmd+Shift+P → "Dev Containers: Rebuild Container"
2. Attendez que Docker recompile l'image
3. Relancez

### Fichiers formatés différemment en CI

**Cause:** Versions Prettier différentes

**Solution:**

```bash
npm run format:check
npm run format  # Si nécessaire
git add .
git commit --amend
```

---

## 📚 Documentation

- [SETUP.md](./SETUP.md) - Guide d'utilisation local (en branch)
- [GOVERNANCE.md](./GOVERNANCE.md) - Architecture & workflow (en branch)
- [.github/SECURITY.md](./.github/SECURITY.md) - Règles de sécurité
- [.github/CODEOWNERS](./.github/CODEOWNERS) - Ownership rules

---

## 🔗 Ressources

| Ressource  | Lien                                                                        |
| ---------- | --------------------------------------------------------------------------- |
| Repository | https://github.com/Hetic-Hackathon-Givaudan-2026-Ousmane/Hackathon          |
| Settings   | https://github.com/Hetic-Hackathon-Givaudan-2026-Ousmane/Hackathon/settings |
| Actions    | https://github.com/Hetic-Hackathon-Givaudan-2026-Ousmane/Hackathon/actions  |
| Issues     | https://github.com/Hetic-Hackathon-Givaudan-2026-Ousmane/Hackathon/issues   |

---

## 📞 Besoin d'aide?

- Consultez [.github/SECURITY.md](./.github/SECURITY.md) pour les questions de sécurité
- Ouvrez une [Issue](https://github.com/Hetic-Hackathon-Givaudan-2026-Ousmane/Hackathon/issues)
- Lancez `npm run lint --help` pour les options ESLint

---

## 📄 License

Hackathon Givaudan 2026 - Ousmane

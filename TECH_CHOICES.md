# Justification des Choix Technologiques

**Tech-Lead:** Ousmane Sacko  
**Date:** 2026-11-05  
**Hackathon:** Givaudan 2026

---

## 📋 Executive Summary

Ce document justifie les choix architecturaux et technologiques du hackathon. La stack retenue privilégie **la scalabilité**, **la maintenabilité**, et **la rapidité de développement** sur un weekend de hackathon.

---

## 🏗️ Architecture Générale

### Choix: Monolithe Full-Stack (API + Frontend + DB)

**Justification:**

- ✅ **Déploiement simplifié** pour un hackathon (pas d'infra complexe)
- ✅ **Contexte partagé** entre front et back (même repo, même type)
- ✅ **Développement rapide** (une équipe, un flux)
- ✅ **Scaling possible** (séparation API/Web facile à faire)

**Alternative rejetée:** Microservices

- ❌ Overhead infra/devops trop lourd pour 2-3 jours
- ❌ Complexité communication inter-services
- ❌ Pas nécessaire pour le scope hackathon

---

## 🔧 Backend: Express + TypeScript

### Choix: Express.js

**Justification:**

- ✅ **Minimaliste & flexible** - contrôle total sur la structure
- ✅ **Écosystème mature** - middlewares et packages abondants
- ✅ **Performance** - léger et rapide (parfait pour API REST)
- ✅ **Apprentissage rapide** - équipe peut démarrer immédiatement
- ✅ **Intégration IA facile** - appels HTTP simples vers LLM APIs

**Alternatives rejetées:**

- ❌ **Fastify** - plus rapide mais moins de ressources/tutos
- ❌ **NestJS** - trop verbeux pour hackathon, meilleur pour projets long-terme
- ❌ **Django/Flask** - changement de langage (pas TypeScript)

### Choix: TypeScript

**Justification:**

- ✅ **Type Safety** - détecte bugs avant runtime (crucial)
- ✅ **Meilleure DX** - autocomplete et refactoring dans VSCode
- ✅ **Documentation auto** - types = documentation en code
- ✅ **Maintenabilité** - code plus clair et robuste
- ✅ **Full-stack typé** - même langage front et back

**Overhead:** Minimal (compilation rapide, tooling moderne)

---

## 🎨 Frontend: Vite + React/Vue + TypeScript

### Choix: Vite

**Justification:**

- ✅ **HMR ultra-rapide** - feedback immédiat pendant dev (crucial pour hackathon)
- ✅ **Build rapide** - production builds en <1s
- ✅ **Zéro config** - prêt à l'emploi
- ✅ **ESM native** - moderne et performant
- ✅ **Petit bundle** - pages se chargent vite (UX importante)

**Alternatives rejetées:**

- ❌ **Create React App** - build lent, HMR lent, obsolète
- ❌ **Next.js** - trop heavy pour simple IA chatbot
- ❌ **Webpack** - trop de config

### Choix: TypeScript (même raison que backend)

---

## 💾 Base de Données: PostgreSQL

### Choix: PostgreSQL 17

**Justification:**

- ✅ **Relationnel solide** - données structurées (questions, réponses)
- ✅ **Recherche full-text** - requêtes complexes si besoin
- ✅ **JSONB support** - flexible pour données semi-structurées (prompts, réponses IA)
- ✅ **Performant** - milieu hackathon, scale bien
- ✅ **Gratuit et open-source** - coût nul
- ✅ **Dev Container** - intégré dans compose.yaml

**Alternatives rejetées:**

- ❌ **MongoDB** - schémas inconsistents, pas bon pour relations
- ❌ **SQLite** - limité en concurrence, pas bon pour prod
- ❌ **Firebase** - lock-in propriétaire, coûts imprévisibles

---

## 🤖 Intégration IA

### Architecture

```
Frontend (Vite)
    ↓ HTTP POST /api/ask
Backend (Express)
    ↓ HTTP/API Call
LLM Provider (OpenAI / Anthropic / etc)
    ↓ Streaming response
Frontend (WebSocket / SSE)
    ↓ Affiche réponse en temps réel
```

### Choix: API Call Direct

**Justification:**

- ✅ **Temps de latence minimal** - pas de couche intermédiaire
- ✅ **Contrôle budget** - rate limiting au niveau backend
- ✅ **Sécurité** - API key protégée côté serveur
- ✅ **Flexibility** - swappable en 5 min (OpenAI → Anthropic → Ollama)

### Provider: Flexible (OpenAI or Anthropic)

**Justification multi-provider:**

- ✅ **Budget** - tarifs varient, pouvoir switcher vite
- ✅ **Disponibilité** - si un provider est down, switch à l'autre
- ✅ **Perf** - Anthropic plus long-context, OpenAI plus rapide
- ✅ **Légal** - pas de dépendance à un seul vendor

**Fallback local:** Ollama si budget/internet limitées

---

## 🔐 Infrastructure & Qualité

### Choix: Dev Container + Docker Compose

**Justification:**

- ✅ **Reproductibilité** - "works on my machine" = "works everywhere"
- ✅ **Onboarding facile** - nouveau dev = 1 click dans VSCode
- ✅ **Même env local/CI** - pas de surprises en prod
- ✅ **Scaling** - facile de ajouter services (Redis, Elasticsearch, etc)

### Choix: ESLint + Prettier + Husky

**Justification:**

- ✅ **Qualité garantie** - code formaté automatiquement
- ✅ **Pas de débats style** - ESLint décide, fini
- ✅ **Bugs évités** - ESLint détecte pièges JS courants
- ✅ **CI/CD robuste** - checks refusent code mal formaté
- ✅ **Onboarding** - nouvel arrivant sait les règles immédiatement

### Choix: GitHub Actions CI/CD

**Justification:**

- ✅ **Intégré GitHub** - pas d'outil externe à configurer
- ✅ **Gratuit** - 2000 min/mois suffisent pour hackathon
- ✅ **Checks obligatoires** - règlesets bloquent PR rouge
- ✅ **Simple** - YAML facile à maintenir

---

## 🎯 Ce Qui N'Est PAS Inclus (et Pourquoi)

### ❌ Authentification / Autorisation Complexe

**Raison:** Scope hackathon = MVP, pas multi-user production  
**Futur:** Ajouter Auth0/NextAuth si besoin

### ❌ Base de Données en Cache (Redis)

**Raison:** PostgreSQL suffit pour le volume de données hackathon  
**Futur:** Ajouter Redis si perf critique

### ❌ Monitoring / Logging Central (Datadog, Sentry)

**Raison:** Dev Container logs suffisent pour déboguer  
**Futur:** Ajouter après hackathon si en production

### ❌ API GraphQL

**Raison:** REST suffit, graphql = complexité inutile pour hackathon  
**Scope:** Simple CRUD sur questions + appel IA

### ❌ Testing Avancé (E2E, Load Testing)

**Raison:** Hackathon = speed > coverage  
**Quand:** Tests unitaires après MVP stable

### ❌ Infrastructure Complexe (K8s, Terraform, etc)

**Raison:** Overkill pour hackathon sur 1 machine  
**Quand:** Ajouter à la prochaine étape (scaling)

---

## 📊 Comparaison Budget & Coûts

### Infrastructure Hackathon

| Ressource              | Coût   | Justification                    |
| ---------------------- | ------ | -------------------------------- |
| Dev Container (local)  | $0     | Gratuit (Docker Desktop)         |
| PostgreSQL 17          | $0     | Open-source, inclus compose.yaml |
| GitHub                 | $0     | Gratuit pour public repos        |
| CI/CD (Actions)        | $0     | 2000 min/mois = gratuit          |
| **LLM API (Optional)** | ~$5    | 1000 appels test = minimal       |
| **TOTAL**              | **$5** | Hackathon = quasi-gratuit        |

### Scalabilité Futur

| Composant | Pour Passer 100x Scaling                 |
| --------- | ---------------------------------------- |
| API       | Horizontal scaling (PM2/Docker replicas) |
| DB        | Upgrade PostgreSQL ou Managed (RDS)      |
| LLM       | Same (APIs scale infiniment)             |
| Storage   | Add S3/Blob storage si images            |

**Conclusion:** Stack scale facilement sans refactoring majeur

---

## ✅ Checklist Tech-Lead

- ✅ Stack choisie basée sur **critères clairs** (speed, scale, cost)
- ✅ Alternatives **évaluées et rejetées** avec raisons
- ✅ Technos **justifiées** pour hackathon (pas over-engineered)
- ✅ Infrastructure **reproductible** (Dev Container)
- ✅ **QA/Testing** en place dès le départ (ESLint, Prettier, CI)
- ✅ **Roadmap** claire pour scaling futur
- ✅ **Coûts** minimisés (serverless/free tier)

---

## 🚀 Décision Finale

**La stack est prête pour un hackathon MVP de haut niveau, avec scale-path clair vers production.**

Toutes les décisions privilégient:

1. **Rapidité de dev** (HMR, TypeScript, etc)
2. **Qualité de code** (ESLint, tests, CI/CD)
3. **Maintenabilité** (mono-repo, Docker, docs)
4. **Coûts** (gratuit/cheap)

---

**Signé:** Tech-Lead  
**Date:** 2026-11-05

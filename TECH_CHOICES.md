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
LLM Provider (OpenAI / Anthropic / Ollama / Équipe Data)
    ↓ Streaming response
Frontend (WebSocket / SSE)
    ↓ Affiche réponse en temps réel
```

### Choix: API Call Direct

**Justification:**

- ✅ **Temps de latence minimal** - pas de couche intermédiaire
- ✅ **Contrôle budget** - rate limiting au niveau backend
- ✅ **Sécurité** - API key protégée côté serveur
- ✅ **Flexibility** - swappable en 5 min entre providers

---

## 🔤 Options IA Disponibles (4 Choix)

### Option 1: API Commerciale (OpenAI / Anthropic) ⭐ RECOMMANDÉE

**Mise en Place:**

```bash
# 1. Obtenir clé API (https://platform.openai.com/keys)
# 2. Ajouter à api/.env
OPENAI_API_KEY=sk-...

# 3. Appel dans Express
const response = await openai.chat.completions.create({
  model: "gpt-4",
  messages: [{ role: "user", content: question }]
});
```

**Pros:**

- ✅ **Qualité élevée** - GPT-4, Claude 3, modèles SOTA
- ✅ **Rapidité** - réponses en 1-2s
- ✅ **Zéro setup** - juste une clé API
- ✅ **Contexte long** - jusqu'à 200k tokens
- ✅ **Production-ready** - infra robuste

**Cons:**

- ❌ **Coût** - ~$0.01-0.03 par requête
- ❌ **Données partent** - hors de votre contrôle
- ❌ **Internet requis** - pas d'offline

**Budget Hackathon:** ~$5-20 (1000-2000 appels tests)

**Quand choisir:** Meilleure qualité, budget OK, démo impressionnante

---

### Option 2: Ollama sur Votre Machine (Local)

**Mise en Place:**

```bash
# 1. Installer Ollama (https://ollama.com/)
# 2. Télécharger un modèle
ollama pull mistral        # 7B (fast)
ollama pull llama2         # 7B (good quality)
ollama pull neural-chat    # 7B (chatbot optimized)

# 3. Appel dans Express
const response = await fetch('http://localhost:11434/api/generate', {
  method: 'POST',
  body: JSON.stringify({
    model: 'mistral',
    prompt: question
  })
});
```

**Pros:**

- ✅ **Gratuit** - aucun coût d'API
- ✅ **Données locales** - tout reste sur votre machine
- ✅ **Offline** - fonctionne sans Internet
- ✅ **Contrôle total** - modèle peut être fine-tuned
- ✅ **Simple** - ~5 min setup

**Cons:**

- ❌ **Qualité modeste** - petits modèles (7B max réaliste)
- ❌ **Lent sans GPU** - 5-30s par réponse sur CPU
- ❌ **GPU macOS** - meilleur perf sur Apple Silicon
- ❌ **GPU Linux/Windows** - besoin NVIDIA CUDA
- ❌ **Usage RAM** - 7B = 4-8GB RAM requis

**Performance:**

- Apple Silicon: Rapide (GPU natif) ⭐
- NVIDIA GPU: Très rapide (CUDA)
- CPU seul: Lent (5-30s par réponse)

**Quand choisir:** Pas de budget, données sensibles, démo locale

---

### Option 3: Ollama en Conteneur (Docker Compose) ⭐ RECOMMANDÉE

**Mise en Place:**

```yaml
# compose.yaml (ajouter)
services:
  ollama:
    image: ollama/ollama:latest
    ports:
      - '11434:11434'
    volumes:
      - ollama_data:/root/.ollama
    environment:
      - OLLAMA_HOST=0.0.0.0:11434

volumes:
  ollama_data:
```

```bash
# 1. Démarrer le container
docker compose up ollama

# 2. Télécharger modèle (dans le container)
docker exec <container_id> ollama pull mistral

# 3. Appel dans Express
const response = await fetch('http://ollama:11434/api/generate', {
  method: 'POST',
  body: JSON.stringify({
    model: 'mistral',
    prompt: question
  })
});
```

**Pros:**

- ✅ **Gratuit** - aucun coût
- ✅ **Reproductible** - même infra partout (Dev Container)
- ✅ **Données locales** - tout reste sur votre machine
- ✅ **Scalable** - facile de changer le modèle
- ✅ **Intégré** - compose.yaml = tout ensemble

**Cons:**

- ❌ **Qualité modeste** - petits modèles (7B)
- ❌ **Lent sans GPU** - 5-30s par réponse
- ❌ **Stockage** - modèle = 4-8GB d'espace disque
- ❌ **Infra plus lourd** - un service de plus à maintenir

**Quand choisir:** Meilleur compromis pour hackathon (setup facile + data local)

---

### Option 4: API Équipe Data/IA (Tunnel ngrok)

**Mise en Place:**

```bash
# 1. Contacter équipe Data/IA
# 2. Recevoir URL ngrok
export IA_API_URL=https://xxx-xxx.ngrok.io

# 3. Appel dans Express
const response = await fetch(`${process.env.IA_API_URL}/ask`, {
  method: 'POST',
  body: JSON.stringify({ question })
});
```

**Pros:**

- ✅ **Gratuit** - dans les limites de ngrok
- ✅ **Qualité variable** - celle du modèle de l'équipe
- ✅ **Support** - équipe Data/IA peut aider

**Cons:**

- ❌ **Dépendance externe** - pas contrôle sur l'infra
- ❌ **Latence** - aller-retour Internet + ngrok
- ❌ **Données partagées** - passent par ngrok
- ❌ **Limites ngrok** - bande passante/requêtes limitées
- ❌ **Instabilité** - tunnel peut crash

**Quand choisir:** Collaboration équipe Data/IA, modèle custom spécialisé

---

## 📊 Comparaison Détaillée

| Critère         | API Commerciale   | Ollama Local           | Ollama Conteneur       | Équipe Data/IA  |
| --------------- | ----------------- | ---------------------- | ---------------------- | --------------- |
| **Coût**        | ~$5-20/hackathon  | Gratuit                | Gratuit                | Gratuit*        |
| **Qualité**     | ⭐⭐⭐⭐⭐ Élevée | ⭐⭐ Modeste           | ⭐⭐ Modeste           | ⭐⭐⭐ Variable |
| **Vitesse**     | 1-2s              | 5-30s (CPU) / 1s (GPU) | 5-30s (CPU) / 1s (GPU) | 2-5s + latence  |
| **Setup**       | 5 min             | 10 min                 | 5 min                  | 15 min          |
| **Données**     | Quittent machine  | Restent local          | Restent local          | Par ngrok       |
| **Offline**     | Non               | Oui                    | Oui                    | Non             |
| **Scalabilité** | Infinie           | Limitée                | Limitée                | Dépend équipe   |

---

## 🎯 Recommandation Tech-Lead

**Pour hackathon, nous recommandons:**

### 🥇 Option 3: Ollama Conteneur (PRÉFÉRÉ)

- **Avantages:** Setup facile, reproductible, données locales, gratuit
- **Usage:** Démo interne, tests, présentations
- **Setup:** 5 min (juste ajouter service compose.yaml)

### 🥈 Option 1: API Commerciale (SI BUDGET OK)

- **Avantages:** Meilleure qualité, réponses rapides, impressionnant
- **Usage:** Démo finale, présentation clients
- **Setup:** 5 min (juste clé API)

### 🥉 Option 4: Équipe Data/IA (SI COLLABORATION)

- **Avantages:** Modèle spécialisé, support équipe
- **Usage:** Feedback, améliorations post-hackathon

---

## 🚀 Implementation Path

```javascript
// Backend abstraction (easy to swap)

class IAProvider {
  async ask(question: string): Promise<string> {
    if (process.env.IA_PROVIDER === 'openai') {
      return this.askOpenAI(question);
    } else if (process.env.IA_PROVIDER === 'ollama') {
      return this.askOllama(question);
    }
  }
}

// Dans Express
app.post('/api/ask', async (req, res) => {
  const answer = await ia.ask(req.body.question);
  res.json({ answer });
});
```

**Changement provider:** Modifier une variable d'env + 1 ligne dans le code

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

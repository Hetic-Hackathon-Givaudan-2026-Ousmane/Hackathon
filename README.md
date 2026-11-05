# Hackathon

Monolithe : une API Express, un front Vite et une base PostgreSQL, dans un dev container.

```
.devcontainer/   le dev container
compose.yaml     la stack : dev + db (PostgreSQL 17)
db/init/         schéma et données de test, joués au premier démarrage
api/             l'API (Express, TypeScript)
web/             le front (Vite, TypeScript)
```

## Démarrer

1. Ouvrir le dépôt dans VS Code et choisir « Reopen in Container ».
2. Dans le terminal du conteneur :

```
cp api/.env.example api/.env
npm ci
npm run dev
```

- Front : http://localhost:5173
- API : http://localhost:3000/api/health et http://localhost:3000/api/questions

## Scripts

- `npm run dev` : lance l'API et le front
- `npm run build` : compile les deux

## Base de données

Les scripts de `db/init/` ne sont joués que si `db/data` est vide. Pour les rejouer à la main :

```
psql postgres://app:app@db:5432/app -f db/init/001-schema.sql -f db/init/002-seed.sql
```

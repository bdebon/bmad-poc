# Epic 1: Foundation & Scraping

**Goal:** Mettre en place la structure du projet TypeScript, configurer Bright Data, et implémenter les scrapers pour Reddit et Hacker News. À la fin de cet epic, on peut récupérer des posts bruts des deux sources.

## Story 1.1: Project Setup

> As a developer,
> I want a TypeScript project properly configured,
> so that I can start implementing the scraping logic.

**Acceptance Criteria:**
1. Le projet est initialisé avec `npm init` et TypeScript configuré (`tsconfig.json`)
2. ESLint + Prettier sont configurés pour la qualité du code
3. Structure de dossiers créée : `src/`, `src/scrapers/`, `src/config/`, `src/types/`
4. Un fichier `config.yaml` existe avec les paramètres de base (liste des subreddits, etc.)
5. Un script `npm run dev` permet d'exécuter le code en local
6. Le `.gitignore` exclut `node_modules/`, `.env`, et les fichiers de build
7. Un fichier `.env.example` documente les variables d'environnement requises

## Story 1.2: Reddit Scraper

> As a developer,
> I want to scrape posts from configured subreddits via Bright Data,
> so that I can collect raw content for analysis.

**Acceptance Criteria:**
1. Le scraper se connecte à Bright Data avec les credentials depuis `.env`
2. Les subreddits à scraper sont configurables via `config.yaml`
3. Le scraper récupère les top posts (hot/top) des dernières 24h de chaque subreddit
4. Chaque post contient : titre, URL, score, nombre de commentaires, subreddit source
5. Les posts sont retournés dans un format TypeScript typé (`RedditPost[]`)
6. Les erreurs de connexion/scraping sont loggées proprement
7. Un test unitaire valide le parsing des données

## Story 1.3: Hacker News Scraper

> As a developer,
> I want to scrape the Hacker News front page via Bright Data,
> so that I can collect tech news for analysis.

**Acceptance Criteria:**
1. Le scraper récupère les posts de la front page HN (top 30)
2. Chaque post contient : titre, URL, score, nombre de commentaires, auteur
3. Les posts sont retournés dans un format TypeScript typé (`HNPost[]`)
4. Le scraper gère les "Ask HN" et "Show HN" comme des posts normaux
5. Les erreurs sont loggées proprement
6. Un test unitaire valide le parsing des données

## Story 1.4: Unified Post Format & Deduplication

> As a developer,
> I want a unified post format and deduplication logic,
> so that posts from different sources can be processed uniformly.

**Acceptance Criteria:**
1. Un type `UnifiedPost` est défini avec les champs communs (titre, url, source, score, etc.)
2. Les posts Reddit et HN sont convertis vers ce format unifié
3. La déduplication identifie les posts pointant vers la même URL
4. Les doublons sont fusionnés en conservant le meilleur score et les deux sources
5. Un test unitaire valide la logique de déduplication

---

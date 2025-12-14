# Tech Watch Tool - Product Requirements Document (PRD)

## Goals and Background Context

### Goals

- Permettre à Benjamin de produire ses "brèves informatiques" YouTube sans parcourir les réseaux sociaux
- Filtrer automatiquement les sujets tech qui résonnent avec sa communauté (dramas, success stories, IA accessible)
- Créer un outil de veille qui comprend le "Profil Benjamin" et ses critères éditoriaux
- Livrer 3-4 sujets pertinents par jour dans Notion, prêts à être transformés en contenu

### Background Context

Benjamin, créateur de contenu tech francophone, produit régulièrement des "brèves informatiques" pour YouTube. Le processus actuel nécessite de scroller Twitter, Reddit et Hacker News pour trouver les sujets qui captent son audience — un processus chronophage et source de distraction.

Le brainstorming a révélé un insight clé : les brèves ne sont pas qu'informationnelles, elles créent un sentiment d'**appartenance communautaire** pour une audience de devs/moldus parfois isolés. L'outil doit donc filtrer non pas les "news tech" mais les **histoires qui font réfléchir et rassemblent**.

### Change Log

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-12-10 | 0.1 | Création initiale du PRD | John (PM) |

## Requirements

### Functional

- **FR1:** Le système doit scraper quotidiennement les posts de Reddit (subreddits ciblés : r/programming, r/webdev, r/SaaS, r/startups) et la front page de Hacker News.
- **FR2:** Le système doit analyser chaque post via un LLM (Claude) en appliquant les critères pondérés du "Profil Benjamin" (dramas ⭐⭐⭐, success stories ⭐⭐⭐, avancées IA ⭐⭐⭐, etc.).
- **FR3:** Le système doit filtrer et exclure les sujets selon les anti-critères définis (trop technique, corporate sans angle humain, politique hors tech).
- **FR4:** Le système doit créer automatiquement 3-4 entrées par jour dans une base Notion avec : titre, description, liens sources, score de pertinence.
- **FR5:** Le système doit permettre à Benjamin de noter chaque sujet (1-5 étoiles) directement dans Notion pour alimenter le feedback loop.
- **FR6:** Le système doit gérer la déduplication des sujets apparaissant sur plusieurs sources (Reddit + HN).
- **FR7:** Le système doit exécuter le pipeline automatiquement 1x par jour via un cron job.

### Non Functional

- **NFR1:** Le système doit utiliser Bright Data comme solution de scraping (sponsor existant).
- **NFR2:** Le temps de traitement du pipeline complet ne doit pas dépasser 10 minutes par exécution.
- **NFR3:** Le coût API Claude doit rester raisonnable (<$10/mois pour l'usage prévu de ~100-200 posts/jour).
- **NFR4:** Le système doit être déployable sur une infrastructure légère (ex: cron sur VPS ou serverless).
- **NFR5:** Les credentials (API keys Notion, Claude, Bright Data) doivent être stockés de manière sécurisée via variables d'environnement.

## Technical Assumptions

### Repository Structure: Monorepo
Un seul repo pour tout le projet — outil simple, pas besoin de séparer.

### Service Architecture: Serverless / Script-based
- Pipeline exécuté via GitHub Actions (cron quotidien)
- Pas de serveur persistant nécessaire
- Architecture "run once daily, output to Notion"

### Tech Stack

| Composant | Choix | Rationale |
|-----------|-------|-----------|
| **Langage** | TypeScript/Node.js | Écosystème riche pour scraping et APIs |
| **Scraping** | Bright Data SDK | Sponsor existant |
| **LLM** | Claude API (Anthropic SDK) | Excellent pour l'analyse et le filtrage contextuel |
| **Output** | Notion API | Benjamin utilise déjà Notion |
| **Execution** | GitHub Actions (cron) | Gratuit, simple, pas d'infra à gérer |
| **Config** | Fichier YAML | Pour les subreddits, critères, paramètres |

### Testing Requirements: Unit + Integration minimal
- Tests unitaires pour la logique de filtrage/scoring
- Test d'intégration pour le pipeline complet (mock des APIs)
- Pas de tests e2e complexes — c'est un script, pas une app

### Additional Technical Assumptions
- Le prompt "Profil Benjamin" sera stocké dans un fichier séparé (facile à itérer)
- Les feedbacks Notion seront récupérés manuellement au début, automatisés en V2
- Gestion des erreurs avec retry et logging (console + optionnel: Discord webhook)
- Support fr/en pour les sources (le LLM gère naturellement les deux langues)

## Epic List

| # | Epic | Goal |
|---|------|------|
| **Epic 1** | Foundation & Scraping | Mettre en place le projet, configurer les scrapers Reddit + HN, et valider que les données arrivent |
| **Epic 2** | LLM Filtering & Scoring | Intégrer Claude pour analyser et scorer les posts selon le "Profil Benjamin" |
| **Epic 3** | Notion Output & Pipeline | Connecter Notion, automatiser le pipeline complet via GitHub Actions |

---

## Epic 1: Foundation & Scraping

**Goal:** Mettre en place la structure du projet TypeScript, configurer Bright Data, et implémenter les scrapers pour Reddit et Hacker News. À la fin de cet epic, on peut récupérer des posts bruts des deux sources.

### Story 1.1: Project Setup

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

### Story 1.2: Reddit Scraper

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

### Story 1.3: Hacker News Scraper

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

### Story 1.4: Unified Post Format & Deduplication

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

## Epic 2: LLM Filtering & Scoring

**Goal:** Intégrer Claude API pour analyser chaque post selon les critères du "Profil Benjamin", scorer leur pertinence, et filtrer les meilleurs candidats pour les brèves.

### Story 2.1: Claude API Integration

> As a developer,
> I want to integrate Claude API with the Anthropic SDK,
> so that I can send posts for analysis.

**Acceptance Criteria:**
1. Le package `@anthropic-ai/sdk` est installé et configuré
2. La clé API est lue depuis les variables d'environnement
3. Une fonction `analyzePost(post: UnifiedPost)` envoie un post à Claude et retourne la réponse
4. Le rate limiting et les erreurs API sont gérés avec retry (3 tentatives max)
5. Un test unitaire avec mock valide l'intégration

### Story 2.2: Benjamin Profile Prompt

> As a developer,
> I want a structured prompt file containing the "Profil Benjamin" criteria,
> so that Claude can filter posts according to editorial guidelines.

**Acceptance Criteria:**
1. Un fichier `prompts/benjamin-profile.md` contient le prompt système complet
2. Le prompt inclut les critères pondérés (dramas ⭐⭐⭐, success stories ⭐⭐⭐, etc.)
3. Le prompt inclut les anti-critères (trop technique, corporate sans angle humain, etc.)
4. Le prompt demande un score de 1-10 et une justification courte
5. Le prompt spécifie le format de sortie attendu (JSON structuré)
6. Le fichier prompt est chargé dynamiquement au runtime

### Story 2.3: Post Analysis & Scoring

> As a developer,
> I want each post analyzed and scored by Claude,
> so that I can identify the most relevant content.

**Acceptance Criteria:**
1. Chaque `UnifiedPost` est envoyé à Claude avec le prompt "Profil Benjamin"
2. Claude retourne un objet `ScoredPost` avec : score (1-10), justification, tags (drama/success/IA/etc.)
3. Les posts avec score < 5 sont marqués comme "rejetés" avec la raison
4. Le traitement est fait en batch avec un délai entre les appels (respect rate limits)
5. Les résultats sont loggés pour debugging
6. Un test valide le parsing de la réponse Claude

### Story 2.4: Top Posts Selection

> As a developer,
> I want to select the top 3-4 posts from the scored results,
> so that only the best content is sent to Notion.

**Acceptance Criteria:**
1. Les posts sont triés par score décroissant
2. Les 4 meilleurs posts sont sélectionnés (configurable via `config.yaml`)
3. En cas d'égalité, le post avec le plus d'engagement (score source + commentaires) gagne
4. Si moins de 4 posts passent le seuil (score >= 6), on prend ce qui est disponible
5. La fonction retourne un tableau `TopPost[]` prêt pour Notion

---

## Epic 3: Notion Output & Pipeline

**Goal:** Connecter le système à Notion pour créer automatiquement les entrées quotidiennes, puis automatiser l'ensemble du pipeline via GitHub Actions.

### Story 3.1: Notion API Integration

> As a developer,
> I want to connect to Notion API,
> so that I can create entries in Benjamin's database.

**Acceptance Criteria:**
1. Le package `@notionhq/client` est installé et configuré
2. La clé API Notion et l'ID de la database sont lus depuis `.env`
3. Une fonction `createNotionEntry(post: TopPost)` crée une page dans la database
4. Les erreurs API sont gérées avec retry
5. Un test avec mock valide l'intégration

### Story 3.2: Notion Entry Format

> As a developer,
> I want entries created with the correct fields and formatting,
> so that Benjamin can use them immediately.

**Acceptance Criteria:**
1. Chaque entrée contient : Titre, Description (justification Claude), URL source, Score, Tags, Date
2. Un champ "Rating" (select 1-5 étoiles) est créé vide pour le feedback manuel
3. Un champ "Sources" liste Reddit/HN avec liens
4. Le titre est formaté proprement (pas de caractères spéciaux cassés)
5. Les tags sont créés comme multi-select (Drama, Success Story, IA, etc.)

### Story 3.3: Main Pipeline Orchestration

> As a developer,
> I want a main entry point that orchestrates the full pipeline,
> so that everything runs in sequence.

**Acceptance Criteria:**
1. Un fichier `src/index.ts` orchestre : scrape → analyze → select → publish
2. Chaque étape est loggée avec timing (durée d'exécution)
3. Si une étape échoue, le pipeline s'arrête avec un message d'erreur clair
4. Un résumé final est loggé : "X posts scrapés, Y analysés, Z publiés sur Notion"
5. Le script peut être exécuté via `npm run pipeline`
6. Un mode `--dry-run` permet de tester sans publier sur Notion

### Story 3.4: GitHub Actions Automation

> As a developer,
> I want the pipeline to run automatically every day via GitHub Actions,
> so that Benjamin receives his daily digest without manual intervention.

**Acceptance Criteria:**
1. Un workflow `.github/workflows/daily-pipeline.yml` est créé
2. Le cron est configuré pour s'exécuter 1x par jour (ex: 6h du matin UTC)
3. Les secrets (API keys) sont configurés via GitHub Secrets
4. Le workflow installe les dépendances, build, et exécute le pipeline
5. En cas d'échec, une notification est envoyée (GitHub email par défaut)
6. Les logs d'exécution sont accessibles dans l'onglet Actions
7. Un `workflow_dispatch` permet de déclencher manuellement le pipeline

---

## Out of Scope (V2+)

Les éléments suivants sont explicitement **hors scope** pour le MVP :

- **Twitter/X scraping** : Complexité d'authentification, prévu pour V2
- **Feedback loop automatisé** : Récupération auto des notes Notion pour enrichir le prompt
- **Auto-génération de scripts vidéo** : Le LLM ne rédige pas les brèves, il filtre seulement
- **Interface web** : Pas de dashboard, Notion suffit
- **Multi-utilisateurs** : Outil personnel pour Benjamin uniquement
- **Historique et analytics** : Pas de tracking des performances sur le temps
- **Notifications push** : Pas de Discord/Slack webhook (sauf erreurs)

---

## Checklist Results Report

### Executive Summary

| Métrique | Valeur |
|----------|--------|
| **Complétude PRD** | ~85% |
| **Scope MVP** | Just Right |
| **Prêt pour Architecture** | Ready |

### Category Analysis

| Category | Status |
|----------|--------|
| Problem Definition & Context | PASS |
| MVP Scope Definition | PASS |
| User Experience Requirements | N/A (pas d'UI) |
| Functional Requirements | PASS |
| Non-Functional Requirements | PARTIAL |
| Epic & Story Structure | PASS |
| Technical Guidance | PASS |
| Cross-Functional Requirements | PARTIAL |
| Clarity & Communication | PASS |

### Verdict

**READY FOR ARCHITECT** — Le PRD est complet et bien structuré pour passer à la phase d'architecture.

---

## Next Steps

### Architect Prompt

> Le PRD pour l'outil de veille tech "Tech Watch Tool" est prêt dans `docs/prd.md`.
>
> C'est un pipeline TypeScript qui scrape Reddit + HN via Bright Data, filtre avec Claude API selon le "Profil Benjamin", et publie 3-4 sujets/jour dans Notion. Exécution via GitHub Actions (cron quotidien).
>
> Passe en mode architecture (`*create-architecture` ou équivalent) pour concevoir l'architecture technique basée sur ce PRD.


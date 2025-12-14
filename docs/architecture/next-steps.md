# Next Steps

## Pour le développement

1. **Project Setup (Epic 1, Story 1.1)**
   - Initialiser le repo avec la structure définie
   - Configurer TypeScript, ESLint, Prettier, Vitest
   - Créer le fichier `config.yaml` avec les paramètres par défaut

2. **Implémenter les stories dans l'ordre des Epics du PRD**
   - Epic 1: Foundation & Scraping
   - Epic 2: LLM Filtering & Scoring
   - Epic 3: Notion Output & Pipeline

3. **Configuration Notion**
   - Créer la database Notion avec le schéma `NotionEntry`
   - Créer une intégration Notion et récupérer l'API key
   - Partager la database avec l'intégration

## Dev Agent Prompt

> L'architecture pour le Tech Watch Tool est prête dans `docs/architecture.md`.
>
> C'est un pipeline TypeScript qui :
> - Scrape Reddit via Bright Data Dataset API (JSON direct)
> - Scrape Hacker News via l'API officielle
> - Filtre avec Claude API selon le "Profil Benjamin"
> - Publie 3-4 sujets/jour dans Notion
> - S'exécute via GitHub Actions (cron quotidien)
>
> Commence par l'Epic 1, Story 1.1 (Project Setup) du PRD (`docs/prd.md`).
> Respecte les coding standards et la structure définie dans l'architecture.


# Epic 3: Notion Output & Pipeline

**Goal:** Connecter le système à Notion pour créer automatiquement les entrées quotidiennes, puis automatiser l'ensemble du pipeline via GitHub Actions.

## Story 3.1: Notion API Integration

> As a developer,
> I want to connect to Notion API,
> so that I can create entries in Benjamin's database.

**Acceptance Criteria:**
1. Le package `@notionhq/client` est installé et configuré
2. La clé API Notion et l'ID de la database sont lus depuis `.env`
3. Une fonction `createNotionEntry(post: TopPost)` crée une page dans la database
4. Les erreurs API sont gérées avec retry
5. Un test avec mock valide l'intégration

## Story 3.2: Notion Entry Format

> As a developer,
> I want entries created with the correct fields and formatting,
> so that Benjamin can use them immediately.

**Acceptance Criteria:**
1. Chaque entrée contient : Titre, Description (justification Claude), URL source, Score, Tags, Date
2. Un champ "Rating" (select 1-5 étoiles) est créé vide pour le feedback manuel
3. Un champ "Sources" liste Reddit/HN avec liens
4. Le titre est formaté proprement (pas de caractères spéciaux cassés)
5. Les tags sont créés comme multi-select (Drama, Success Story, IA, etc.)

## Story 3.3: Main Pipeline Orchestration

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

## Story 3.4: GitHub Actions Automation

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

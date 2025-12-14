# Requirements

## Functional

- **FR1:** Le système doit scraper quotidiennement les posts de Reddit (subreddits ciblés : r/programming, r/webdev, r/SaaS, r/startups) et la front page de Hacker News.
- **FR2:** Le système doit analyser chaque post via un LLM (Claude) en appliquant les critères pondérés du "Profil Benjamin" (dramas ⭐⭐⭐, success stories ⭐⭐⭐, avancées IA ⭐⭐⭐, etc.).
- **FR3:** Le système doit filtrer et exclure les sujets selon les anti-critères définis (trop technique, corporate sans angle humain, politique hors tech).
- **FR4:** Le système doit créer automatiquement 3-4 entrées par jour dans une base Notion avec : titre, description, liens sources, score de pertinence.
- **FR5:** Le système doit permettre à Benjamin de noter chaque sujet (1-5 étoiles) directement dans Notion pour alimenter le feedback loop.
- **FR6:** Le système doit gérer la déduplication des sujets apparaissant sur plusieurs sources (Reddit + HN).
- **FR7:** Le système doit exécuter le pipeline automatiquement 1x par jour via un cron job.

## Non Functional

- **NFR1:** Le système doit utiliser Bright Data comme solution de scraping (sponsor existant).
- **NFR2:** Le temps de traitement du pipeline complet ne doit pas dépasser 10 minutes par exécution.
- **NFR3:** Le coût API Claude doit rester raisonnable (<$10/mois pour l'usage prévu de ~100-200 posts/jour).
- **NFR4:** Le système doit être déployable sur une infrastructure légère (ex: cron sur VPS ou serverless).
- **NFR5:** Les credentials (API keys Notion, Claude, Bright Data) doivent être stockés de manière sécurisée via variables d'environnement.

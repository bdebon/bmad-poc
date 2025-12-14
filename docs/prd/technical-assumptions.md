# Technical Assumptions

## Repository Structure: Monorepo
Un seul repo pour tout le projet — outil simple, pas besoin de séparer.

## Service Architecture: Serverless / Script-based
- Pipeline exécuté via GitHub Actions (cron quotidien)
- Pas de serveur persistant nécessaire
- Architecture "run once daily, output to Notion"

## Tech Stack

| Composant | Choix | Rationale |
|-----------|-------|-----------|
| **Langage** | TypeScript/Node.js | Écosystème riche pour scraping et APIs |
| **Scraping** | Bright Data SDK | Sponsor existant |
| **LLM** | Claude API (Anthropic SDK) | Excellent pour l'analyse et le filtrage contextuel |
| **Output** | Notion API | Benjamin utilise déjà Notion |
| **Execution** | GitHub Actions (cron) | Gratuit, simple, pas d'infra à gérer |
| **Config** | Fichier YAML | Pour les subreddits, critères, paramètres |

## Testing Requirements: Unit + Integration minimal
- Tests unitaires pour la logique de filtrage/scoring
- Test d'intégration pour le pipeline complet (mock des APIs)
- Pas de tests e2e complexes — c'est un script, pas une app

## Additional Technical Assumptions
- Le prompt "Profil Benjamin" sera stocké dans un fichier séparé (facile à itérer)
- Les feedbacks Notion seront récupérés manuellement au début, automatisés en V2
- Gestion des erreurs avec retry et logging (console + optionnel: Discord webhook)
- Support fr/en pour les sources (le LLM gère naturellement les deux langues)

# Tech Stack

## Cloud Infrastructure

- **Provider:** GitHub (Actions pour le compute, Secrets pour les credentials)
- **Key Services:** GitHub Actions runners (Ubuntu latest)
- **Deployment Regions:** N/A (serverless, GitHub-managed)

*Note: Pas de cloud provider traditionnel (AWS/GCP/Azure) — le projet utilise uniquement GitHub Actions comme "compute" gratuit.*

## Technology Stack Table

| Category | Technology | Version | Purpose | Rationale |
|----------|------------|---------|---------|-----------|
| **Language** | TypeScript | 5.3 | Langage principal | Typage fort, excellent écosystème Node.js, maintenabilité |
| **Runtime** | Node.js | 20 LTS | Environnement d'exécution | Version LTS stable, support long terme, compatible GH Actions |
| **Scraping** | Bright Data SDK | latest | Scraping Reddit + HN | Sponsor existant (NFR1), gère proxies et anti-bot |
| **LLM** | @anthropic-ai/sdk | ^0.32 | Intégration Claude API | SDK officiel, TypeScript natif, gestion streaming |
| **Output** | @notionhq/client | ^2.2 | Publication Notion | SDK officiel Notion, bien maintenu |
| **Config** | yaml | ^2.3 | Parsing config.yaml | Standard, léger, pas de dépendances |
| **HTTP** | Built-in fetch | native | Requêtes HTTP si besoin | Node 20 inclut fetch natif, pas de dep externe |
| **Validation** | zod | ^3.22 | Validation des données | Type-safe, excellent avec TypeScript |
| **Linting** | ESLint | ^9.0 | Qualité du code | Flat config moderne, standard industrie |
| **Formatting** | Prettier | ^3.2 | Formatage code | Standard industrie, intégration ESLint |
| **Testing** | Vitest | ^2.0 | Tests unitaires/intégration | Rapide, compatible Jest API, TypeScript natif |
| **Build** | tsx | ^4.7 | Exécution TypeScript | Pas de build step, exécution directe TS |

---

# Infrastructure and Deployment

## Infrastructure as Code

- **Tool:** N/A (pas d'IaC traditionnel)
- **Approach:** GitHub Actions = infrastructure as YAML

Le projet n'utilise pas de cloud provider traditionnel. GitHub Actions EST l'infrastructure :
- Compute : GitHub-hosted runners (Ubuntu)
- Secrets : GitHub Secrets
- Scheduling : Cron intégré aux workflows
- Logs : GitHub Actions logs (rétention 90 jours)

## Deployment Strategy

- **Strategy:** "Deploy on merge" — le code EST le déploiement
- **CI/CD Platform:** GitHub Actions
- **Pipeline Configuration:** `.github/workflows/daily-pipeline.yml`

Pas de déploiement traditionnel — le pipeline s'exécute directement depuis le repo.

## GitHub Actions Workflow

```yaml
# .github/workflows/daily-pipeline.yml
name: Daily Tech Watch Pipeline

on:
  schedule:
    - cron: '0 6 * * *'  # 6h UTC = 7h Paris (hiver) / 8h (été)
  workflow_dispatch:      # Manual trigger

jobs:
  run-pipeline:
    runs-on: ubuntu-latest
    timeout-minutes: 10   # NFR2 compliance

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - run: npm ci

      - run: npm run pipeline
        env:
          BRIGHT_DATA_API_KEY: ${{ secrets.BRIGHT_DATA_API_KEY }}
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
          NOTION_API_KEY: ${{ secrets.NOTION_API_KEY }}
          NOTION_DATABASE_ID: ${{ secrets.NOTION_DATABASE_ID }}
```

## Environments

| Environment | Purpose | Details |
|-------------|---------|---------|
| **Local** | Développement | `npm run dev` avec `.env` local, mode `--dry-run` |
| **Production** | Exécution quotidienne | GitHub Actions avec secrets, publie sur Notion |

## Environment Promotion Flow

```
Local Development → git push → main branch → GitHub Actions (cron) → Notion Output
```

## Rollback Strategy

- **Primary Method:** Git revert + re-run workflow
- **Trigger Conditions:** Pipeline failure, mauvaise qualité des résultats
- **Recovery Time Objective:** < 5 minutes

Procédure : `git revert` → push → trigger manuel workflow

---

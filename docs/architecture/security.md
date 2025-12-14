# Security

## Input Validation

- **Validation Library:** zod
- **Validation Location:** À l'entrée de chaque module (boundary)
- **Required Rules:** Toutes les réponses API externes validées avec zod schemas

```typescript
const RedditPostSchema = z.object({
  title: z.string().min(1),
  url: z.string().url(),
  score: z.number().int().min(0),
  num_comments: z.number().int().min(0),
  author: z.string(),
  created_utc: z.number(),
  subreddit: z.string(),
});
```

## Authentication & Authorization

- **Auth Method:** API Keys (pas d'OAuth, pas de JWT)
- **Session Management:** N/A (script stateless)
- Chaque SDK reçoit sa clé via constructeur, jamais dans les URLs ou logs

## Secrets Management

- **Development:** Fichier `.env` local (gitignored)
- **Production:** GitHub Secrets

| Secret | Source | Usage |
|--------|--------|-------|
| `BRIGHT_DATA_API_KEY` | Bright Data dashboard | Reddit scraping |
| `ANTHROPIC_API_KEY` | Anthropic console | Claude API |
| `NOTION_API_KEY` | Notion integrations | Notion API |
| `NOTION_DATABASE_ID` | Notion URL | Target database |

**Validation au démarrage :** Fail fast si un secret est manquant.

## Data Protection

- **Encryption in Transit:** HTTPS pour toutes les APIs
- **PII Handling:** Aucune donnée personnelle collectée (posts publics uniquement)
- **Logging Restrictions:**
  - JAMAIS logger les API keys
  - JAMAIS logger les réponses complètes Claude
  - OK de logger titres et scores des posts

## Dependency Security

- **Scanning Tool:** `npm audit` (CI)
- **Update Policy:** Mensuel ou sur alerte critique

---

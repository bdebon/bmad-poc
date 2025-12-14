# Error Handling Strategy

## General Approach

- **Error Model:** Fail-fast avec retry intelligent
- **Exception Hierarchy:** Erreurs typées par domaine (ScraperError, AnalyzerError, PublisherError)
- **Error Propagation:** Les erreurs remontent au pipeline orchestrator qui décide de retry ou fail

```typescript
class PipelineError extends Error {
  constructor(message: string, public readonly cause?: Error) {
    super(message);
  }
}

class ScraperError extends PipelineError {}
class AnalyzerError extends PipelineError {}
class PublisherError extends PipelineError {}
class ConfigError extends PipelineError {}
```

## Logging Standards

- **Library:** Console (natif Node.js)
- **Format:** JSON structuré
- **Levels:** `info`, `warn`, `error`

```typescript
{
  "level": "info",
  "timestamp": "2025-12-10T06:00:15.123Z",
  "stage": "scraper",
  "message": "Reddit scraping complete",
  "data": { "postCount": 47, "duration": 8234 }
}
```

## Error Handling Patterns

### External API Errors

| API | Retry Policy | Timeout |
|-----|--------------|---------|
| Bright Data | 3 retries, exponential backoff (1s, 2s, 4s) | 30s |
| HN API | 3 retries, 500ms backoff | 10s |
| Claude API | 3 retries, exponential backoff | 60s |
| Notion API | 3 retries, 1s backoff | 15s |

### Business Logic Errors

- **Aucun post trouvé:** Warning, pipeline continue (0 entrées Notion)
- **Tous les posts rejetés:** Warning, pipeline continue
- **Moins de 4 posts passent le seuil:** Info, publie ce qui est disponible

### Data Consistency

- **Idempotency:** Pas de déduplication Notion (acceptable pour 1 run/jour)
- **Partial Failure:** Si Notion échoue après 2/4 posts, les premiers restent. Pipeline exit 1.

---

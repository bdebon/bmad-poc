# External APIs

## Bright Data Dataset API (Reddit uniquement)

- **Purpose:** Récupérer les top posts Reddit en JSON structuré
- **Documentation:** https://docs.brightdata.com/scraping-automation/web-data-apis/web-scraper/datasets/reddit
- **Base URL(s):** `https://api.brightdata.com/datasets/v3/`
- **Authentication:** API Key via `BRIGHT_DATA_API_KEY` env var
- **Rate Limits:** Selon le plan (flexible)

**Key Endpoints Used:**
- `POST /trigger` — Déclencher la collecte pour les subreddits configurés
- `GET /snapshot/{id}` — Récupérer les résultats JSON

**Response Format:**
```json
{
  "title": "Post title",
  "url": "https://...",
  "score": 1234,
  "num_comments": 89,
  "author": "username",
  "created_utc": 1702234567,
  "subreddit": "programming"
}
```

**Integration Notes:**
- Pas de parsing HTML — données structurées directement
- Configurer les subreddits cibles dans la requête
- Réponse asynchrone possible (trigger → poll snapshot)

---

## Hacker News API (Officielle)

- **Purpose:** Récupérer les top stories de la front page HN
- **Documentation:** https://github.com/HackerNews/API
- **Base URL(s):** `https://hacker-news.firebaseio.com/v0/`
- **Authentication:** Aucune (API publique)
- **Rate Limits:** Pas de limite documentée (usage raisonnable)

**Key Endpoints Used:**
- `GET /topstories.json` — IDs des 500 top stories
- `GET /item/{id}.json` — Détails d'un item (post)

**Response Format:**
```json
{
  "id": 12345,
  "title": "Show HN: Something cool",
  "url": "https://...",
  "score": 234,
  "descendants": 45,
  "by": "username",
  "time": 1702234567,
  "type": "story"
}
```

**Integration Notes:**
- Gratuit, fiable, maintenu par YC
- Nécessite 2 appels : liste des IDs → détails de chaque post
- Limiter à top 30 posts pour respecter le PRD
- Pas de dépendance Bright Data = moins de coûts

---

## Claude API (Anthropic)

- **Purpose:** Analyse et scoring des posts selon le "Profil Benjamin"
- **Documentation:** https://docs.anthropic.com/
- **Base URL(s):** `https://api.anthropic.com/v1`
- **Authentication:** API Key via `ANTHROPIC_API_KEY` env var
- **Rate Limits:** Tier 1: 60 RPM, 60K tokens/min

**Key Endpoints Used:**
- `POST /messages` — Analyse du post, réponse JSON structurée

**Integration Notes:**
- Utiliser `claude-3-haiku-20240307` pour minimiser les coûts
- Retry avec backoff exponentiel pour les 429

---

## Notion API

- **Purpose:** Création des entrées quotidiennes dans la database de Benjamin
- **Documentation:** https://developers.notion.com/
- **Base URL(s):** `https://api.notion.com/v1`
- **Authentication:** Internal Integration Token via `NOTION_API_KEY` env var
- **Rate Limits:** 3 requests/second (average)

**Key Endpoints Used:**
- `POST /pages` — Créer une nouvelle page dans la database

**Integration Notes:**
- L'intégration doit être ajoutée à la database Notion manuellement
- Le schéma de la database doit correspondre aux champs `NotionEntry`

---

# Data Models

## UnifiedPost

**Purpose:** Format standardisé pour tous les posts, quelle que soit la source (Reddit ou HN).

| Attribute | Type | Description |
|-----------|------|-------------|
| `id` | string | Identifiant unique (format: `{source}_{originalId}`) |
| `title` | string | Titre du post |
| `url` | string | URL vers le contenu (article externe ou discussion) |
| `sourceUrl` | string | URL vers la discussion source (Reddit thread / HN comments) |
| `source` | `'reddit' \| 'hn'` | Provenance du post |
| `subreddit` | string \| null | Subreddit d'origine (null pour HN) |
| `score` | number | Score/upvotes sur la plateforme source |
| `commentCount` | number | Nombre de commentaires |
| `author` | string | Auteur du post |
| `createdAt` | Date | Date de création du post |
| `scrapedAt` | Date | Date de scraping |

**Relationships:**
- Transformé depuis `RedditPost` ou `HNPost`
- Input pour `ScoredPost`

## ScoredPost

**Purpose:** Post enrichi avec l'analyse Claude et le scoring selon le "Profil Benjamin".

| Attribute | Type | Description |
|-----------|------|-------------|
| `post` | UnifiedPost | Le post original |
| `relevanceScore` | number (1-10) | Score de pertinence pour les brèves |
| `justification` | string | Explication courte de Claude (2-3 phrases) |
| `tags` | string[] | Tags de catégorisation (`drama`, `success-story`, `ia`, etc.) |
| `rejected` | boolean | True si score < seuil |
| `rejectionReason` | string \| null | Raison du rejet (anti-critère déclenché) |
| `analyzedAt` | Date | Timestamp de l'analyse |

**Relationships:**
- Étend `UnifiedPost`
- Input pour sélection des top posts
- Les top posts deviennent `NotionEntry`

## NotionEntry

**Purpose:** Structure de données pour créer une page dans la database Notion.

| Attribute | Type | Description |
|-----------|------|-------------|
| `title` | string | Titre du sujet (peut être reformulé par Claude) |
| `description` | string | Résumé/justification pour Benjamin |
| `sourceUrls` | string[] | Liens vers Reddit/HN/article original |
| `sources` | string[] | Liste des plateformes (`Reddit`, `HN`) |
| `relevanceScore` | number | Score 1-10 |
| `tags` | string[] | Multi-select Notion |
| `rating` | null | Champ vide pour feedback manuel (1-5 étoiles) |
| `date` | Date | Date de publication dans Notion |

**Relationships:**
- Créé depuis `ScoredPost`
- Correspond au schéma de la database Notion cible

## Config (config.yaml)

**Purpose:** Configuration externalisée du pipeline.

| Attribute | Type | Description |
|-----------|------|-------------|
| `sources.reddit.subreddits` | string[] | Liste des subreddits à scraper |
| `sources.reddit.limit` | number | Nombre de posts par subreddit |
| `sources.hn.limit` | number | Nombre de posts HN (front page) |
| `filtering.minScore` | number | Score minimum pour passer (défaut: 6) |
| `filtering.maxPosts` | number | Nombre max de posts à publier (défaut: 4) |
| `notion.databaseId` | string | ID de la database Notion cible |

---

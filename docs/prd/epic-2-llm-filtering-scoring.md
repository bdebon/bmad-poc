# Epic 2: LLM Filtering & Scoring

**Goal:** Intégrer Claude API pour analyser chaque post selon les critères du "Profil Benjamin", scorer leur pertinence, et filtrer les meilleurs candidats pour les brèves.

## Story 2.1: Claude API Integration

> As a developer,
> I want to integrate Claude API with the Anthropic SDK,
> so that I can send posts for analysis.

**Acceptance Criteria:**
1. Le package `@anthropic-ai/sdk` est installé et configuré
2. La clé API est lue depuis les variables d'environnement
3. Une fonction `analyzePost(post: UnifiedPost)` envoie un post à Claude et retourne la réponse
4. Le rate limiting et les erreurs API sont gérés avec retry (3 tentatives max)
5. Un test unitaire avec mock valide l'intégration

## Story 2.2: Benjamin Profile Prompt

> As a developer,
> I want a structured prompt file containing the "Profil Benjamin" criteria,
> so that Claude can filter posts according to editorial guidelines.

**Acceptance Criteria:**
1. Un fichier `prompts/benjamin-profile.md` contient le prompt système complet
2. Le prompt inclut les critères pondérés (dramas ⭐⭐⭐, success stories ⭐⭐⭐, etc.)
3. Le prompt inclut les anti-critères (trop technique, corporate sans angle humain, etc.)
4. Le prompt demande un score de 1-10 et une justification courte
5. Le prompt spécifie le format de sortie attendu (JSON structuré)
6. Le fichier prompt est chargé dynamiquement au runtime

## Story 2.3: Post Analysis & Scoring

> As a developer,
> I want each post analyzed and scored by Claude,
> so that I can identify the most relevant content.

**Acceptance Criteria:**
1. Chaque `UnifiedPost` est envoyé à Claude avec le prompt "Profil Benjamin"
2. Claude retourne un objet `ScoredPost` avec : score (1-10), justification, tags (drama/success/IA/etc.)
3. Les posts avec score < 5 sont marqués comme "rejetés" avec la raison
4. Le traitement est fait en batch avec un délai entre les appels (respect rate limits)
5. Les résultats sont loggés pour debugging
6. Un test valide le parsing de la réponse Claude

## Story 2.4: Top Posts Selection

> As a developer,
> I want to select the top 3-4 posts from the scored results,
> so that only the best content is sent to Notion.

**Acceptance Criteria:**
1. Les posts sont triés par score décroissant
2. Les 4 meilleurs posts sont sélectionnés (configurable via `config.yaml`)
3. En cas d'égalité, le post avec le plus d'engagement (score source + commentaires) gagne
4. Si moins de 4 posts passent le seuil (score >= 6), on prend ce qui est disponible
5. La fonction retourne un tableau `TopPost[]` prêt pour Notion

---

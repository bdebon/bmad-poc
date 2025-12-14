# Coding Standards

## Core Standards

- **Language & Runtime:** TypeScript 5.3, Node.js 20 LTS
- **Style & Linting:** ESLint 9 (flat config) + Prettier
- **Test Organization:** `tests/unit/*.test.ts`, `tests/integration/*.test.ts`

## Naming Conventions

| Element | Convention | Example |
|---------|------------|---------|
| Files | kebab-case | `hacker-news.ts`, `prompt-loader.ts` |
| Functions | camelCase | `scrapeReddit()`, `analyzePost()` |
| Types/Interfaces | PascalCase | `UnifiedPost`, `ScoredPost` |
| Constants | SCREAMING_SNAKE_CASE | `MAX_RETRIES`, `DEFAULT_TIMEOUT` |
| Env vars | SCREAMING_SNAKE_CASE | `ANTHROPIC_API_KEY` |

## Critical Rules

1. **Toujours valider les données externes avec zod** — Toute réponse API doit être validée

2. **Utiliser le logger structuré, pas console.log direct**
   ```typescript
   // ✅ Requis
   logger.info('scraper', 'Scraping complete', { postCount: 47 });
   ```

3. **Toujours typer explicitement les retours de fonction async**
   ```typescript
   // ✅ Requis
   async function scrape(): Promise<RedditPost[]> { ... }
   ```

4. **Gérer les erreurs avec les classes d'erreur définies**
   ```typescript
   // ✅ Requis
   throw new ScraperError('Bright Data API failed', { cause: err });
   ```

5. **Pas de `any`** — utiliser `unknown` si le type est vraiment inconnu

6. **Toute constante magique doit être dans config.yaml ou en constante nommée**

7. **Les secrets viennent UNIQUEMENT de process.env, jamais hardcodés**

---

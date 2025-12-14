# Source Tree

```
tech-watch-tool/
├── .github/
│   └── workflows/
│       └── daily-pipeline.yml    # GitHub Actions cron workflow
│
├── src/
│   ├── index.ts                  # Pipeline orchestrator (entry point)
│   │
│   ├── config/
│   │   ├── index.ts              # Config loader + env validation
│   │   └── schema.ts             # Zod schemas for config validation
│   │
│   ├── scrapers/
│   │   ├── index.ts              # Barrel export
│   │   ├── reddit.ts             # Bright Data Reddit fetcher
│   │   └── hackernews.ts         # HN API fetcher
│   │
│   ├── unifier/
│   │   ├── index.ts              # Unify + deduplicate logic
│   │   └── deduplicator.ts       # URL-based deduplication
│   │
│   ├── analyzer/
│   │   ├── index.ts              # Claude API integration
│   │   └── prompt-loader.ts      # Load benjamin-profile.md
│   │
│   ├── selector/
│   │   └── index.ts              # Top posts selection logic
│   │
│   ├── publisher/
│   │   └── index.ts              # Notion API integration
│   │
│   └── types/
│       ├── index.ts              # Barrel export
│       ├── posts.ts              # RedditPost, HNPost, UnifiedPost, ScoredPost
│       ├── config.ts             # Config types
│       └── notion.ts             # NotionEntry types
│
├── prompts/
│   └── benjamin-profile.md       # LLM prompt with scoring criteria
│
├── tests/
│   ├── unit/
│   │   ├── unifier.test.ts
│   │   ├── selector.test.ts
│   │   └── deduplicator.test.ts
│   └── integration/
│       ├── reddit.test.ts        # Mock Bright Data responses
│       ├── hackernews.test.ts    # Mock HN API responses
│       └── analyzer.test.ts      # Mock Claude responses
│
├── config.yaml                   # Runtime configuration
├── .env.example                  # Environment variables template
├── .gitignore
├── package.json
├── tsconfig.json
├── eslint.config.js              # ESLint flat config
├── prettier.config.js
├── vitest.config.ts
└── README.md
```

**Conventions:**
- Un fichier `index.ts` par module = barrel export
- Tests miroir la structure src
- Pas de `dist/` — tsx exécute directement TypeScript

---

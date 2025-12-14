# Core Workflows

## Main Pipeline Workflow

```mermaid
sequenceDiagram
    participant GHA as GitHub Actions
    participant IDX as index.ts
    participant CFG as Config
    participant BD as Bright Data API
    participant HN as HN API
    participant UNI as Unifier
    participant CL as Claude API
    participant SEL as Selector
    participant NO as Notion API

    GHA->>IDX: Trigger (cron 6h UTC)
    IDX->>CFG: loadConfig()
    CFG-->>IDX: config + env validated

    par Scrape Sources
        IDX->>BD: POST /trigger (subreddits)
        BD-->>IDX: RedditPost[] (JSON)
        IDX->>HN: GET /topstories + /item/{id}
        HN-->>IDX: HNPost[] (JSON)
    end

    IDX->>UNI: unifyPosts(reddit, hn)
    UNI->>UNI: deduplicateByUrl()
    UNI-->>IDX: UnifiedPost[]

    loop For each post (with delay)
        IDX->>CL: POST /messages (post + profile)
        CL-->>IDX: { score, justification, tags }
    end
    Note over IDX: ScoredPost[] created

    IDX->>SEL: selectTopPosts(scored, maxCount=4)
    SEL-->>IDX: TopPost[] (max 4)

    alt dry-run mode
        IDX->>IDX: Log results, skip Notion
    else normal mode
        loop For each top post
            IDX->>NO: POST /pages
            NO-->>IDX: Page created
        end
    end

    IDX->>GHA: Exit 0 + summary log
```

## Error Handling Flow

```mermaid
sequenceDiagram
    participant IDX as Pipeline
    participant API as External API
    participant LOG as Logger

    IDX->>API: Request

    alt Success (2xx)
        API-->>IDX: Response
        IDX->>IDX: Continue pipeline
    else Rate Limited (429)
        API-->>IDX: 429 Too Many Requests
        IDX->>LOG: Warn: Rate limited
        IDX->>IDX: Wait (exponential backoff)
        IDX->>API: Retry (max 3)
    else Server Error (5xx)
        API-->>IDX: 500/502/503
        IDX->>LOG: Error: Server error
        IDX->>IDX: Retry with backoff (max 3)
    else Client Error (4xx)
        API-->>IDX: 400/401/403
        IDX->>LOG: Error: Client error (no retry)
        IDX->>IDX: Fail pipeline
    else Timeout
        API-->>IDX: Timeout
        IDX->>LOG: Warn: Timeout
        IDX->>IDX: Retry (max 2)
    end
```

## Daily Execution Timeline

```
06:00 UTC - GitHub Actions cron trigger
06:00:01  - Load config, validate env
06:00:02  - Start scraping (parallel)
06:00:10  - Reddit data received (~50 posts)
06:00:15  - HN data received (~30 posts)
06:00:16  - Unify + deduplicate (~70 unique posts)
06:00:17  - Start Claude analysis
06:03:00  - Analysis complete (~70 posts × 2s each)
06:03:01  - Select top 4 posts
06:03:02  - Publish to Notion (4 pages)
06:03:10  - Pipeline complete, exit 0

Total: ~3-4 minutes (well under NFR2's 10 min limit)
```

---

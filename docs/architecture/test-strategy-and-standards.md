# Test Strategy and Standards

## Testing Philosophy

- **Approach:** Test-after (pragmatique pour un projet simple)
- **Coverage Goals:** 80% pour la logique métier (unifier, selector, deduplicator)
- **Test Pyramid:** Unit 70% / Integration 30% / E2E 0%

## Unit Tests

- **Framework:** Vitest 2.0
- **File Convention:** `*.test.ts`
- **Location:** `tests/unit/`
- **Mocking Library:** Vitest built-in (`vi.mock`, `vi.fn`)
- **Coverage Requirement:** 80% branches pour les modules critiques

**Modules prioritaires:** `unifier/`, `selector/`, `deduplicator`, `config/schema`

**Pattern requis:** AAA (Arrange, Act, Assert)

```typescript
describe('selectTopPosts', () => {
  it('should return max 4 posts sorted by score', () => {
    // Arrange
    const posts: ScoredPost[] = [...];
    // Act
    const result = selectTopPosts(posts, 4);
    // Assert
    expect(result[0].relevanceScore).toBe(9);
  });
});
```

## Integration Tests

- **Scope:** Modules avec APIs mockées
- **Location:** `tests/integration/`

| Dependency | Test Approach |
|------------|---------------|
| Bright Data API | Mock fetch avec fixtures JSON |
| HN API | Mock fetch avec fixtures JSON |
| Claude API | Mock @anthropic-ai/sdk |
| Notion API | Mock @notionhq/client |

## Test Data Management

- **Strategy:** Fixtures JSON statiques
- **Location:** `tests/fixtures/`

## Continuous Testing

```yaml
# .github/workflows/ci.yml
- run: npm run lint
- run: npm run test
- run: npm audit --audit-level=high
```

---

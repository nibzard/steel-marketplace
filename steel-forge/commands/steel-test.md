---
description: Help write and run tests for Steel automation projects
---

# Steel Testing Assistant

Help the user write effective tests for Steel automation using standard testing frameworks.

## Test Approaches

### Unit Tests
Test individual functions and utilities:
```typescript
// tests/utils.test.ts
import { generateSelector } from '../src/utils';

describe('generateSelector', () => {
  it('should create optimal selector', () => {
    const element = { id: 'submit', class: 'btn' };
    expect(generateSelector(element)).toBe('#submit');
  });
});
```

### Integration Tests
Test Steel session management:
```typescript
// tests/session.test.ts
import { Steel } from 'steel-sdk';

describe('Steel Session', () => {
  let client: Steel;
  
  beforeAll(() => {
    client = new Steel({ steelAPIKey: process.env.STEEL_API_KEY! });
  });

  it('should create session', async () => {
    const session = await client.sessions.create();
    expect(session.id).toBeDefined();
    await client.sessions.release(session.id);
  });

  it('should connect with Playwright', async () => {
    const session = await client.sessions.create();
    const browser = await chromium.connectOverCDP(
      `${session.websocketUrl}?apiKey=${process.env.STEEL_API_KEY}`
    );
    
    expect(browser.contexts().length).toBeGreaterThan(0);
    
    await browser.close();
    await client.sessions.release(session.id);
  });
});
```

### E2E Tests
Test complete automation workflows:
```typescript
// tests/e2e/scraping.test.ts
describe('Product Scraping', () => {
  it('should scrape product data', async () => {
    const client = new Steel({ steelAPIKey: process.env.STEEL_API_KEY! });
    const session = await client.sessions.create();
    
    const browser = await chromium.connectOverCDP(
      `${session.websocketUrl}?apiKey=${process.env.STEEL_API_KEY}`
    );
    const page = browser.contexts()[0].pages()[0];
    
    await page.goto('https://example.com/products');
    const title = await page.locator('h1').first().textContent();
    
    expect(title).toBeTruthy();
    
    await browser.close();
    await client.sessions.release(session.id);
  });
});
```

## Test Best Practices

1. **Always Clean Up Sessions**:
   ```typescript
   afterEach(async () => {
     if (session) {
       await client.sessions.release(session.id);
     }
   });
   ```

2. **Use Longer Timeouts**: Steel automation can take time
   ```typescript
   jest.setTimeout(30000); // 30 seconds
   ```

3. **Handle Errors Gracefully**:
   ```typescript
   try {
     await page.goto('https://example.com');
   } catch (error) {
     console.log('Session viewer:', session.sessionViewerUrl);
     throw error;
   }
   ```

4. **Test with Live Sessions**: Use `sessionViewerUrl` to debug test failures

5. **Mock External Services**: Don't test third-party APIs, focus on your Steel code

## Common Test Patterns

### Session Creation Test
```typescript
it('should create session within timeout', async () => {
  const start = Date.now();
  const session = await client.sessions.create();
  const duration = Date.now() - start;
  
  expect(duration).toBeLessThan(5000); // Should be fast
  await client.sessions.release(session.id);
});
```

### Selector Reliability Test
```typescript
it('should find element with selector', async () => {
  const session = await client.sessions.create();
  const browser = await chromium.connectOverCDP(/*...*/);
  const page = browser.contexts()[0].pages()[0];
  
  await page.goto('https://example.com');
  const element = await page.waitForSelector('[data-testid="target"]');
  
  expect(element).toBeTruthy();
});
```

### Data Extraction Test
```typescript
it('should extract correct data', async () => {
  // Setup session and page
  const data = await page.evaluate(() => {
    return {
      title: document.querySelector('h1')?.textContent,
      price: document.querySelector('.price')?.textContent
    };
  });
  
  expect(data.title).toBeTruthy();
  expect(data.price).toMatch(/\$\d+/);
});
```

## Running Tests

Guide users to use standard test runners:
- Jest: `npm test` or `jest`
- Vitest: `vitest run`
- Pytest: `pytest tests/`

Always provide working test examples they can copy and modify.

---
description: Debug Steel automation issues and provide solutions
---

# Steel Debugging Assistant

Help the user debug Steel automation issues using live sessions and error analysis.

## Debugging Approach

1. **Ask to see the error** - Get the full error message and stack trace
2. **Check the live session** - Ask if they're using `sessionViewerUrl` to see what's happening
3. **Identify the issue** - Match error patterns to common Steel issues
4. **Provide specific fix** - Give working code that solves the problem

## Steel CLI for Debugging

If user has Steel CLI (`@steel-dev/cli`), suggest:
- `steel config` - Verify API key and configuration
- `steel run <template> --view` - Run working example to compare
- `steel browser start --verbose` - Start local browser with detailed logs

Install if needed: `npm install -g @steel-dev/cli`

## Common Steel Issues & Fixes

### Issue: Session Creation Timeout
**Symptoms**: `Session creation timed out` or similar errors

**Solution**:
```typescript
const session = await client.sessions.create({
  sessionTimeout: 60000, // Increase timeout to 60s
});
```

### Issue: Selector Not Found
**Symptoms**: `TimeoutError: waiting for selector failed`

**Solution**:
```typescript
// Wait for page to fully load
await page.waitForLoadState('networkidle');

// Try multiple selectors
const selectors = [
  '[data-testid="submit"]',
  '#submit-button',
  'button:has-text("Submit")'
];

for (const selector of selectors) {
  try {
    await page.waitForSelector(selector, { timeout: 5000 });
    break;
  } catch (e) {
    continue;
  }
}
```

### Issue: WebSocket Connection Failed
**Symptoms**: `WebSocket connection failed` or `CDP connection error`

**Check**:
```typescript
// Ensure API key is passed correctly
const wsUrl = `${session.websocketUrl}?apiKey=${process.env.STEEL_API_KEY}`;
const browser = await chromium.connectOverCDP(wsUrl);
```

### Issue: Session Not Releasing
**Symptoms**: Running out of concurrent sessions

**Solution**:
```typescript
// Always use try-finally
const session = await client.sessions.create();
try {
  // Your automation code
} finally {
  await client.sessions.release(session.id);
}
```

### Issue: Page Loading Too Slowly
**Symptoms**: Timeouts during page navigation

**Solution**:
```typescript
const session = await client.sessions.create({
  blockAds: true, // Block ads for faster loading
  dimensions: { width: 1280, height: 800 }
});

await page.goto(url, { 
  waitUntil: 'domcontentloaded' // Don't wait for everything
});
```

### Issue: CAPTCHA Blocking Automation
**Symptoms**: Automation stops at CAPTCHA pages

**Solution**:
```typescript
const session = await client.sessions.create({
  solveCaptcha: true, // Enable CAPTCHA solving
  useProxy: true      // Use residential proxies
});
```

### Issue: Cannot Find Elements in Iframe
**Symptoms**: Elements exist but selector fails

**Solution**:
```typescript
// Wait for iframe
const frameElement = await page.waitForSelector('iframe');
const frame = await frameElement.contentFrame();

// Search inside iframe
await frame.waitForSelector('[data-testid="target"]');
```

### Issue: Dynamic Content Not Loading
**Symptoms**: Elements appear later but selector times out

**Solution**:
```typescript
// Wait for network to be idle
await page.waitForLoadState('networkidle');

// Or wait for specific element
await page.waitForSelector('[data-testid="loaded-content"]', {
  state: 'visible',
  timeout: 15000
});
```

## Debugging Workflow

When user reports an issue:

1. **Get context**:
   - What are they trying to do?
   - What error message do they see?
   - Can they share the code?

2. **Use live session**:
   ```typescript
   const session = await client.sessions.create();
   console.log('Debug at:', session.sessionViewerUrl);
   // User can watch what's happening in real-time
   ```

3. **Add debugging output**:
   ```typescript
   console.log('Step 1: Creating session');
   const session = await client.sessions.create();
   console.log('Session ID:', session.id);
   
   console.log('Step 2: Connecting browser');
   const browser = await chromium.connectOverCDP(/*...*/);
   
   console.log('Step 3: Navigating to page');
   await page.goto(url);
   console.log('Current URL:', page.url());
   ```

4. **Test selectors**:
   ```typescript
   // Check if element exists
   const element = await page.$('[data-testid="target"]');
   if (element) {
     console.log('Element found!');
   } else {
     console.log('Element not found, trying alternatives...');
   }
   ```

5. **Provide working solution** with explanation

## When to Check Steel Status

If issues persist, suggest checking:
- Steel Cloud status: https://status.steel.dev
- API key validity: Check in Steel dashboard
- Session limits: Check concurrent session limits
- Network issues: Test from different network

Always focus on practical, immediate solutions over theoretical debugging approaches.

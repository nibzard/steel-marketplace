---
description: Help deploy Steel automation to production environments
---

# Steel Deployment Assistant

Help the user deploy Steel automation to production with best practices and configurations.

## Deployment Options

### 1. Docker Deployment

**Create Dockerfile**:
```dockerfile
FROM node:18-alpine

WORKDIR /app

# Install dependencies
COPY package*.json ./
RUN npm ci --only=production

# Copy source
COPY . .

# Build if using TypeScript
RUN npm run build

# Run the automation
CMD ["node", "dist/index.js"]
```

**Docker Compose with Steel**:
```yaml
version: '3.8'
services:
  steel-automation:
    build: .
    environment:
      - STEEL_API_KEY=${STEEL_API_KEY}
      - NODE_ENV=production
    restart: unless-stopped
```

### 2. Serverless Deployment (AWS Lambda, etc.)

**Key Considerations**:
- Steel handles the browser, your function just needs to call the API
- Set appropriate timeouts (Lambda: 15 min max)
- Use environment variables for API keys

**Lambda Handler Example**:
```typescript
import { Steel } from 'steel-sdk';

export const handler = async (event: any) => {
  const client = new Steel({ 
    steelAPIKey: process.env.STEEL_API_KEY! 
  });
  
  const session = await client.sessions.create();
  
  try {
    // Your automation logic
    const result = await runAutomation(session);
    return { statusCode: 200, body: JSON.stringify(result) };
  } finally {
    await client.sessions.release(session.id);
  }
};
```

### 3. Scheduled Jobs (Cron, GitHub Actions)

**GitHub Actions Example**:
```yaml
name: Daily Steel Scraping

on:
  schedule:
    - cron: '0 9 * * *'  # Daily at 9 AM UTC
  workflow_dispatch:

jobs:
  scrape:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run Steel automation
        env:
          STEEL_API_KEY: ${{ secrets.STEEL_API_KEY }}
        run: npm start
```

### 4. Kubernetes Deployment

**Deployment YAML**:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: steel-automation
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: automation
        image: your-registry/steel-automation:latest
        env:
        - name: STEEL_API_KEY
          valueFrom:
            secretKeyRef:
              name: steel-secrets
              key: api-key
        resources:
          limits:
            memory: "512Mi"
            cpu: "500m"
```

## Production Best Practices

### Environment Configuration
```typescript
const config = {
  steel: {
    apiKey: process.env.STEEL_API_KEY,
    sessionTimeout: parseInt(process.env.STEEL_TIMEOUT || '60000'),
    retries: parseInt(process.env.STEEL_RETRIES || '3')
  },
  // Other config...
};
```

### Error Handling & Retries
```typescript
async function runWithRetry(fn: () => Promise<any>, retries = 3) {
  for (let i = 0; i < retries; i++) {
    try {
      return await fn();
    } catch (error) {
      console.error(`Attempt ${i + 1} failed:`, error);
      if (i === retries - 1) throw error;
      await new Promise(r => setTimeout(r, 1000 * (i + 1))); // Backoff
    }
  }
}
```

### Session Management
```typescript
// Always clean up sessions
const session = await client.sessions.create();
try {
  await runAutomation(session);
} catch (error) {
  console.error('Automation failed:', error);
  console.error('Session viewer:', session.sessionViewerUrl);
  throw error;
} finally {
  await client.sessions.release(session.id);
}
```

### Logging & Monitoring
```typescript
// Structured logging
console.log(JSON.stringify({
  timestamp: new Date().toISOString(),
  level: 'info',
  message: 'Session created',
  sessionId: session.id,
  viewerUrl: session.sessionViewerUrl
}));

// Track metrics
const startTime = Date.now();
await runAutomation(session);
const duration = Date.now() - startTime;
console.log(JSON.stringify({
  metric: 'automation_duration_ms',
  value: duration
}));
```

### Security Checklist
- ✅ Store API keys in environment variables or secrets management
- ✅ Never commit `.env` files with real keys
- ✅ Use least-privilege API keys when possible
- ✅ Enable rate limiting in your application
- ✅ Monitor for unusual usage patterns
- ✅ Set session timeouts to prevent runaway sessions

### Cost Optimization
```typescript
// Reuse sessions when possible
const sessionPool = new SessionPool(client, { maxSize: 5 });

// Use appropriate dimensions
const session = await client.sessions.create({
  dimensions: { width: 1280, height: 800 } // Smaller = faster
});

// Block unnecessary resources
const session = await client.sessions.create({
  blockAds: true // Faster and cheaper
});
```

## Deployment Checklist

Before deploying, verify:

1. **Environment Variables Set**:
   - `STEEL_API_KEY` configured
   - Other necessary config variables

2. **Error Handling**:
   - Try-catch blocks around Steel operations
   - Proper session cleanup in finally blocks
   - Retry logic for transient failures

3. **Resource Limits**:
   - Appropriate timeouts configured
   - Memory limits set (if containerized)
   - Concurrent session limits considered

4. **Monitoring**:
   - Logging configured
   - Error tracking (Sentry, etc.) set up
   - Metrics collection enabled

5. **Testing**:
   - Tested in staging environment
   - Load tested if expecting high volume
   - Verified session cleanup works

## Common Deployment Issues

### Issue: API Key Not Found
```typescript
if (!process.env.STEEL_API_KEY) {
  throw new Error('STEEL_API_KEY environment variable not set');
}
```

### Issue: Timeouts in Production
```typescript
const session = await client.sessions.create({
  sessionTimeout: 120000 // 2 minutes for production
});
```

### Issue: Too Many Concurrent Sessions
```typescript
// Implement session pooling or queuing
const queue = new PQueue({ concurrency: 5 });
await queue.add(() => runAutomation());
```

Guide users through their specific deployment scenario with practical, working configurations.

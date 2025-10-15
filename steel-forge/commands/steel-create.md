---
description: Create a new Steel browser automation project with best practices
---

# Steel Project Creator

Help the user create a new Steel automation project. Guide them through setup and generate starter code.

## Steps

**Note**: If the user has Steel CLI installed, you can use `steel forge <template>` to create projects instantly from official templates. Check with `which steel` or suggest installing: `npm install -g @steel-dev/cli`

Available templates: `playwright`, `playwright-py`, `puppeteer`, `browser-use`, `oai-cua`, and more.

1. **Ask about the project**:
   - Project name
   - Use case (web scraping, testing, e-commerce automation, etc.)
   - Language preference (TypeScript or Python)

2. **Create project structure** (or use `steel forge` if CLI available):
   ```
   project-name/
   ├── package.json (or requirements.txt)
   ├── .env.example
   ├── src/
   │   └── index.ts (or main.py)
   └── README.md
   ```

3. **Generate starter code** based on use case:

   **TypeScript Example**:
   ```typescript
   import { Steel } from 'steel-sdk';
   
   const client = new Steel({
     steelAPIKey: process.env.STEEL_API_KEY!
   });
   
   async function main() {
     const session = await client.sessions.create({
       dimensions: { width: 1280, height: 800 }
     });
     
     console.log('Session created:', session.id);
     console.log('Live view:', session.sessionViewerUrl);
     
     // Your automation code here
     
     await client.sessions.release(session.id);
   }
   
   main().catch(console.error);
   ```

   **Python Example**:
   ```python
   from steel import Steel
   import os
   
   client = Steel(steel_api_key=os.getenv('STEEL_API_KEY'))
   
   def main():
       session = client.sessions.create(
           dimensions={'width': 1280, 'height': 800}
       )
       
       print(f'Session created: {session.id}')
       print(f'Live view: {session.session_viewer_url}')
       
       # Your automation code here
       
       client.sessions.release(session.id)
   
   if __name__ == '__main__':
       main()
   ```

4. **Create .env.example**:
   ```
   STEEL_API_KEY=your_api_key_here
   ```

5. **Create README.md** with:
   - Project description
   - Setup instructions
   - How to run
   - Link to Steel docs: https://docs.steel.dev

## Key Steel Features to Mention

- **Live Sessions**: Use `session.sessionViewerUrl` to watch automation in real-time
- **Proxy Support**: Add `useProxy: true` for geo-specific automation
- **CAPTCHA Solving**: Add `solveCaptcha: true` to handle CAPTCHAs
- **File Operations**: Use `/v1/sessions/:id/files` API for file uploads/downloads
- **Multi-tab**: Steel supports multiple tabs per session

## Best Practices

- Always release sessions when done
- Use environment variables for API keys
- Enable session viewer URLs during development
- Handle errors gracefully
- Set appropriate timeouts

After creating the project, guide the user through installing dependencies and running the first session.

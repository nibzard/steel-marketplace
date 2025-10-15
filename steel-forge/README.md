# Steel Forge - Claude Code Plugin

A Claude Code plugin that provides expert assistance for building browser automation with [Steel](https://steel.dev).

## What This Plugin Does

Steel Forge enhances Claude Code with specialized knowledge about Steel browser automation, providing:

- **Commands** for common Steel development tasks
- **Expert agents** for specialized assistance (CLI-aware)
- **Best practices** and working code examples
- **Debugging help** for common Steel issues
- **Steel CLI integration** - Agents know about and can use the Steel CLI

## Steel CLI

This plugin is aware of the Steel CLI (`@steel-dev/cli`) and can leverage it when available:
- `steel forge <template>` - Create projects from official templates
- `steel run <template> --view` - Run cookbook examples instantly
- `steel browser start` - Start local Steel browser for development
- `steel config` - View current configuration

Install the Steel CLI: `npm install -g @steel-dev/cli`

## Installation

### Local Development

1. Create a local marketplace:
```bash
mkdir steel-marketplace
cd steel-marketplace
mkdir .claude-plugin
```

2. Create marketplace manifest (`.claude-plugin/marketplace.json`):
```json
{
  "name": "steel-marketplace",
  "owner": {
    "name": "Local Dev"
  },
  "plugins": [
    {
      "name": "steel-forge",
      "source": "./path/to/plugin",
      "description": "Steel browser automation assistant"
    }
  ]
}
```

3. In Claude Code:
```
/plugin marketplace add ./steel-marketplace
/plugin install steel-forge@steel-marketplace
```

4. Restart Claude Code to load the plugin

## Available Commands

### `/steel-create` - Create New Project
Creates a new Steel automation project with proper structure and starter code.

**Example**: `/steel-create` then follow the prompts

### `/steel-dev` - Development Assistant
Provides development help with live debugging, code generation, and best practices.

**Use for**:
- Getting help with Steel SDK usage
- Debugging live sessions with sessionViewerUrl
- Learning Steel patterns and best practices

### `/steel-test` - Testing Assistant
Helps write tests for Steel automation using standard testing frameworks.

**Use for**:
- Writing unit, integration, and E2E tests
- Testing Steel session management
- Implementing test best practices

### `/steel-debug` - Debugging Assistant
Diagnoses and fixes Steel automation issues.

**Use for**:
- Troubleshooting errors and failures
- Fixing selector and timing issues
- Resolving session connection problems

### `/steel-deploy` - Deployment Assistant
Guides deployment to production environments.

**Use for**:
- Docker and Kubernetes deployments
- Serverless functions (AWS Lambda, etc.)
- Scheduled jobs and cron automation
- Production best practices

## Available Agents

Claude can automatically invoke these specialized agents when appropriate:

### Architect Agent
**Expertise**: System design, scalability, architecture patterns
**Use when**: Designing new systems, planning for scale, structuring projects

### Debugger Agent
**Expertise**: Error diagnosis, troubleshooting, fixing issues
**Use when**: Automation fails, selectors don't work, sessions timeout

### Optimizer Agent
**Expertise**: Performance optimization, cost reduction, efficiency
**Use when**: Code is slow, costs too high, need better throughput

### Scout Agent
**Expertise**: Code exploration, understanding existing projects
**Use when**: Working with legacy code, documenting systems, learning patterns

## Quick Start Examples

### Create a Simple Scraper
```
/steel-create

Then follow prompts to create a web scraping project
```

### Debug a Failing Automation
```
/steel-debug

I'm getting "TimeoutError: waiting for selector" - can you help?
[Paste your code]
```

### Optimize Performance
```
Ask the Optimizer agent: "My scraper is slow, can you review this code?"
[Paste your code]
```

### Design a Scalable System
```
Ask the Architect agent: "I need to scrape 10,000 products daily, how should I structure this?"
```

## Steel Resources

- **Documentation**: https://docs.steel.dev
- **API Reference**: https://docs.steel.dev/api-reference
- **Cookbook**: https://github.com/steel-dev/steel-cookbook
- **Discord**: https://discord.gg/steel-dev
- **Dashboard**: https://app.steel.dev

## Features This Plugin Knows About

- ✅ Session creation and management
- ✅ Live session debugging with sessionViewerUrl
- ✅ Playwright integration via CDP
- ✅ Proxy support and geo-targeting
- ✅ CAPTCHA solving
- ✅ Multi-tab automation
- ✅ File upload/download API
- ✅ Ad blocking and resource optimization
- ✅ Session pooling and reuse
- ✅ Docker and serverless deployment

## Tips for Best Results

1. **Be specific**: Describe exactly what you're trying to build or fix
2. **Share code**: Paste relevant code when asking for help
3. **Use commands**: Start with `/steel-` commands for guided assistance
4. **Leverage agents**: Let Claude invoke specialized agents automatically
5. **Check live sessions**: Always use sessionViewerUrl during development

## Examples

**Example 1: Create a new project**
```
User: /steel-create
Claude: [Guides through project creation with TypeScript/Python choice]
```

**Example 2: Debug selector issue**
```
User: /steel-debug My selector times out: await page.waitForSelector('.product')
Claude: [Provides specific fixes for timing issues]
```

**Example 3: Optimize slow code**
```
User: This code is slow, can the optimizer help?
[Paste code]
Claude: [Optimizer agent analyzes and suggests improvements]
```

## Contributing

To improve this plugin:

1. Edit command files in `commands/` to improve instructions
2. Update agent files in `agents/` to enhance expertise
3. Keep examples practical and up-to-date with Steel SDK
4. Test changes locally before distributing

## License

MIT

## Support

For plugin issues: Open an issue in the repository
For Steel support: https://discord.gg/steel-dev

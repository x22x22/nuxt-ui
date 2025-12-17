# Nuxt UI Documentation Site

This is the official documentation site for Nuxt UI, built with Nuxt and powered by Nuxt Content.

## Features

- 📚 Comprehensive component documentation
- 🤖 Built-in MCP (Model Context Protocol) server
- 🔍 AI-powered search using MCP tools
- 📝 Interactive examples and code snippets
- 🎨 Live component demonstrations

## Independent Deployment

This documentation site can be deployed independently from the main Nuxt UI library. It is a complete, standalone Nuxt application.

### Quick Start

```bash
# Install dependencies
pnpm install

# Development
pnpm dev

# Build for production
pnpm build

# Preview production build
node .output/server/index.mjs
```

## MCP Server

The documentation site includes an integrated MCP server that provides AI assistants with access to component information, documentation, and examples.

### MCP Endpoint

When deployed, the MCP server is available at: `/mcp`

For example, the production MCP server is at: https://ui.nuxt.com/mcp

### Connecting AI Assistants

You can connect various AI assistants to the MCP server:

**Claude Code:**
```bash
claude mcp add --transport http nuxt-ui-remote https://ui.nuxt.com/mcp
```

**Cursor:** Add to `.cursor/mcp.json`:
```json
{
  "mcpServers": {
    "nuxt-ui": {
      "type": "http",
      "url": "https://ui.nuxt.com/mcp"
    }
  }
}
```

For more configuration options, see the [MCP documentation](./content/docs/1.getting-started/7.ai/1.mcp.md).

### How It Works

The MCP server is powered by `@nuxtjs/mcp-toolkit`, which automatically:

1. Scans `server/mcp/` directory for tools, resources, and prompts
2. Creates an HTTP endpoint at `/mcp` implementing the MCP protocol
3. Registers all discovered capabilities with the MCP server

### MCP Implementation Structure

```
server/mcp/
├── tools/              # AI-callable tools
│   ├── list-components.ts
│   ├── get-component.ts
│   ├── get-component-metadata.ts
│   ├── list-templates.ts
│   ├── get-template.ts
│   ├── search-components-by-category.ts
│   └── ... (more tools)
├── resources/          # Discoverable resources
│   ├── components.ts
│   ├── composables.ts
│   ├── examples.ts
│   ├── templates.ts
│   └── documentation-pages.ts
└── prompts/            # Guided workflows
    ├── find-component-for-usecase.ts
    ├── implement-component-with-props.ts
    └── setup-project-with-template.ts
```

## Environment Variables

For production deployment, configure:

```bash
# .env
NUXT_PUBLIC_SITE_URL=https://your-domain.com
NUXT_GITHUB_TOKEN=your_github_token  # Optional: for fetching additional content
AI_GATEWAY_API_KEY=your_api_key      # Optional: for AI gateway features
```

## Deployment

The built application (`.output` directory) can be deployed to any Node.js hosting platform:

- Vercel
- Netlify
- AWS
- Google Cloud
- Your own Node.js server

The MCP server will be automatically available at the `/mcp` endpoint after deployment.

## Documentation

For detailed information about deploying and using the MCP server, see:

- [MCP Deployment Guide](./MCP_DEPLOYMENT.md) - Comprehensive deployment guide (English & 中文)
- [MCP Documentation](./content/docs/1.getting-started/7.ai/1.mcp.md) - User-facing MCP setup guide

## Tech Stack

- **Framework:** Nuxt 4
- **Content:** @nuxt/content
- **UI Library:** @nuxt/ui (workspace dependency)
- **MCP Server:** @nuxtjs/mcp-toolkit
- **AI Integration:** ai, @ai-sdk/mcp, @ai-sdk/vue
- **Type Safety:** TypeScript

## License

[MIT License](../LICENSE.md)

# MCP Server Deployment Guide | MCP 服务器部署指南

[English](#english) | [中文](#中文)

---

## English

### Overview

This document answers common questions about deploying the Nuxt UI documentation site and its integrated MCP (Model Context Protocol) server.

### Can docs/package.json be deployed independently?

**Yes**, the `docs/package.json` can be deployed independently as a standalone Nuxt application.

The `docs` directory is a complete Nuxt application with its own:
- `package.json` with all necessary dependencies
- `nuxt.config.ts` configuration file
- Server-side API routes and MCP tools
- Content management via `@nuxt/content`

### Deployment Steps

1. **Install dependencies:**
   ```bash
   cd docs
   pnpm install
   # or npm install, yarn install, bun install
   ```

2. **Build for production:**
   ```bash
   pnpm build
   # This creates a .output directory with the production build
   ```

3. **Deploy the `.output` directory** to your hosting platform (Vercel, Netlify, Node.js server, etc.)

### Does deployment automatically start the MCP server?

**Yes**, when you deploy the docs application, the MCP server is automatically started and available at the `/mcp` endpoint.

This is handled by the `@nuxtjs/mcp-toolkit` module, which is configured in `nuxt.config.ts`:

```typescript
export default defineNuxtConfig({
  modules: [
    '@nuxtjs/mcp-toolkit',
    // ... other modules
  ],
  mcp: {
    name: 'Nuxt UI',
    browserRedirect: '/docs/getting-started/ai/mcp'
  }
})
```

The toolkit automatically:
- Scans the `server/mcp/` directory for tools, resources, and prompts
- Creates an HTTP endpoint at `/mcp` that implements the MCP protocol
- Registers all discovered tools and resources with the MCP server

### Is the deployed MCP service the same as https://ui.nuxt.com/mcp?

**Yes, exactly**. When you use the command:

```bash
claude mcp add --transport http nuxt-ui-remote https://ui.nuxt.com/mcp
```

You are connecting to the MCP server that is running as part of the deployed Nuxt UI documentation site at `https://ui.nuxt.com`.

### How the MCP Server Works

1. **Server-side tools** are defined in `docs/server/mcp/tools/` directory:
   - `list-components.ts` - Lists all UI components
   - `get-component.ts` - Gets specific component details
   - `get-component-metadata.ts` - Gets component props and types
   - And many more...

2. **Resources** are defined in `docs/server/mcp/resources/` directory:
   - `components.ts` - Component resource definitions
   - `examples.ts` - Example code resources
   - `templates.ts` - Project template resources
   - And more...

3. **Prompts** are defined in `docs/server/mcp/prompts/` directory for guided workflows

4. The `@nuxtjs/mcp-toolkit` module automatically:
   - Discovers all these files
   - Creates the `/mcp` HTTP endpoint
   - Implements the MCP protocol specification
   - Handles client connections from AI assistants

### Environment Variables

For production deployment, you may want to configure:

```bash
# .env
NUXT_PUBLIC_SITE_URL=https://your-domain.com
NUXT_GITHUB_TOKEN=your_github_token  # Optional: for fetching additional content
AI_GATEWAY_API_KEY=your_api_key      # Optional: for AI features
```

### Verification

After deployment, you can verify the MCP server is running:

1. Visit `https://your-domain.com/mcp` in a browser
   - You should be redirected to `/docs/getting-started/ai/mcp`

2. Use an MCP client to connect:
   ```bash
   claude mcp add --transport http your-mcp-server https://your-domain.com/mcp
   ```

3. The MCP server will respond with the list of available tools and resources

### Architecture

```
docs/
├── package.json                    # Standalone Nuxt app dependencies
├── nuxt.config.ts                  # Configures @nuxtjs/mcp-toolkit
├── server/
│   ├── mcp/                        # MCP server implementation
│   │   ├── tools/                  # MCP tools (automatically discovered)
│   │   ├── resources/              # MCP resources (automatically discovered)
│   │   └── prompts/                # MCP prompts (automatically discovered)
│   └── api/                        # Regular API routes
└── content/                        # Markdown documentation content
```

When deployed, Nitro (Nuxt's server engine) serves:
- Static pages and assets
- API routes at `/api/*`
- **MCP endpoint at `/mcp`** (configured by mcp-toolkit)

---

## 中文

### 概述

本文档回答关于部署 Nuxt UI 文档站点及其集成的 MCP（模型上下文协议）服务器的常见问题。

### docs/package.json 是否可以单独部署？

**是的**，`docs/package.json` 可以作为独立的 Nuxt 应用程序单独部署。

`docs` 目录是一个完整的 Nuxt 应用程序，包含：
- `package.json` 及所有必需的依赖项
- `nuxt.config.ts` 配置文件
- 服务器端 API 路由和 MCP 工具
- 通过 `@nuxt/content` 进行内容管理

### 部署步骤

1. **安装依赖：**
   ```bash
   cd docs
   pnpm install
   # 或者 npm install, yarn install, bun install
   ```

2. **生产环境构建：**
   ```bash
   pnpm build
   # 这会创建一个包含生产构建的 .output 目录
   ```

3. **部署 `.output` 目录**到您的托管平台（Vercel、Netlify、Node.js 服务器等）

### 部署后会自动启动 MCP 服务吗？

**是的**，当您部署 docs 应用程序时，MCP 服务器会自动启动并在 `/mcp` 端点可用。

这由 `@nuxtjs/mcp-toolkit` 模块处理，它在 `nuxt.config.ts` 中配置：

```typescript
export default defineNuxtConfig({
  modules: [
    '@nuxtjs/mcp-toolkit',
    // ... 其他模块
  ],
  mcp: {
    name: 'Nuxt UI',
    browserRedirect: '/docs/getting-started/ai/mcp'
  }
})
```

该工具包会自动：
- 扫描 `server/mcp/` 目录查找工具、资源和提示词
- 创建实现 MCP 协议的 HTTP 端点 `/mcp`
- 将所有发现的工具和资源注册到 MCP 服务器

### 部署的 MCP 服务是否就是命令中 https://ui.nuxt.com/mcp 对应的 MCP 服务？

**是的，完全正确**。当您使用命令：

```bash
claude mcp add --transport http nuxt-ui-remote https://ui.nuxt.com/mcp
```

您正在连接到作为部署在 `https://ui.nuxt.com` 的 Nuxt UI 文档站点一部分运行的 MCP 服务器。

### MCP 服务器的工作原理

1. **服务器端工具**定义在 `docs/server/mcp/tools/` 目录中：
   - `list-components.ts` - 列出所有 UI 组件
   - `get-component.ts` - 获取特定组件详情
   - `get-component-metadata.ts` - 获取组件属性和类型
   - 还有更多...

2. **资源**定义在 `docs/server/mcp/resources/` 目录中：
   - `components.ts` - 组件资源定义
   - `examples.ts` - 示例代码资源
   - `templates.ts` - 项目模板资源
   - 还有更多...

3. **提示词**定义在 `docs/server/mcp/prompts/` 目录中，用于引导工作流

4. `@nuxtjs/mcp-toolkit` 模块会自动：
   - 发现所有这些文件
   - 创建 `/mcp` HTTP 端点
   - 实现 MCP 协议规范
   - 处理来自 AI 助手的客户端连接

### 环境变量

对于生产部署，您可能需要配置：

```bash
# .env
NUXT_PUBLIC_SITE_URL=https://your-domain.com
NUXT_GITHUB_TOKEN=your_github_token  # 可选：用于获取额外内容
AI_GATEWAY_API_KEY=your_api_key      # 可选：用于 AI 功能
```

### 验证

部署后，您可以验证 MCP 服务器是否正在运行：

1. 在浏览器中访问 `https://your-domain.com/mcp`
   - 您应该被重定向到 `/docs/getting-started/ai/mcp`

2. 使用 MCP 客户端连接：
   ```bash
   claude mcp add --transport http your-mcp-server https://your-domain.com/mcp
   ```

3. MCP 服务器将响应可用工具和资源的列表

### 架构

```
docs/
├── package.json                    # 独立 Nuxt 应用依赖
├── nuxt.config.ts                  # 配置 @nuxtjs/mcp-toolkit
├── server/
│   ├── mcp/                        # MCP 服务器实现
│   │   ├── tools/                  # MCP 工具（自动发现）
│   │   ├── resources/              # MCP 资源（自动发现）
│   │   └── prompts/                # MCP 提示词（自动发现）
│   └── api/                        # 常规 API 路由
└── content/                        # Markdown 文档内容
```

部署后，Nitro（Nuxt 的服务器引擎）提供：
- 静态页面和资源
- `/api/*` 的 API 路由
- **`/mcp` 的 MCP 端点**（由 mcp-toolkit 配置）

---

## Additional Notes

### Dependencies

The key dependencies that enable MCP functionality:

- `@nuxtjs/mcp-toolkit@^0.5.2` - Nuxt module that provides MCP server capabilities
- `@modelcontextprotocol/sdk@^1.24.3` - Official MCP SDK
- `@ai-sdk/mcp@^0.0.12` - AI SDK MCP integration
- `@nuxt/content@^3.9.0` - Content management system for documentation

### Related Files

- `/docs/content/docs/1.getting-started/7.ai/1.mcp.md` - User-facing MCP documentation
- `/docs/nuxt.config.ts` - Main configuration with MCP setup
- `/docs/server/mcp/` - All MCP server implementation files
- `/docs/server/api/search.ts` - Example of internal MCP client usage

### Support

For issues or questions:
- GitHub Issues: https://github.com/nuxt/ui/issues
- Documentation: https://ui.nuxt.com/docs/getting-started/ai/mcp
- MCP Protocol: https://modelcontextprotocol.io/

---

## 补充说明

### 依赖项

启用 MCP 功能的关键依赖项：

- `@nuxtjs/mcp-toolkit@^0.5.2` - 提供 MCP 服务器功能的 Nuxt 模块
- `@modelcontextprotocol/sdk@^1.24.3` - 官方 MCP SDK
- `@ai-sdk/mcp@^0.0.12` - AI SDK MCP 集成
- `@nuxt/content@^3.9.0` - 用于文档的内容管理系统

### 相关文件

- `/docs/content/docs/1.getting-started/7.ai/1.mcp.md` - 面向用户的 MCP 文档
- `/docs/nuxt.config.ts` - 包含 MCP 设置的主配置
- `/docs/server/mcp/` - 所有 MCP 服务器实现文件
- `/docs/server/api/search.ts` - 内部 MCP 客户端使用示例

### 支持

如有问题或疑问：
- GitHub Issues: https://github.com/nuxt/ui/issues
- 文档: https://ui.nuxt.com/docs/getting-started/ai/mcp
- MCP 协议: https://modelcontextprotocol.io/

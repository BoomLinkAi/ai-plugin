# Boomlink AI Plugin

Official Boomlink plugin for AI clients. Boomlink is a plugin-and-template site platform for building
working websites with ChatGPT or Claude, with hosting and a built-in database.

Works with **Claude Code**, **Codex**, **Cursor**, **Antigravity**, and **Gemini CLI**. Boomlink is
also available in **ChatGPT** as an app.

- Website: https://boomlink.ai
- Docs: https://boomlink.ai/docs
- Examples: https://boomlink.ai/examples

## MCP server

The plugin connects to the hosted Boomlink MCP server. There is nothing to install or run.

```
https://boomlink.ai/api/v1/chatgpt-app
```

- Transport: Streamable HTTP
- Auth: OAuth with dynamic client registration. Your client opens a Boomlink sign-in page the first
  time it connects.
- Account: sign up at https://boomlink.ai/auth. The Free plan includes one site on a Boomlink
  subdomain.

## Installation

This repo is a self-contained, multi-client plugin bundle. Until it is published to each client's
marketplace, load it as a local plugin directory.

### Claude Code

```bash
git clone https://github.com/BoomLinkAi/ai-plugin boomlink-ai-plugin
claude --plugin-dir ./boomlink-ai-plugin
```

The bundled `.mcp.json` wires the Boomlink MCP server automatically. Use `/mcp` inside Claude Code to
sign in and check the connection.

To add only the MCP server, without the skills:

```bash
claude mcp add --transport http boomlink https://boomlink.ai/api/v1/chatgpt-app
```

### Antigravity (Google)

Place the plugin folder in one of Antigravity's plugin locations, then restart:

```bash
# Workspace-level (this project only)
mkdir -p .agents/plugins && cp -R /path/to/boomlink-ai-plugin .agents/plugins/

# Global (all workspaces)
mkdir -p ~/.gemini/config/plugins && cp -R /path/to/boomlink-ai-plugin ~/.gemini/config/plugins/
```

Antigravity reads the root `plugin.json` marker, the `skills/`, and `mcp_config.json` automatically.

### Cursor

Add manually in **Cursor Settings > MCP / Plugins**, pointing at this folder. Cursor's stable surface
is MCP config. The bundled `.mcp.json` provides it.

### Codex

Add the Boomlink plugin marketplace, then install the plugin from that marketplace:

```bash
codex plugin marketplace add BoomLinkAi/ai-plugin
codex plugin add boomlink-ai-plugin@boomlink
```

### Gemini CLI

```bash
gemini extensions install https://github.com/BoomLinkAi/ai-plugin
```

The bundled `gemini-extension.json` connects the Boomlink MCP server via `mcp-remote`.

### Other MCP clients

Point any client that supports remote MCP servers at `https://boomlink.ai/api/v1/chatgpt-app`. For
clients without remote MCP support:

```bash
npx mcp-remote https://boomlink.ai/api/v1/chatgpt-app
```

## Features

- Start a site from a complete template with its own database (event, fitness, portfolio,
  restaurant, SaaS, full-stack app)
- Edit pages, content and navigation in draft authoring sessions with a live preview
- Add isolated plugins such as a blog and forms
- Preview, publish, list versions and roll back
- Manage assets and site config

Nothing a visitor sees changes until you approve a publish.

### Bundled skills

Your AI client loads these on demand when your request matches their description:

| Skill | Use it to |
| --- | --- |
| `getting-started` | see your sites and get one recommended next step |
| `create-website` | build a new draft site from a template |
| `edit-website` | change content, pages or navigation without publishing |
| `frontend-design` | lift the visual design of existing pages |
| `write-copy` | rewrite page copy so it converts |
| `optimize-page` | review a page for conversion problems and fix the biggest ones |
| `seo-setup` | titles, descriptions, schema markup and AI search visibility |
| `customize-site-admin` | build or change the site's custom admin dashboard |
| `publish-website` | take approved changes live, or roll back |

## Example usage

```
> Show me my Boomlink websites and what to do next
> Create a portfolio site for my photography studio. Do not publish it.
> Change the homepage headline to "Welcome to my studio" and show me the preview
> Set up SEO for my site
> Publish my site
```

## Pricing

| Plan | Price | Includes |
| --- | --- | --- |
| Free | $0 | 1 site, Boomlink subdomain, basic analytics |
| Starter | $12/mo | 5 sites, 1 custom domain + SSL, remove branding |
| Team | $29/mo | 25 sites, 5 custom domains + SSL, roles and permissions, 5 seats |

## How to develop

```bash
git clone https://github.com/BoomLinkAi/ai-plugin boomlink-ai-plugin
claude --plugin-dir ./boomlink-ai-plugin
```

- Add new workflow instructions under `skills/<name>/SKILL.md`.
- See `references/plugin-types.md` for per-client manifest notes.
- To use a different endpoint, set `BOOMLINK_AI_PLUGIN_MCP_URL` (Gemini CLI) or edit the server URL
  in `.mcp.json`, `mcp.json`, and `mcp_config.json`.

## Support

Email founder@boomlink.ai or open an issue in this repository.

- Privacy: https://boomlink.ai/privacy
- Terms: https://boomlink.ai/terms-and-conditions

## License

MIT

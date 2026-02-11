# Jamdesk CLI

[![npm version](https://img.shields.io/npm/v/jamdesk)](https://www.npmjs.com/package/jamdesk)
[![Node.js](https://img.shields.io/node/v/jamdesk)](https://nodejs.org)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](./LICENSE)

CLI for [Jamdesk](https://www.jamdesk.com) — build, preview, and deploy documentation sites from MDX.

![Jamdesk documentation site showing dark mode with sidebar navigation and API reference](https://www.jamdesk.com/cli-screenshot.jpg)

## What is Jamdesk?

Jamdesk is a docs-as-code platform. Connect a GitHub repo, write in MDX, and your docs deploy globally in seconds — with AI search, analytics, and custom domains built in. The CLI is how you develop locally: preview your site, validate config, catch broken links, and migrate from other tools.

- **Dev server** — Turbopack-powered with hot reload on every save
- **50+ MDX components** — accordions, tabs, code groups, callouts, [and more](https://www.jamdesk.com/docs/components/overview)
- **Three themes** — Jam, Nebula, Pulsar. Configured in `docs.json`
- **OpenAPI** — auto-generate API reference pages from your specs
- **Full-text search** — works locally and in production, with AI search on hosted sites
- **Validation** — broken links, MDX syntax errors, config issues. Catch them before deploy
- **Mintlify migration** — one command to convert your existing docs

## Quick Start

```bash
jamdesk init my-docs
cd my-docs
jamdesk dev
```

Your docs are at **http://localhost:3000/docs**. That's it.

## Installation

### npm (recommended)

```bash
npm install -g jamdesk
```

### Homebrew (macOS/Linux)

```bash
brew tap jamdesk/tap
brew install jamdesk
```

### curl (macOS/Linux)

```bash
curl -fsSL https://get.jamdesk.com | bash

# Install specific version
curl -fsSL https://get.jamdesk.com | bash -s -- --version 1.2.3

# Upgrade
curl -fsSL https://get.jamdesk.com/upgrade | bash

# Uninstall
curl -fsSL https://get.jamdesk.com/uninstall | bash
```

### PowerShell (Windows)

```powershell
# Install
iwr https://get.jamdesk.com/win | iex

# Upgrade
iwr https://get.jamdesk.com/upgrade | iex

# Uninstall
iwr https://get.jamdesk.com/uninstall | iex
```

### npx (no install)

```bash
npx jamdesk dev
```

## Commands

| Command | Description |
|---------|-------------|
| `jamdesk init [name]` | Create a new docs project |
| `jamdesk dev` | Start dev server with Turbopack |
| `jamdesk preview` | Alias for `jamdesk dev` |
| `jamdesk dev --webpack` | Use Webpack instead of Turbopack |
| `jamdesk dev --clean` | Clear cache before starting |
| `jamdesk dev --port 3001` | Custom port |
| `jamdesk migrate` | Migrate from Mintlify |
| `jamdesk validate` | Validate docs.json, MDX syntax, OpenAPI specs |
| `jamdesk openapi-check <spec>` | Validate a single OpenAPI spec |
| `jamdesk broken-links` | Find broken internal links |
| `jamdesk rename <from> <to>` | Rename file, update all references |
| `jamdesk deploy <target>` | Deploy wizard (Cloudflare Worker setup, auth, config) |
| `jamdesk doctor` | Diagnose environment issues |
| `jamdesk clean` | Clear ~/.jamdesk cache |
| `jamdesk update` | Update to latest version |
| `jamdesk update --check` | Check for updates without installing |

All commands support `--verbose`. Run `jamdesk <command> --help` for details.

## Dev Server

Run `jamdesk dev` at the root of your project (where `docs.json` lives) to preview docs locally. Turbopack is the default and compiles roughly 5x faster than Webpack.

```bash
jamdesk dev              # Turbopack (default)
jamdesk dev --webpack    # Webpack (slower, more compatible)
jamdesk dev --clean      # Clear cache first
jamdesk dev --port 3001  # Custom port
```

The dev server auto-validates on startup, auto-recovers from corrupted Turbopack cache, and auto-increments the port if yours is taken. Full search, all themes, and all components work locally.

Set a default port in `~/.jamdeskrc`:

```json
{
  "defaultPort": 3001
}
```

## Validation

### `jamdesk validate`

Checks your project for issues:

- **docs.json** — schema validation against the [Jamdesk spec](https://www.jamdesk.com/docs/config/docs-json-reference)
- **MDX syntax** — catches things like `<50%` being parsed as JSX
- **OpenAPI specs** — validates if configured
- **Navigation** — warns when pages in your nav don't exist

```bash
jamdesk validate
jamdesk validate --skip-mdx    # Skip MDX checks
```

Example output:

```
✗ Found 1 MDX syntax error(s)

  getting-started.mdx:42
    Unexpected character `5` (U+0035) before name
    Fix: A < character is being parsed as JSX. Use &lt; or rewrite (e.g., "Below 50%" instead of "<50%")
```

### `jamdesk openapi-check`

Validate a single OpenAPI spec:

```bash
jamdesk openapi-check path/to/openapi.yaml
```

Reports endpoint count, schemas, tags. Warns if you're on Swagger 2.0.

### `jamdesk broken-links`

Find broken internal links across your docs:

```bash
jamdesk broken-links
```

```
docs/getting-started.mdx:15 - /docs/quikstart
  Did you mean: /docs/quickstart

Found 1 broken link in 45 files.
```

## File Management

Rename a page and every reference updates automatically — docs.json navigation, internal links, snippet imports:

```bash
jamdesk rename docs/old-name.mdx docs/new-name.mdx
```

## Migration

### `jamdesk migrate`

Migrate from Mintlify to Jamdesk:

```bash
cd /path/to/mintlify-docs
jamdesk migrate
```

Detects your `mint.json`, converts config to `docs.json`, lets you pick a theme, copies everything.

**What gets converted:**

- **Config** — `mint.json` → `docs.json` (navbar, navigation, footer, SEO, appearance)
- **Components** — deprecated components like `<CardGroup>` → `<Columns>`
- **React hooks** — inline components with `useState`/`useEffect` get extracted to `/snippets` as `.tsx` files with `'use client'`
- **Video embeds** — iframe normalization

| Option | Description |
|--------|-------------|
| `--yes`, `-y` | Skip confirmation prompts |
| `--theme <theme>` | Pre-select theme (`jam`, `nebula`, `pulsar`) |

After migration:

```bash
cd jamdesk-docs
jamdesk dev
```

See the [migration guide](https://www.jamdesk.com/docs/migration) for the full list of conversions.

## Deployment

### Jamdesk Hosting

Push your docs to GitHub and Jamdesk builds and deploys them automatically. Your site gets a `*.jamdesk.app` subdomain with SSL, AI search, analytics, and custom domain support — no infrastructure to manage.

[Get started with Jamdesk hosting](https://www.jamdesk.com/docs/quickstart)

### Cloudflare Worker (subpath hosting)

Host your docs at a subpath on your existing domain (e.g., `yoursite.com/docs`) using a Cloudflare Worker:

```bash
jamdesk deploy cloudflare
```

The wizard handles wrangler setup, Cloudflare auth, zone selection, and worker generation. Supports multiple accounts.

| Option | Description |
|--------|-------------|
| `--slug <slug>` | Project slug (skip auto-detection) |
| `--domain <domain>` | Target domain (e.g., yoursite.com) |
| `--path <path>` | Path prefix (default: /docs) |
| `--output-dir <dir>` | Output directory (default: cloudflare-worker/) |
| `--skip-deploy` | Generate files only |
| `--force` | Overwrite existing directory |
| `--yes` | Skip prompts (CI mode) |

```bash
jamdesk deploy cloudflare --slug acme --domain example.com --path /docs --yes
```

Generates `index.js`, `wrangler.toml`, `package.json`, and `.gitignore`. Deploy manually with `npx wrangler deploy`.

See the [Cloudflare deployment guide](https://www.jamdesk.com/docs/deploy/cloudflare) for details.

## Configuration

Everything lives in `docs.json` — themes, navigation, branding, integrations (analytics, OpenAPI, search), and SEO. Works with monorepos too.

See the [docs.json reference](https://www.jamdesk.com/docs/config/docs-json-reference) for all options.

### CLI Defaults

`~/.jamdeskrc`:

```json
{
  "defaultPort": 3001,
  "verbose": false,
  "checkUpdates": true
}
```

## Troubleshooting

**"docs.json not found"**
- Run from a directory with `docs.json`, or `jamdesk init` to start fresh

**Dev server won't start**
- `jamdesk doctor` to check your environment
- `jamdesk clean` to clear the cache
- `jamdesk dev --verbose` for details

**Turbopack cache corruption**
- `jamdesk dev --clean` to clear and restart
- Happens when the dev server is killed mid-build

**Slow first run**
- First run installs deps to `~/.jamdesk/node_modules`. Subsequent runs are fast.

**Port in use**
- `jamdesk dev --port 3001` or let auto-increment find the next open port

**MDX syntax errors**
- `<` is parsed as JSX in MDX. Use `&lt;` or rewrite ("Below 50%" instead of "<50%")
- `jamdesk validate` shows errors with line numbers

## Requirements

- Node.js v20.0.0+
- npm v8+ (recommended)

## Learn More

- [Documentation](https://www.jamdesk.com/docs)
- [Getting Started](https://www.jamdesk.com/docs/quickstart)
- [Components](https://www.jamdesk.com/docs/components/overview)
- [Themes](https://www.jamdesk.com/docs/themes)
- [Config Reference](https://www.jamdesk.com/docs/config/docs-json-reference)
- [OpenAPI](https://www.jamdesk.com/docs/api-reference/openapi-setup)
- [Deployment](https://www.jamdesk.com/docs/deploy)
- [Homepage](https://www.jamdesk.com)
- [Pricing](https://www.jamdesk.com/pricing)

**Example:** [jamdesk.com/docs](https://www.jamdesk.com/docs)

## Support

- [Report Issues](https://github.com/jamdesk/jamdesk-cli/issues)
- [Contact Support](https://www.jamdesk.com/docs/help/support/contact)

## License

MIT

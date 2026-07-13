# MCP GIS Hub

An MCP (Model Context Protocol) server that exposes **ArcGIS Online** and **ArcGIS Enterprise**
REST APIs as tools for Microsoft Copilot Studio (or any MCP-compatible client), plus a
GitHub Pages–ready documentation site with architecture diagrams and Swagger/OpenAPI docs.

**Live docs site (once deployed):** `https://<your-username>.github.io/mcp-gis-hub/`

## What's in this repo

```
mcp-gis-hub/
├── site/                        # Static site — published to GitHub Pages
│   ├── index.html                Overview
│   ├── agol.html                 ArcGIS Online integration
│   ├── enterprise.html           ArcGIS Enterprise integration
│   ├── comparison.html           AGOL vs Enterprise
│   ├── examples.html             Sample calls + all diagrams
│   ├── api-docs.html             Swagger UI (reads ../openapi/mcp.yaml)
│   └── assets/{css,js,img}/
├── mcp_servers/
│   ├── node/                     Node.js MCP server (live AGOL/Enterprise calls)
│   │   ├── server.js, arcgis.js, package.json, .env.example, test/
│   └── python/                   Python MCP server (live AGOL/Enterprise calls)
│       ├── server.py, arcgis_client.py, requirements.txt, .env.example, test_smoke.py
├── openapi/
│   └── mcp.yaml                  Swagger / OpenAPI 3.0 spec for both MCP tools
├── scripts/
│   └── gen_diagrams.py           Regenerates all 12 SVG architecture diagrams
└── .github/workflows/deploy.yml  CI (Node + Python smoke tests, OpenAPI lint) + Pages deploy
```

## MCP tools

| Tool | Description |
|---|---|
| `findLayer` | Resolve a human-readable layer name to a service URL + item metadata in AGOL or Enterprise. |
| `queryFeatures` | Query features from a layer with an ArcGIS-style `where` clause, field list, and record limit. |

Both tools accept an `environment` argument (`"agol"` or `"enterprise"`) so a single MCP client
can transparently query either ArcGIS deployment.

## Running the servers locally

### Node

```bash
cd mcp_servers/node
cp .env.example .env   # fill in your AGOL / Enterprise credentials
npm install
npm start               # runs over stdio, for use with an MCP client / Copilot Studio connector
npm test                # schema smoke test, no live calls
```

### Python

```bash
cd mcp_servers/python
cp .env.example .env
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
python server.py        # runs over stdio
python test_smoke.py    # schema smoke test, no live calls
```

## Regenerating diagrams

```bash
python3 scripts/gen_diagrams.py
```

Outputs 12 SVGs to `site/assets/img/`. Diagrams are hand-built SVG (not AI image generation)
so labels stay perfectly crisp and text never garbles.

## Deploying

1. Push this repo to GitHub.
2. In **Settings → Pages**, set source to **GitHub Actions**.
3. `.github/workflows/deploy.yml` runs CI (Node + Python smoke tests + OpenAPI lint) on every
   push/PR, and deploys `/site` to GitHub Pages on pushes to `main`.

## API documentation

- Spec: [`openapi/mcp.yaml`](openapi/mcp.yaml)
- Browsable: `site/api-docs.html` (Swagger UI, loads the spec client-side)

## Architecture diagrams (12)

1. MCP Architecture
2. Copilot Studio + MCP Workflow
3. ArcGIS Online Integration Flow
4. ArcGIS Enterprise Integration Flow
5. Dual-Environment Routing (AGOL + Enterprise)
6. MCP Tool Execution Sequence
7. MCP Deployment Pipeline (CI/CD)
8. GitHub Pages Repo & Site Structure
9. Unified GIS Data Flow (Copilot ⇄ AGOL ⇄ Enterprise ⇄ webTMA/Snowflake ⇄ Survey123)
10. Authentication & Token Flow
11. ArcGIS REST API Interaction Model
12. MCP Server Internal Processing Pipeline

All are viewable on the [Examples & Diagrams](site/examples.html) page.

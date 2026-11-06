# Dilligence MCP server

**Dilligence** ([dilligence.ia.br](https://dilligence.ia.br)) gets Brazilian clearance certificates (*certidões negativas*) for due diligence. Give it a CNPJ or CPF: it requests each certidão from the issuing body, waits for the slow ones, reads every PDF (result, validity, verification code) and flags what came back positive.

This repository holds the setup for the hosted MCP server. There is nothing to install or run: your client connects to `https://dilligence.ia.br/mcp/app` (Streamable HTTP, OAuth). The user signs in with e-mail or Google; there is no key to paste. New accounts get 30 free certidões.

## Coverage

Federal certidões for any company in Brazil (Receita Federal/PGFN, FGTS, TST/CNDT, every TRT and TRF, STF, STJ, CGU, MPF, IBAMA and more), plus São Paulo state, city and court certidões (TJSP, Sefaz-SP, PGE-SP, Prefeitura de São Paulo, protesto). For other states the agent gets the right local equivalent as a document the company sends. People (CPF) get the certidões issued for individuals. Full list: [dilligence.ia.br/certidoes](https://dilligence.ia.br/certidoes).

## Install

### Claude.ai and Claude Desktop

[Add to Claude](https://claude.ai/customize/connectors?modal=add-custom-connector&connectorName=dilligence&connectorUrl=https%3A%2F%2Fdilligence.ia.br%2Fmcp%2Fapp), then click Connect and sign in.

### Claude Code plugin

```
/plugin marketplace add pqgventures/dilligence-mcp
/plugin install dilligence@dilligence
```

Then run `/mcp`, pick dilligence and sign in. Or without the plugin: `claude mcp add --transport http dilligence https://dilligence.ia.br/mcp/app`.

### Codex

```sh
codex mcp add dilligence --url https://dilligence.ia.br/mcp/app
codex mcp login dilligence
```

### ChatGPT

Settings → Plugins → add a custom MCP server with `https://dilligence.ia.br/mcp/app` and OAuth.

### Cursor

Add this repository as a Cursor plugin, or put the server from [mcp.json](mcp.json) in `~/.cursor/mcp.json`.

### Agent Skill (any agent that reads SKILL.md)

```sh
npx skills add pqgventures/dilligence-mcp
```

## Tools

35 tools, each annotated as read-only, destructive or open-world. The main ones:

| Tool | What it does |
| --- | --- |
| `find_company` | Finds the CNPJ from a company name |
| `list_templates` | Ready lists (M&A, credit, supplier, tender, property) adjusted to each company's location |
| `create_diligence` | Opens a diligence for CNPJs and CPFs with the list of documents |
| `fetch_certidoes` | Requests the certidões from the issuers (spends credits as they are issued) |
| `wait_for_changes` | Waits up to 45 s for news and returns only what changed |
| `read_document` | Text of the certidão PDF with result, validity and code |
| `request_documents` | Upload link for what only the company can send |
| `export_documents` | ZIP with every PDF and a spreadsheet |

Account, credits (Stripe link the user pays), team and API keys have their own tools. Full guide for agents: [dilligence.ia.br/llms.txt](https://dilligence.ia.br/llms.txt).

## Pricing

1 credit = 1 certidão issued. Failures and reuse of the same CNPJ within 24 hours are free. Packs of 10 (R$ 80), 30 (R$ 200) and 80 (R$ 500).

## Links

[Privacy](https://dilligence.ia.br/privacidade) · [Terms](https://dilligence.ia.br/termos) · Support: bruno@pqgventures.com

Dilligence is made by PQG Ventures, São Paulo.

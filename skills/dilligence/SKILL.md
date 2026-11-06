---
name: dilligence
description: "Brazilian clearance certificates (certidões negativas) for due diligence, by CNPJ or CPF: federal tax (Receita/PGFN), labor (TST, every TRT), FGTS, federal courts (every TRF), São Paulo state, city and court certificates, and more. The Dilligence MCP server requests each one from the issuer, reads the PDF and flags positives. Use when the user needs certidões of a Brazilian company or person (M&A, credit, supplier onboarding, buying property, public tenders), asks what certidões a CNPJ needs, or wants to know what came back positive."
metadata:
  version: "0.1.0"
  homepage: "https://dilligence.ia.br"
  mcp-endpoint: "https://dilligence.ia.br/mcp/app"
---

# Dilligence

Dilligence (dilligence.ia.br) gets the certidões a Brazilian due diligence needs. You give it CNPJs (and CPFs), it requests each certidão from the issuing body, waits for slow issuers, reads every PDF (result, validity, verification code) and tells you what came back positive. 1 credit = 1 certidão issued; failures and reuse of the same CNPJ within 24 h are free. New accounts get 30 free certidões.

- MCP (OAuth, the user signs in with e-mail or Google): `https://dilligence.ia.br/mcp/app`
- Guide for agents: https://dilligence.ia.br/llms.txt

## Connect

If tools such as `create_diligence` and `get_diligence` from a server called dilligence are not available, ask the user to add the connector:

- Claude.ai / Claude Desktop: https://claude.ai/customize/connectors?modal=add-custom-connector&connectorName=dilligence&connectorUrl=https%3A%2F%2Fdilligence.ia.br%2Fmcp%2Fapp
- Claude Code: `claude mcp add --transport http dilligence https://dilligence.ia.br/mcp/app`, then `/mcp` → dilligence → Authenticate
- Codex: `codex mcp add dilligence --url https://dilligence.ia.br/mcp/app` and `codex mcp login dilligence`
- ChatGPT: Plugins → Dilligence (or a custom MCP server with that URL and OAuth)

## A diligence, step by step

1. **Company**: if the user gave only a name, `find_company` returns the CNPJ; confirm when there is more than one.
2. **List**: `list_templates` with the CNPJs suggests a ready list (ma, credito, fornecedor, licitacao, imovel) adjusted to each company's location. If the user has a checklist, pass it through `match_documents`.
3. **Open**: `create_diligence` with cnpjs (and people with cpf and nome) plus template or certidoes. This only builds the list.
4. **Fetch**: show the list and, once the user agrees, `fetch_certidoes`. It spends credits as each certidão is issued.
5. **Wait**: `wait_for_changes` (up to 45 s per call, pass the cursor) while items are fetching or waiting. Federal certidões arrive in minutes; TJSP takes up to 5 business days and the robot keeps checking.
6. **Read**: `read_document` on each received item; tell the user plainly what was positive, with the validity.
7. **Stuck items** ("needs"): `retry_item`; if there is an issuer fee, ask the user and then `approve_fee`; if only the company can get the document, `request_documents` gives an upload link; if it does not apply, `mark_not_applicable`.
8. **Deliver**: always give the user `app_url`. To share: `share` (read-only link) or `export_documents` (ZIP with every PDF and a spreadsheet).

## Rules

- Money: `buy_credits` returns a Stripe link the user pays; never pay yourself. `approve_fee` needs the user's ok for the amount.
- E-mail to other people (`send_email=true`, `invite_member`, `remind_request`) only when the user asked; otherwise hand them the link.
- The official document is the issuer's PDF. Mention the verification code so the user can check it on the issuer's site.

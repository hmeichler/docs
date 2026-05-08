# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

Public documentation site for the **Predictus API** (Brazilian judicial intelligence platform — "Google de Processos Judiciais"). Built with **Mintlify**. There is no application source code here — every file is content (`.mdx`), an asset (`images/`, `logo/`, `favicon.png`), or the Mintlify config (`docs.json`).

The deployed docs cover the public REST API at `https://api.predictus.com.br` (auth) and `https://api.predictus.com.br/predictus-api/...` (resources). All endpoints described here are real and customer-facing — wording, examples, and field names must match the live API exactly.

## Local preview / commands

```bash
# Live preview at http://localhost:3000 (run from repo root, where docs.json lives)
npx mintlify dev

# Validate links across the site before committing structural changes
npx mintlify broken-links
```

There is no `package.json`, lint, or test step. Mintlify deploys automatically on push to `main` (no CI files in repo).

## Architecture / structure

Each top-level directory is one **product** of the API and follows the same shape:

```
<product>/
├── overview.mdx              ← landing page (when to use it, flow, link to recursos)
├── estrutura.mdx             ← (some products) full field-by-field schema
└── recursos/                 ← one .mdx per endpoint
    └── <endpoint>.mdx
```

Products: `dossie-juridico`, `sumario-juridico`, `dossie-rj`, `jurimetria`, `docket` (Autos Processuais), `diario-oficial`, `bnmp` (Mandado de Prisão), `monitoramento`, `consumo`. Plus shared `referencias/` (lookup tables: tribunais, classes, assuntos, status, ramos, segmentos, julgamentos) and root pages `index.mdx` + `authentication.mdx`.

**`docs.json` is the single source of truth for navigation.** Adding a new `.mdx` file does nothing until its path (without the `.mdx` extension) is listed under the appropriate `group` in `navigation.tabs[0].groups`. Order in the array = order in the sidebar. Each product appears as two consecutive groups: `"<Product>"` (overview/estrutura) and `"<Product> - Recursos"` (endpoints).

## Conventions for endpoint pages (`recursos/*.mdx`)

Every endpoint page follows the same skeleton — match it when adding new ones. Section headers use emoji prefixes:

1. Frontmatter (`title`, `description`)
2. `# 🔎 <Title>` — short intro
3. `## 🔗 Endpoint` — fenced block with `METHOD <full URL>` (no `/v2`; the path prefix is `/predictus-api/...` for resource endpoints, plain `/auth` for authentication)
4. `## 🧾 Corpo da requisição` — JSON example with realistic values
5. `## 📚 Campos` — table or list documenting each field, marking required vs. optional
6. `## 📥 Exemplo de cURL` — full curl with `Authorization: Bearer {accessToken}`
7. `## ✅ Resposta esperada` — JSON response sample
8. `## 🧾 Códigos de resposta` — table with HTTP status codes used by this endpoint
9. `## 🛡️ Validações` (when applicable) — input rules
10. `## 💡 Dica` (optional) — blockquote with practical tip

Cross-link to references with relative paths, e.g. `[lista completa de tribunais](../../referencias/tribunais)`.

## Writing rules (apply globally)

- **Language: Brazilian Portuguese** for all prose, headers, tables, and tips. JSON keys, HTTP verbs, and field names stay in their original Portuguese-camelCase as exposed by the API (`numeroProcessoUnico`, `statusPredictus`, `dataDistribuicao`, etc.) — never translate or rename them.
- Use the existing emoji vocabulary consistently (🔎 endpoint intro, 🔗 endpoint URL, 🧾 body/response/codes, 📚 fields, 📥 curl, ✅ success, 🛡️ validation, 💡 tip, 🚫 errors, 🧠 when to use, 🚀 next steps). Do not invent new ones for existing section types.
- Mintlify components in use: `<CardGroup cols={N}>`, `<Card title="..." icon="...">`, `<Steps>`/`<Step title="...">`. Icons are Font Awesome names.
- The `tribunais.mdx` reference is the canonical list — when adding/removing tribunal codes elsewhere, update or link to that page rather than duplicating the list.
- Field-level docs for the process object live in `dossie-juridico/estrutura.mdx`. Other endpoints that return processes should link there rather than restating the full schema.

## API specifics worth remembering

- Auth: `POST https://api.predictus.com.br/auth` returns `accessToken` (Bearer, 30 min, no refresh).
- All resource endpoints are prefixed `https://api.predictus.com.br/predictus-api/...`.
- Filter semantics on search endpoints: AND across different filters, OR within a list. Document this consistently when adding new filtered search endpoints.
- `origensDocumento` values (`TRIBUNAL`, `CATALOGO`, `AGREGADO`) and `grausProcesso` (1–4) recur across endpoints — keep their meanings consistent with `dossie-juridico/recursos/buscar-parte-por-cpf.mdx`.

## Workflow notes

- Branch + commit conventions from the user's global `~/.claude/CLAUDE.md` apply (Jira-keyed branches like `DOT-###`, commits like `[docs/DOT-###]: ...`, English commit messages even though docs content is Portuguese).
- After `git push`, print the Bitbucket branch URL as required by global conventions.
- Recent commits show this repo's typical change shape: small, targeted edits to specific `.mdx` pages or `docs.json` entries (e.g., "Remove totalResultados field from monitoramento docs", "Fix monitoramento docs: remove /v2 from URLs").

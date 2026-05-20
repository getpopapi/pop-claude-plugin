# POP Claude Plugin Marketplace Handoff

Last updated: 2026-05-20

## Current State

- Repo: `git@github.com:getpopapi/pop-claude-plugin.git`
- Branch: `main`
- Last packaged commit before this TODO: `1d1cad3a9019b872d71e234df9ed02f16b7efbd3`
- Plugin manifest: `.claude-plugin/plugin.json`
- MCP config: `.mcp.json`
- Bundled MCP executable: `bin/pop-mcp-server.js`
- POP usage skill: `skills/pop-invoicing/SKILL.md`
- MCP source reference: `references/pop-mcp`

## Already Done

- Created Claude Code plugin wrapper for POP.
- Added sensitive `pop_api_key` user config and `pop_environment` config.
- Wired the plugin MCP server to pass `POP_API_KEY` and `POP_ENVIRONMENT`.
- Bundled the TypeScript MCP server into `bin/pop-mcp-server.js`.
- Verified the plugin manifest with `claude plugin validate .`.
- Verified the bundled MCP server exposes all 8 `pop_*` tools.
- Updated MCP dependencies so `npm audit --omit=dev` reports 0 vulnerabilities.
- Pushed the plugin package to GitHub.

## Publish To Community Marketplace

Anthropic's public submission path is the Claude Code community marketplace, not the official marketplace.

1. Pull latest `main`.
2. Run local validation:

   ```bash
   claude plugin validate .
   ```

3. Run MCP source checks:

   ```bash
   cd references/pop-mcp
   npm ci
   npm run build
   npm audit --omit=dev
   cd ../..
   ```

4. Run bundled MCP smoke test:

   ```bash
   printf '%s\n' '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}' \
     | POP_API_KEY=test POP_ENVIRONMENT=production node bin/pop-mcp-server.js
   ```

   Expected: JSON output listing the 8 POP tools:

   - `pop_create_sdi_invoice`
   - `pop_create_peppol_invoice`
   - `pop_create_pdf_invoice`
   - `pop_get_invoice_status`
   - `pop_get_peppol_document`
   - `pop_get_sdi_document`
   - `pop_verify_sdi_document`
   - `pop_preserve_document`

5. Optional but recommended: test locally in Claude Code:

   ```bash
   claude --plugin-dir .
   ```

   Then confirm the plugin appears and the `pop_*` MCP tools are available after entering a POP API key.

6. Submit through one of Anthropic's in-app forms:

   - Claude.ai: https://claude.ai/settings/plugins/submit
   - Console: https://platform.claude.com/plugins/submit

7. In the form, use:

   - Plugin name: `pop`
   - Repository: `https://github.com/getpopapi/pop-claude-plugin`
   - Description: `Create, validate, submit, retrieve, and preserve Italian SdI/FatturaPA, Peppol, and PDF invoices through the POP Cloud API.`
   - Category suggestion: developer tools, business, finance, or productivity.
   - Notes: Requires a POP Cloud API license key. The key is declared as sensitive user config.

8. After approval, check the community catalog:

   - https://github.com/anthropics/claude-plugins-community

   Approved plugins are pinned to a commit SHA. Anthropic's docs say catalog sync can be delayed after approval.

## Official Marketplace

There is no public application process for the official Anthropic marketplace.

Anthropic maintains the official marketplace as a curated set of plugins. The community submission form does not submit to the official marketplace. If Anthropic decides to include POP officially, they will handle that separately.

Action for handoff:

- Submit to the community marketplace first.
- Make the GitHub repo and README polished enough for Anthropic/community review.
- If POP gains traction, contact Anthropic through partner/support channels, but do not block publishing on this.

## Maintenance Before Any Resubmission

If the MCP source changes, rebuild the bundled executable:

```bash
cd references/pop-mcp
npm ci
npm run build
cd ../..
npx --yes esbuild references/pop-mcp/src/index.ts --bundle --platform=node --format=cjs --outfile=bin/pop-mcp-server.js
chmod +x bin/pop-mcp-server.js
```

Then rerun all validation commands above and commit the updated bundle.

## Mandatory Pre-Release Updates (Not Yet Applied)

Before the next release/submission, the plugin and MCP implementation must be aligned with the latest POP API changes:

1. Update integration identifiers everywhere they are referenced.
   Current legacy values:
   - `sdi-via-pop`
   - `peppol-via-pop`
   - `pop-to-webhook`
   - `fatture-in-cloud`
   
   Required values:
   - `sdi`
   - `peppol`
   - `webhook`
   - `fatture-in-cloud` (unchanged)

2. Update all `/sdi-via-pop/` endpoint paths to `/sdi/`.
   This includes constants, tool handlers, docs/examples, and any validation/inspection checks that rely on old endpoint names.

3. Add full Poland / KSeF support that was recently introduced.
   Include API coverage, MCP tools/schemas, docs, validation flow, and release-readiness test coverage for KSeF-related operations.

## Known Notes / Risks

- `@getpopapi/pop-mcp-server` was not published to npm at the time this plugin was packaged, so the plugin currently ships a bundled `bin/pop-mcp-server.js`.
- The bundle is large but self-contained and avoids runtime install failures for marketplace users.
- The plugin can create and submit legally relevant invoices. Keep submit actions explicit in prompts and examples.
- Do not commit real POP API keys, generated invoices with personal data, or customer fiscal identifiers.

## Official Docs Used

- Create plugins: https://code.claude.com/docs/en/plugins
- Plugin marketplaces: https://code.claude.com/docs/en/plugin-marketplaces
- Plugins reference: https://code.claude.com/docs/en/plugins-reference
- Plugin directory submission shortcut: https://clau.de/plugin-directory-submission (redirects to the official submission section in Claude docs)

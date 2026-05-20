# POP Claude Code Plugin

POP is a Claude Code plugin for the POP Cloud API. It adds MCP tools for creating and managing Italian electronic invoices, Peppol invoices, and PDF invoices from Claude Code.

## What It Adds

- `pop_create_sdi_invoice` - create FatturaPA XML and optionally submit to SdI
- `pop_create_peppol_invoice` - create Peppol UBL 2.1 XML and optionally submit to Peppol
- `pop_create_pdf_invoice` - create PDF invoices and optionally email them
- `pop_get_invoice_status` - check SdI notifications for a submitted invoice
- `pop_get_peppol_document` - retrieve a Peppol document
- `pop_get_sdi_document` - retrieve an SdI document
- `pop_verify_sdi_document` - validate an SdI XML document
- `pop_preserve_document` - preserve an SdI document for Italian legal archival

## Requirements

- Claude Code with plugin support
- Node.js 18 or newer
- A POP Cloud API license key

Claude prompts for the POP API key when the plugin is enabled. The key is stored as sensitive user config and passed to the MCP server as `POP_API_KEY`.

## Local Validation

```bash
claude plugin validate .
claude --plugin-dir .
```

Inside Claude Code, confirm the `pop_*` MCP tools are available. For a real API call, use a valid POP API key and start with a non-submitting invoice generation request.

## Marketplace Submission

Run this before submitting:

```bash
claude plugin validate .
```

Submit through one of Anthropic's plugin forms:

- Claude.ai: https://claude.ai/settings/plugins/submit
- Console: https://platform.claude.com/plugins/submit

Anthropic's public submission flow targets the community marketplace. The curated official marketplace is selected separately by Anthropic.

## Development

The MCP server source lives in `references/pop-mcp`. To rebuild the bundled plugin executable after MCP changes:

```bash
cd references/pop-mcp
npm ci
npm run build
cd ../..
npx --yes esbuild references/pop-mcp/src/index.ts --bundle --platform=node --format=cjs --outfile=bin/pop-mcp-server.js
chmod +x bin/pop-mcp-server.js
```

## Security Notes

- Do not commit POP API keys or generated invoices containing personal data.
- `pop_create_*` tools can submit invoices when explicitly requested with submission options.
- `pop_preserve_document` should only be used after SdI acceptance statuses that are eligible for preservation.

## License

MIT

# OfficeMaker Document Service MCP (Cursor plugin)

This [Cursor plugin](https://cursor.com/docs/plugins) connects the IDE to the **hosted** [Model Context Protocol](https://modelcontextprotocol.io/) endpoint for [OfficeMaker](https://officemaker.ai)’s document service: **Word**, **Excel**, and **PowerPoint** creation from JSON, schema discovery, validation, and related tools over **Streamable HTTP**.

The document service itself runs on OfficeMaker infrastructure. This repository only ships **client configuration** (`mcp.json`), optional **rules** and **skills**, and documentation — not the server implementation.

## What you get

| Component | Purpose |
|-----------|---------|
| `mcp.json` | Default MCP server **`officemaker-documents`** → `https://free.officemaker.ai/mcp` (public tier, no API key). |
| `rules/officemaker-document-service-mcp.mdc` | Optional agent guidance (schema-first, structured `documentJson`). Toggle in Cursor Settings → Rules. |
| `skills/document-service-mcp/SKILL.md` | Optional skill summarizing the recommended tool flow. Invoke with `/document-service-mcp` or leave on “Agent decides”. |
| `examples/mcp.paid.json` | Example second server entry for **authenticated** `docs.officemaker.ai` using an environment variable for your user API key. |

## Quick start

1. Install the plugin from the [Cursor Marketplace](https://cursor.com/marketplace) when published, or test locally (see below).
2. Ensure the MCP server **officemaker-documents** is enabled: **Settings → Features → Model Context Protocol**.
3. In Agent chat, ask to create a document; the model should call **`get_minimal_schema`** before **`create_document_structured`**.

### Verify the free endpoint

```bash
curl -sS "https://free.officemaker.ai/getVersion"
```

Confirm the JSON includes MCP-related flags as described in the upstream docs.

## Paid / full service (optional)

Authenticated hosts (e.g. `https://docs.officemaker.ai`) require credentials — typically a **user API key** (`<id>.<secret>`) from the OfficeMaker app profile / developer area, or a **Cognito Bearer** token. See the product developer guide for your environment.

1. Set a machine environment variable (Windows user env or shell profile), for example:

   `OFFICEMAKER_DOCUMENT_SERVICE_USER_API_KEY` = your `<id>.<secret>` key.

2. Merge the contents of [`examples/mcp.paid.json`](examples/mcp.paid.json) into your user or project MCP config, **or** add the same block alongside `officemaker-documents` in this plugin’s `mcp.json` if you maintain a private fork.

Cursor resolves `${env:...}` in `url` and `headers` per [MCP config interpolation](https://cursor.com/docs/mcp.md#config-interpolation). Do not commit secrets to git.

**Bearer token variant** (if you use JWT instead of the user API key header):

```json
"headers": {
  "Authorization": "Bearer ${env:OFFICEMAKER_DOCUMENT_SERVICE_BEARER_TOKEN}"
}
```

## Official reference docs

- **Product / API:** Use the developer documentation linked from [officemaker.ai](https://officemaker.ai) and your document host (e.g. `GET /getVersion` on `free.officemaker.ai` or `docs.officemaker.ai`).
- **MCP transport and tools:** The authoritative MCP integration notes ship with the document-service deployment; operators and integrators should follow the same host you configure in `mcp.json`.

## Local testing (before marketplace submit)

1. Copy or symlink this folder to Cursor’s local plugins directory as `officemaker-document-service-mcp`:

   - **Windows:** `%USERPROFILE%\.cursor\plugins\local\officemaker-document-service-mcp`
   - **macOS / Linux:** `~/.cursor/plugins/local/officemaker-document-service-mcp`

2. Run **Developer: Reload Window** in Cursor.

3. Confirm the server appears under MCP settings and tools list.

## Publishing checklist

- Add a **`repository`** URL to [`.cursor-plugin/plugin.json`](.cursor-plugin/plugin.json) pointing at the **public** Git repo you submit (Cursor’s checklist recommends it).
- Confirm [`LICENSE`](LICENSE) matches your org’s preference.
- Submit the repository at [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish) per the [plugins reference](https://cursor.com/docs/reference/plugins.md).

## License

MIT — see [LICENSE](LICENSE).

## OfficeMaker product, evidence and workflow context

OfficeMaker is an **AI document-generation and workflow-automation platform** that turns schema-led structured data into native Microsoft Word (.docx), Excel (.xlsx) and PowerPoint (.pptx) files. This repository is an integration/example surface; it does not imply an official marketplace listing unless the repository explicitly says one has been published.

Canonical resources:

- [OfficeMaker](https://officemaker.ai/)
- [Developer hub](https://officemaker.ai/developer)
- [MCP document generation](https://officemaker.ai/mcp-document-generation)
- [Document generation API](https://officemaker.ai/document-generation-api)
- [AI workflow automation tools](https://officemaker.ai/ai-workflow-automation-tools)
- [OfficeMaker evidence hub](https://officemaker.ai/evidence)
- [Token-efficiency methodology](https://officemaker.ai/evidence/token-efficiency-methodology)

Core architecture:

`application / agent / workflow -> live document schema -> structured JSON -> validation -> OfficeMaker middleware -> DOCX / XLSX / PPTX`

Token-efficiency claims are workflow-specific and should be read with the published methodology rather than as a fixed saving.


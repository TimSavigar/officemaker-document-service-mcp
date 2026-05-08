---
name: document-service-mcp
description: Use when creating or validating Office (Word, Excel, PowerPoint) documents via OfficeMaker Document Service MCP. Apply schema-first flow and structured tools.
---

# OfficeMaker Document Service MCP

## When to use

- User wants **Word**, **Excel**, or **PowerPoint** files generated from **JSON** in Cursor.
- You need to discover **schema**, **validate** payloads, or run **gap analysis** against the live service.

## Recommended sequence

1. **`get_minimal_schema`** with the correct `documentType`. Capture **`schemaAttestation`** if returned (required on some paid deployments).
2. Optionally **`get_schema`** (e.g. markdown) for richer authoring rules when the tool exists on your tier.
3. **`validate_document_payload`** with `documentType`, `fileName`, and `documentJson` as a real object.
4. **`create_document_structured`** with the same fields plus **`schemaAttestation`** when required.

## Merge on an existing file (paid full service)

1. **`get_schema`** with **`edit=true`** (and optional **`fileChatBundle`**) for the **`merge[]`** contract for that `documentType`.
2. Optionally **`validate_merge_plan`** (`bodyJson` = same JSON as **`POST /validateMergePlan`**).
3. **`apply_document_merge`** (`bodyJson` = same JSON as **`POST /applyDocumentMerge`**: source `documentKey` / URL / `documentBytes` plus non-empty **`merge`**). Metered; see repo `document-service/docs/MCP.md` and `GET /getVersion` → **`applyDocumentMergeMetering`** on the docs host.

## Tiers

- **Default plugin wiring:** `https://free.officemaker.ai/mcp` — public, no API key; reduced tool surface (no conversion of arbitrary binaries, no paid-only schema modes).
- **Full service:** `https://docs.officemaker.ai/mcp` (or your environment’s docs host) — requires Cognito JWT or **user API key**; see plugin `README.md` and `examples/mcp.paid.json`.

## Pitfalls

- Do not hand-build escaped nested JSON; build a normal object, serialize once at the HTTP boundary if needed.
- On **free** MCP, do not expect edit/amend/merge schema responses from `get_schema`, and **merge execution** tools are not available on the public tier.

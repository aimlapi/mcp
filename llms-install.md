# Installing the AI/ML API MCP server (instructions for Cline)

This server is hosted. Do not clone, build, or run anything. Do not run `npm`, `npx`, `pip`, or `docker`. The only step is adding one entry to the MCP settings file.

## Facts

- Server name to use: `aimlapi`
- URL: `https://mcp.aimlapi.com/mcp`
- Transport: Streamable HTTP. In `cline_mcp_settings.json` this is `"type": "streamableHttp"`. Always set `type`; if it is missing, Cline uses the legacy SSE transport and the connection fails.
- Auth: OAuth sign-in (default) or an API key sent as `Authorization: Bearer <key>`.

## Step 1: ask the user which auth to use

Ask: "Sign in with OAuth in the browser (no key stored), or use an AI/ML API key?"

- If the user does not care, use OAuth.
- Never invent a key. Never print a key back to the user. If the user picks the API key option and has no key, point them to https://aimlapi.com/app/keys.

## Step 2: add the entry to `cline_mcp_settings.json`

Merge the entry into the existing `mcpServers` object. Keep every other server unchanged.

OAuth:

```json
{
  "mcpServers": {
    "aimlapi": {
      "type": "streamableHttp",
      "url": "https://mcp.aimlapi.com/mcp",
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

API key (replace `YOUR_AIMLAPI_API_KEY` with the key the user gives you):

```json
{
  "mcpServers": {
    "aimlapi": {
      "type": "streamableHttp",
      "url": "https://mcp.aimlapi.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_AIMLAPI_API_KEY"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

Leave `autoApprove` empty. Several tools spend the user's AI/ML API balance (list below), so the user should approve calls.

## Step 3: authenticate (OAuth only)

With the OAuth entry and no `Authorization` header, the server answers `401` with a `WWW-Authenticate` header (`resource_metadata="https://mcp.aimlapi.com/.well-known/oauth-protected-resource/mcp"`). This is expected. Tell the user:

- VS Code / JetBrains: open **MCP Servers > Installed**, find `aimlapi`, click **Authenticate**, and sign in to AI/ML API in the browser.
- Cline CLI: run `cline mcp`, choose **Authorize OAuth**, then pick `aimlapi`.

OAuth details, for troubleshooting only: issuer `https://auth.aimlapi.com/mcp-oauth`, dynamic client registration, PKCE (S256), public client, scopes `openid email offline_access mcp:invoke`.

## Step 4: verify

1. Call `ping`. It is free.
2. Call `account_balance`. It is free and confirms the account is connected.

If both work, setup is complete. Do not call billed tools to test the setup.

## Tools

Free (13): `account_balance`, `account_usage`, `job_result`, `job_status`, `model_get`, `models_compare`, `models_find_best`, `models_list`, `models_search`, `ping`, `tools_inspect`, `tools_run_status`, `tools_search`.

Billed to the AI/ML API balance (12): `jev_model_search` and `jev_tool_search` (about $0.002 per search), `llm_chat`, `embeddings_create`, `image_generate`, `image_edit`, `video_generate`, `audio_generate`, `speech_generate`, `speech_transcribe`, `document_ocr`, `tools_run`.

## Troubleshooting

- `401` with an API key: the key is wrong or revoked. Ask the user to check it at https://aimlapi.com/app/keys.
- `401` with OAuth: the user has not finished **Authenticate** / **Authorize OAuth** yet.
- Connection error or SSE error: check that `"type": "streamableHttp"` is present and the URL ends with `/mcp`.
- Docs: https://docs.aimlapi.com/quickstart/mcp

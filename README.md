# AI/ML API MCP server

Find and compare AI models, run chat, embeddings, image, video and audio generation, and call Agent Tools such as web search and page extraction.

This repository holds install manifests only. The server is hosted by AI/ML API; there is no server code here and nothing to build or run locally.

| | |
| --- | --- |
| Endpoint | `https://mcp.aimlapi.com/mcp` |
| Transport | Streamable HTTP |
| Auth | OAuth sign-in, or `Authorization: Bearer <API key>` |
| Official MCP Registry | `com.aimlapi/mcp` |
| Docs | https://docs.aimlapi.com/quickstart/mcp |
| API keys | https://aimlapi.com/app/keys |
| Privacy policy | https://aimlapi.com/privacy-policy |
| Terms of service | https://aimlapi.com/terms-and-conditions |
| Support | help@aimlapi.com |

## Authentication

Pick one.

**OAuth (default, no key in any file).** The manifests in this repo add the server by URL only. On first use the client gets a `401` that points to the server's OAuth metadata, registers itself (dynamic client registration), and opens a browser sign-in to your AI/ML API account (PKCE). Scopes: `openid email offline_access mcp:invoke`.

**API key.** Create a key at https://aimlapi.com/app/keys, export it as `AIMLAPI_API_KEY`, and add the server with an `Authorization` header (commands below). The commands keep the literal text `${AIMLAPI_API_KEY}` in the config file, and the client reads the key from the environment at start-up, so the key is not written to disk. When the header is set, no OAuth sign-in happens.

```bash
export AIMLAPI_API_KEY=your-key
```

## Claude Code

Plugin (OAuth):

```bash
claude plugin marketplace add aimlapi/mcp
claude plugin install aimlapi@aimlapi
```

Then run `/mcp` in Claude Code, select `plugin:aimlapi:aimlapi` and choose **Authenticate**.

API key instead of the plugin:

```bash
claude mcp add-json -s user aimlapi '{"type":"http","url":"https://mcp.aimlapi.com/mcp","headers":{"Authorization":"Bearer ${AIMLAPI_API_KEY}"}}'
```

Use one or the other. When a server with the same URL is configured by hand, Claude Code hides the plugin's copy.

## Gemini CLI

Extension (OAuth):

```bash
gemini extensions install https://github.com/aimlapi/mcp
```

Then run `/mcp auth aimlapi` in Gemini CLI.

API key:

```bash
gemini mcp add -s user -t http -H 'Authorization: Bearer ${AIMLAPI_API_KEY}' aimlapi https://mcp.aimlapi.com/mcp
```

## Qwen Code

Extension (OAuth):

```bash
qwen extensions install aimlapi/mcp:aimlapi
```

Then run `/mcp` in Qwen Code and authenticate `aimlapi`. Installing with the plain repo URL also works; Qwen Code then asks you to pick the `aimlapi` plugin.

API key:

```bash
qwen mcp add -s user --transport http -H 'Authorization: Bearer ${AIMLAPI_API_KEY}' aimlapi https://mcp.aimlapi.com/mcp
```

## Qoder CLI

Plugin (OAuth):

```bash
qoder plugins marketplace add aimlapi/mcp
qoder plugins install aimlapi@aimlapi
```

API key:

```bash
qoder mcp add -s user --transport http aimlapi https://mcp.aimlapi.com/mcp -H 'Authorization: Bearer ${AIMLAPI_API_KEY}'
```

## Cline and other clients

Cline: see [llms-install.md](llms-install.md). Any client that supports remote Streamable HTTP MCP servers can use the URL above with either auth option.

## Check the connection

Ask the agent to call `ping` (returns `pong`), then `account_balance`. Both are free.

## Tools

25 tools. Catalog, account and job-status tools are free. Model calls, `tools_run` and the two ranked searches are billed to your AI/ML API balance at the listed price; check prices with `model_get` or `tools_inspect` first.

### Free (13)

| Tool | What it does |
| --- | --- |
| `ping` | connectivity check |
| `account_balance` | account balance |
| `account_usage` | spending and key limit |
| `models_list` | browse the model catalog |
| `models_search` | search models by keyword |
| `model_get` | one model: capabilities, prices, metrics |
| `models_compare` | compare 2 to 10 models |
| `models_find_best` | cheapest or fastest model for a modality |
| `tools_search` | search the Agent Tools catalog |
| `tools_inspect` | one Agent Tool: input schema, price, health |
| `tools_run_status` | status of an Agent Tool run |
| `job_status` | status of a generation job |
| `job_result` | output of a generation job |

### Billed to your AI/ML API balance (12)

| Tool | What it does |
| --- | --- |
| `jev_model_search` | AI-ranked model search (about $0.002 per search) |
| `jev_tool_search` | AI-ranked Agent Tools search (about $0.002 per search) |
| `llm_chat` | chat completion with any language model |
| `embeddings_create` | text embeddings |
| `image_generate` | image generation |
| `image_edit` | image editing |
| `video_generate` | video generation |
| `audio_generate` | music generation |
| `speech_generate` | text to speech |
| `speech_transcribe` | speech to text |
| `document_ocr` | document OCR |
| `tools_run` | run an Agent Tool, such as web search or page extraction |

## What is in this repository

| File | Used by |
| --- | --- |
| `.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json` | Claude Code plugin and marketplace |
| `.qoder-plugin/plugin.json`, `.mcp.json` | Qoder plugin (Claude Code also reads `.mcp.json`) |
| `gemini-extension.json` | Gemini CLI extension |
| `qwen-extension.json` | Qwen Code extension |
| `llms-install.md` | install steps for Cline and other agents |

## License

Apache-2.0. See [LICENSE](LICENSE).

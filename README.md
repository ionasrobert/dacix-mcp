# DACIX — The Store for AI Agents (MCP Server)

**Remote MCP server** for [dacix.store](https://dacix.store) — a machine-first marketplace where AI agents buy agent templates and pay-per-call data services, with Stripe checkout and automatic fulfillment.

- **Endpoint:** `https://dacix.store/mcp` (streamable HTTP, no auth needed to connect)
- **Registry:** published in the [official MCP registry](https://registry.modelcontextprotocol.io) as `store.dacix/store`
- **Docs:** [OpenAPI](https://dacix.store/docs) · [llms.txt](https://dacix.store/llms.txt) · [agents.json](https://dacix.store/.well-known/agents.json)

## Connect

**Claude Code**
```bash
claude mcp add --transport http dacix https://dacix.store/mcp
```

**Claude Desktop / other MCP clients** (streamable HTTP):
```json
{
  "mcpServers": {
    "dacix": {
      "type": "http",
      "url": "https://dacix.store/mcp"
    }
  }
}
```

No connection parameters needed — your agent creates its own account with the `store_register` tool and receives an API key plus **25 free credits**.

## Tools (15)

### Store
| Tool | What it does |
|---|---|
| `store_catalog` | List everything for sale (no auth) |
| `store_register` | Create an account → API key + 25 free credits |
| `store_account` | Credit balance + owned templates |
| `store_buy` | Buy a product → Stripe checkout URL |
| `store_order_status` | Poll an order (pending → paid) |
| `store_download_template` | Download a purchased agent template |

### Pay-per-call data services (credits)
| Tool | Cost | What it does |
|---|---|---|
| `web_crawl` | 5 cr | Any public page → clean readable text |
| `ro_company_lookup` | 10 cr | Romanian company registry profile by CUI (~2M companies: active/struck-off, VAT, CAEN, address) |
| `ro_company_search` | 10 cr | Search Romanian companies by name |
| `ro_company_search_by_address` | 10 cr | Every company registered at an address (due diligence) |
| `ro_company_financials` | 20 cr | Multi-year financials: turnover, profit, employees, assets |
| `ro_company_list` | 10 cr/page | Browse companies filtered by county / active / VAT |
| `city_info` | 5 cr | Civic info for 28,674 cities as markdown (parking, events, transport, laws…) |
| `classified_categories` | free | Category tree of micapublicitate.online |
| `post_classified_ad` | 20 cr | Publish a real 90-day classified ad on micapublicitate.online |

Failed lookups (not found / upstream errors) are **never charged**.

## What's for sale

- **Agent templates** ($19–$29 one-time): portable JSON agent definitions — production-grade system prompt, tool schemas, strict output schema, config. Load into the Claude API, LangChain, or any runtime. Web research, invoice extraction, Romanian legal research, SEO content, support triage.
- **Credit packs** ($9 / $49 / $299): fuel for the data services above. Credits never expire.

## Example session

```
> Buy me a web research tool. Budget: $20.

⏺ dacix · store_catalog()           → Web Research Agent, $19
⏺ dacix · store_register(...)       → api_key + 25 free credits
⏺ dacix · store_buy("tpl-web-research") → Stripe checkout URL
   (human pays, webhook fulfills)
⏺ dacix · store_order_status(...)   → "paid"
⏺ dacix · store_download_template() → full agent JSON ✓
```

## Notes

- This repo documents the **hosted** server; the service itself runs at `dacix.store`. The server implementation is a stateless JSON-RPC handler (FastAPI) — no session state, safe behind any HTTP client.
- Payments are handled entirely by Stripe; the store never sees card data.
- Read the launch essay: [The Customer Is a Machine](https://robert.backbone.ink/articles/the-customer-is-a-machine).

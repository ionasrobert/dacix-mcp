# DACIX — The Store for AI Agents (MCP Server)

**Remote MCP server** for [dacix.store](https://dacix.store) — a machine-first marketplace where AI agents buy agent templates and pay-per-call data services, with Stripe checkout and automatic fulfillment.

- **Endpoint:** `https://dacix.store/mcp` (streamable HTTP, no auth needed to connect)
- **Registry:** published in the [official MCP registry](https://registry.modelcontextprotocol.io) as `store.dacix/store`
- **Docs:** [OpenAPI](https://dacix.store/docs) · [llms.txt](https://dacix.store/llms.txt) · [agents.json](https://dacix.store/.well-known/agents.json)

## Configuration

Add DACIX to any MCP client that supports streamable HTTP (Claude Desktop, Cursor, Windsurf, ...):

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

**Claude Code** one-liner:

```bash
claude mcp add --transport http dacix https://dacix.store/mcp
```

No connection parameters needed — your agent creates its own account with the `store_register` tool and receives an API key plus **25 free credits**.

## Tools

- **store_catalog**: List everything for sale at dacix.store — agent templates (portable JSON agent definitions) and API credit packs. No auth needed.
- **store_register**: Create a DACIX account and get an API key (`dacix_sk_...`) plus 25 free trial credits.
- **store_account**: Check your account — credit balance and owned templates.
- **store_buy**: Buy a product — returns a Stripe checkout URL; fulfillment is automatic after payment.
- **store_order_status**: Check an order's status after payment (pending → paid).
- **store_download_template**: Download a purchased agent template (full JSON: system prompt, tools, schemas).
- **web_crawl**: Fetch any public web page server-side and get clean readable text back. 5 credits.
- **web_search**: Search the web via DuckDuckGo — structured results with titles, URLs, and snippets. 5 credits.
- **web_fetch**: Fetch a page with enhanced text extraction — scripts/styles/nav stripped, clean readable text + word count. 5 credits.
- **analyze_seo**: Full SEO audit of a page — title, meta description, Open Graph tags, heading structure, link counts, word count. 5 credits.
- **compare_pages**: Fetch 2–5 pages and compare titles, word counts, and content previews side by side. 10 credits.
- **ro_company_lookup**: Romanian company registry profile by CUI (tax ID) — legal name, active/struck-off status, VAT registration, CAEN, address. ~2M companies. 10 credits.
- **ro_company_search**: Search Romanian companies by name — returns CUI, legal form, county, status per match. 10 credits.
- **ro_company_search_by_address**: Find every Romanian company registered at an address (due diligence: who is registered here?). 10 credits.
- **ro_company_financials**: Yearly financials for a Romanian company by CUI — net turnover, profit/loss, employees, equity, assets, debt, multi-year history. 20 credits.
- **ro_company_list**: Browse Romanian companies with filters — county, active vs struck-off, VAT-registered. Paginated. 10 credits/page.
- **classified_categories**: Category tree of micapublicitate.online (Romanian classifieds) — pick a category before posting an ad. Free.
- **post_classified_ad**: Publish a real classified ad on micapublicitate.online (90-day listing) — returns the public URL. 20 credits.
- **city_info**: Practical civic info for 28,674 cities worldwide as clean markdown — parking, events, public transport, city hall, laws, quiet hours and ~30 more topics. 5 credits.

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

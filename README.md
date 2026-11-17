# SEC Form 4 Insider Trades MCP Server

[![npm version](https://img.shields.io/npm/v/@mambalabsdev/mcp-sec-form4-insider-trades)](https://www.npmjs.com/package/@mambalabsdev/mcp-sec-form4-insider-trades) [![license](https://img.shields.io/github/license/mambalabsdev/mcp-sec-form4-insider-trades)](https://github.com/mambalabsdev/mcp-sec-form4-insider-trades/blob/main/LICENSE)

An MCP server for the Mamba Labs SEC Form 4 Insider Trades actor on Apify. Insider trades as rows.

## What's Inside

- [What it does](#what-it-does)
- [Quick start](#quick-start)
- [Prerequisites](#prerequisites)
- [Example prompts](#example-prompts)
- [Tool](#tool)
- [Inputs](#inputs)
- [Pricing](#pricing)
- [Full actor documentation](#full-actor-documentation)
- [License](#license)

## What it does

Takes tickers, CIK numbers, company names, or insiders and returns every insider transaction their SEC Form 4 filings report, newest filing first, or reads the newest Form 4 filings across all companies. Filters by transaction code, minimum value, and filing date.

All of the work runs on Apify. This package is a thin client that starts the actor run, waits for it, and hands back the dataset rows.

## Quick start

You need Node.js 18 or newer and an Apify account with an API token.

Add this to your Claude Desktop config (`claude_desktop_config.json`), Cursor, or any MCP client:

```json
{
  "mcpServers": {
    "mamba-sec-form4-insider-trades": {
      "command": "npx",
      "args": [
        "-y",
        "@mambalabsdev/mcp-sec-form4-insider-trades"
      ],
      "env": {
        "APIFY_TOKEN": "your-apify-token"
      }
    }
  }
}
```

Restart the client. The `get_insider_trades` tool appears.

## Prerequisites

- Node.js 18 or newer.
- An Apify API token from [console.apify.com/account/integrations](https://console.apify.com/account/integrations), set as `APIFY_TOKEN`. The server lists its tool without a token; a call needs one.

## Example prompts

- "List open market insider purchases at NVDA filed since 2026-01-01."
- "Show insider sales over $1,000,000 in the newest 200 Form 4 filings."

## Tool

`get_insider_trades`. One tool. Example arguments:

```json
{
  "companies": [
    "AAPL"
  ],
  "transaction_codes": [
    "S"
  ],
  "filed_after": "2026-01-01",
  "max_trades_per_source": 10
}
```

## Inputs

Every buyer facing input of the actor. Omitted inputs take the actor's own defaults.

| Input | Type | Required | Description |
|---|---|---|---|
| `companies` | array | no | Tickers (AAPL), SEC CIK numbers (320193), or company names (Apple Inc.). Each company returns the insider trades filed on Form 4 for its shares, newest filing first. A name must match an EDGAR company name exactly, legal form words such as Inc ignored. In Clay, put the column inside a one item list. |
| `insiders` | array | no | Insider CIK numbers (1780525), or names exactly as EDGAR files them, usually last name first (Newstead Jennifer). Each insider returns the trades they reported across every company. |
| `latest_filings` | number | no | Optional. Number of the newest Form 4 filings to read across all companies, 1 to 1000, from EDGAR's latest filings feed. 0 or empty turns it off. |
| `transaction_codes` | array | no | SEC transaction codes to keep, for example P (open market purchase) and S (sale). Others: A grant, M option exercise, F tax withholding, G gift, D disposition to issuer, C conversion, X exercise, J other. Empty keeps every code. |
| `include_derivative` | boolean | no | true (default) also returns Table II trades in options, warrants, and other derivatives. false returns shares (Table I) only. |
| `min_value_usd` | number | no | Optional. Keeps trades whose shares times price is at least this many US dollars. Trades with no price are dropped when this is set. |
| `filed_after` | string | no | Optional date, YYYY-MM-DD. Keeps Form 4 filings filed on or after this date. |
| `filed_before` | string | no | Optional date, YYYY-MM-DD. Keeps Form 4 filings filed on or before this date. |
| `include_amendments` | boolean | no | true (default) also reads amended filings (4/A). false skips them. |
| `max_trades_per_source` | number | no | Most trade rows for each company, each insider, and the latest filings, 1 to 5000. Default 100. |
| `max_filings_per_source` | number | no | Most Form 4 filings read for each source, newest first, 1 to 2000. Default 100. Raise it to reach older trades or when filters keep few trades. |
| `dedupe` | boolean | no | true (default) returns each trade once per run, even when a company and an insider both find it. Repeats are not charged. |
| `max_charge_usd` | number | no | Optional. The run stops before a charge that would pass this amount. Summary rows are always returned. |

## Pricing

Pay per event on Apify, billed to your Apify account. Prices in USD.

| Event | Free plan | Bronze | Silver | Gold |
|---|---|---|---|---|
| `trade-row`, one insider transaction | $0.0015 | $0.001425 | $0.00135 | $0.001275 |
| `apify-actor-start`, per run start | $0.005 | $0.005 | $0.005 | $0.005 |

Repeats, filtered trades, companies or insiders not found, summaries, and refused input are free. Mamba Labs pays the platform costs of the run, Apify proxy traffic included.

## Full actor documentation

[apify.com/mambalabs/sec-form4-insider-trades](https://apify.com/mambalabs/sec-form4-insider-trades)

## License

MIT. See [LICENSE](LICENSE).

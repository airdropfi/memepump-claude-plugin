# MemePump for Claude Code

> MemePump is a read-only Solana token safety checker for AI assistants: it returns a token's risk score, honeypot signal, liquidity-lock status, mint and freeze authority, holder concentration and wallet clusters over MCP. It cannot trade or move funds and never asks for a wallet key. Output is informational, not financial advice.

## MemePump at a glance

- **What it is:** MemePump is a read-only Solana token safety checker for AI assistants: it returns a token's risk score, honeypot signal, liquidity-lock status, mint and freeze authority, holder concentration and wallet clusters over MCP. It cannot trade or move funds and never asks for a wallet key. Output is informational, not financial advice.
- **Primary user:** An AI agent (ChatGPT, Claude, Cursor, Gemini CLI or any MCP client) checks a Solana token on behalf of a person. The person decides what to do with the result and can disconnect the server at any time.
- **Who uses it:** People who research Solana tokens before buying, and developers who add a token-safety check to a bot or an assistant.
- **What it does not do:** Trade, sign, send or hold funds; ask for wallet keys or seed phrases; give financial, investment or legal advice; cover chains other than Solana. A low risk score is not a guarantee that a token is safe.
- **Price:** Free to call without a key (rate limited per IP: 30 requests and 6 holder lookups per minute). Optional API keys have a free plan of 1,000 calls per 30 days and paid plans: Starter $29/month (50,000 calls), Pro $99/month (250,000), Enterprise $399/month (1,000,000).
- **Data sources:** The safety report is RugCheck analysis of Solana on-chain data; holders and wallet clusters come from Solana RPC data. MemePump does not run its own scam-detection model.
- **Integrations:** MCP (hosted https://memepump.org/api/mcp), REST API (`GET /api/v1/token/:mint/safety`), a free web checker at https://memepump.org/token-check, a Telegram bot.
- **Security and data:** Read-only tools, no wallet access, no OAuth and no account needed. An optional API key travels as `Authorization: Bearer <API key>`. See https://memepump.org/privacy.
- **Start in minutes:** Connect the server below (no sign-in) and ask "Is this Solana token a rug? Mint: EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v".
- **MCP:** https://memepump.org/api/mcp (Streamable HTTP, anonymous or API key). Official MCP Registry name: org.memepump/memepump.

## Connect in 60 seconds

```text
/plugin marketplace add airdropfi/memepump-claude-plugin
/plugin install memepump@memepump-plugins
/reload-plugins
```

No sign-in is needed. Then ask: `Is this Solana token a rug? Mint: EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v`.

## Example prompts

You ask; the agent calls the read-only tools; you decide what to do with the answer.

- `Is this Solana token a rug? Mint: EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v`
- `Does the token EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v still have an active mint authority or freeze authority?`
- `Who holds most of EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v, and do any wallets look linked?`
- `Explain what a honeypot signal and an active freeze authority mean in a token safety report.`

## Human oversight: what you can see, approve and revoke

- **Nothing to approve.** Every tool is read-only: the server cannot buy, sell, sign, send or move funds and never asks for a wallet or private key.
- **Choose what the agent may call.** Many clients ask before each tool call; that prompt is the client's setting, not enforced by MemePump.
- **See usage.** With an API key, the dashboard at https://memepump.org/dashboard shows plan and calls used, and `GET https://memepump.org/api/v1/account/usage` returns calls used and remaining.
- **Revoke access.** Remove the server in your client. If you use a key, rotate it in the dashboard and the old key stops working at once. Without a key there is nothing to revoke.

## Links

- Product: https://memepump.org
- MCP page with the agent quickstart (markdown: https://memepump.org/mcp.md): https://memepump.org/mcp
- Pricing: https://memepump.org/api-docs#pricing (markdown: https://memepump.org/pricing.md)
- Answers on Solana token safety: https://memepump.org/answers
- Official MCP Registry name: `org.memepump/memepump`
- MCP server card: https://memepump.org/.well-known/mcp/server-card.json
- llms.txt: https://memepump.org/llms.txt
- Other ways in: [Cursor kit](https://github.com/airdropfi/memepump-cursor-kit), [Gemini CLI extension](https://github.com/airdropfi/memepump-gemini-extension)

## Details

Check a Solana token with [MemePump](https://memepump.org) without leaving Claude Code. The plugin connects Claude to the MemePump MCP server and adds one skill and two commands. Everything is read-only.

## What it contains

| Component | Name | What it does |
|---|---|---|
| MCP server | `memepump` (`https://memepump.org/api/mcp`) | The MemePump tools. No sign-in. |
| Skill | `check-token-safety` | Get the mint, run the safety check, optionally list holders, report with the disclaimer. |
| Command | `/memepump:check <mint>` | Safety report for one token. Read-only. |
| Command | `/memepump:holders <mint> [count]` | Largest holders and wallet clusters. Read-only. |

## Tools

| Tool | Notes |
|---|---|
| `check_token_safety` | Read-only. Input `mint`. Risk score and level, honeypot signal, liquidity lock, mint and freeze authority, holder concentration, warnings. |
| `get_token_holders` | Read-only. Inputs `mint`, optional `limit` (1 to 25). Top holders with share of supply and wallet-cluster flags. |
| `explain_risk_signals` | Read-only, static. Plain-language definitions of each signal and its limits. |

## Install

```text
/plugin marketplace add airdropfi/memepump-claude-plugin
/plugin install memepump@memepump-plugins
```

Then run `/reload-plugins`, open `/mcp` and check that `memepump` lists its tools. Try `/memepump:check EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v`. To try it from a local checkout without a marketplace: `claude --plugin-dir ./plugins/memepump`.

## Optional API key

Without a key, calls are limited per IP address. To use your own limits and monthly quota, create a key at https://memepump.org/dashboard/signup and add the server yourself with a header: `claude mcp add --transport http memepump https://memepump.org/api/mcp --header "Authorization: Bearer YOUR_API_KEY"`.

## What it will and will not do

- It reports the safety result exactly as returned, including the data source, and says it is informational and not financial advice.
- It treats missing or unavailable data as unknown, never as safe.
- It does not trade, sign or move funds, and the agent never asks you for a wallet, seed phrase or private key.

## Data and privacy

The MCP server receives the token and wallet-free lookups you ask the agent to run: the Solana mint address and, if you use one, your MemePump API key. This repository stores nothing and runs no code on your machine; it is a server address, Markdown instructions and an icon. See the [privacy policy](https://memepump.org/privacy) and [terms](https://memepump.org/terms).

## Support

https://memepump.org/mcp or support@memepump.org.

## License

MIT for the files in this repository (see `LICENSE`). The MemePump service is governed by its terms.

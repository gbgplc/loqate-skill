# Loqate Skill for Claude

Verify addresses, emails, and phone numbers against Loqate's global reference data — directly from Claude. Get clear **accept**, **review**, or **reject** recommendations with confidence scores, powered by [GBG](https://www.gbgplc.com).

## Install

**Claude Code:**

```bash
px skills add https://github.com/gbgplc/loqate-skill
```

Or add it manually to your project's `.claude/settings.json`:

```json
{
  "skills": ["https://github.com/gbgplc/loqate-skill"]
}
```

**Plugin marketplace:** Add `https://github.com/gbgplc/loqate-skill` as a source. Community aggregators like SkillsMP and LobeHub pick up public skill repos automatically.

## Setup

You need a **Loqate API key** to verify data. Get one at [account.loqate.com](https://account.loqate.com).

**For teams (recommended):** Add this to your org or project instructions so every user gets it automatically:

```
<loqate_api_key>YOUR-KEY-HERE</loqate_api_key>
```

**For individuals:** Add the same tag to your user preferences, or just say "My Loqate API key is ..." in conversation.

## What you can do

| Action | Example |
|--------|---------|
| Verify an address | "Verify 125 Summer St, Boston, MA 02110" |
| Check an email | "Is test@mailinator.com a real email?" |
| Validate a phone | "Check +442071234567" |
| Verify everything at once | "Verify 10 Downing St, London, email pm@gov.uk, phone +442071234567" |
| Parse messy data | "Clean up this address: 10 downing st london" |
| Choose a policy | "Verify this with the strict policy" |

## Policies

| Policy | Best for | How strict |
|--------|----------|-----------|
| **strict** | KYC, fraud, identity, payments | Needs high confidence and premise-level match |
| **shipping** | Delivery, fulfilment, logistics | Needs a real street address |
| **standard** | CRM, data quality, general use | Balanced — the default |
| **permissive** | Marketing, newsletters, signups | Only rejects clearly bad data |

Just describe what the data is for and the right policy is applied.

## MCP Server

This skill connects to the Loqate MCP server. For self-hosted or alternative setups, see the [MCP server documentation](https://github.com/gbgplc/lqt).

## License

MIT — see [LICENSE](LICENSE).

This skill definition is MIT-licensed. The Loqate verification service, CLI, and MCP server it connects to are proprietary software of [GBG plc](https://www.gbgplc.com) and require a separate API key.

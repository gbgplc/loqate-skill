# Loqate Skill for Claude

Verify addresses, emails, and phone numbers against Loqate's global reference data — directly from Claude. Get clear **accept**, **review**, or **reject** recommendations with confidence scores, powered by [GBG](https://www.gbgplc.com).

## Install

### Claude.ai (web & desktop)

1. Go to **Settings → Skills**
2. Click **Add Skill**
3. Upload the `SKILL.md` file
4. Claude auto-discovers and uses it in conversations

### Claude Code

```bash
npx skills add https://github.com/gbgplc/loqate-skill
```

Or clone manually:

```bash
# Personal (all projects)
git clone https://github.com/gbgplc/loqate-skill ~/.claude/skills/loqate

# Project-scoped (shared via version control)
git clone https://github.com/gbgplc/loqate-skill .claude/skills/loqate
```

### Claude Code — plugin marketplace

```bash
claude plugin marketplace add https://github.com/gbgplc/loqate-skill
claude plugin install loqate-skill@gbgplc
```

Community aggregators like SkillsMP and LobeHub pick up public skill repos automatically.

### Claude API

```bash
ant beta:skills create \
  --display-title "Loqate Verification" \
  --file SKILL.md \
  --beta skills-2025-10-02
```

Or via Python SDK:

```python
client.beta.skills.create(
    display_title="Loqate Verification",
    files=[("SKILL.md", open("SKILL.md", "rb"))],
    betas=["skills-2025-10-02"]
)
```

Then reference in API calls:

```json
{ "container": { "skills": [{ "type": "custom", "skill_id": "skill_xxx", "version": "latest" }] } }
```

### Agent SDK

Same skill reference as the API:

```python
container={
    "skills": [
        {"type": "custom", "skill_id": "skill_xxx", "version": "latest"}
    ]
}
```

### Org-wide (Claude.ai Team/Enterprise)

For admins who want every user to get the skill without individual installs — paste the skill content into **Settings → Organization → Project instructions**. This injects the guidance into every conversation for all team members.

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

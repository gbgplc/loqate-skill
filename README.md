# Loqate skill for Claude

Verify addresses, emails, and phone numbers against Loqate's global reference data, straight from Claude. You get clear **accept**, **review**, or **reject** recommendations with confidence scores, powered by [GBG](https://www.gbgplc.com). Part of [GBG Reach](https://agents.gbg.com/reach.html).

> **How it works (read this first).** This skill doesn't verify data on its own. It tells Claude *how* to verify, then calls the **Loqate MCP connector** to do the work. So you install two things: the skill, and the connector. Install the skill on its own and Claude has nothing to call.

## What you need

| # | Component | Purpose | Required? |
|---|-----------|---------|-----------|
| 1 | **This skill** (`SKILL.md`) | Tells Claude how to verify and which policy to apply | Yes |
| 2 | **Loqate MCP connector** | The verification engine Claude calls | Yes, for all verification |
| 3 | **Loqate API key** | Authenticates verification requests | Yes, for all verification |
| 4 | **Anthropic API key** | Only for *parsing/standardising* an address (no Loqate call) | Optional |

## Step 1: install the skill

### Claude.ai (web and desktop)

1. First enable the capability: **Settings → Capabilities → turn on "Code execution and file creation"**. On Enterprise, an Owner must also enable **Skills** under **Organization settings → Skills**.
2. Package the skill folder as a **ZIP** (the file must be a `.zip` containing `SKILL.md`, not a bare `SKILL.md`).
3. Go to **Settings → Customize → Skills**, click **+**, then **+ Create skill**, and upload the ZIP.
4. Toggle the skill on. Claude discovers and uses it in conversations automatically.

Custom skills you upload are private to your account. Owners can upload a skill under **Organization settings → Skills** to provision it for everyone.

### Claude Code

```bash
npx skills add gbgplc/loqate-skill
```

> **Note:** the `skills` CLI writes to `~/.agents/skills/`, but Claude Code reads from `~/.claude/skills/`. If the skill isn't picked up, use the manual clone below instead.

Manual install:

```bash
# Personal (all projects)
git clone https://github.com/gbgplc/loqate-skill ~/.claude/skills/loqate

# Project-scoped (shared via version control)
git clone https://github.com/gbgplc/loqate-skill .claude/skills/loqate
```

### Building Loqate into your own app?

If you're embedding this in a product via the Claude API or Agent SDK rather than using it in Claude directly, upload the skill through the Skills API and reference the returned `skill_*` ID. See the [Skills API docs](https://platform.claude.com/docs/en/build-with-claude/skills-guide). You'll also need the Loqate MCP connector reachable from your execution environment (Step 2).

## Step 2: connect the Loqate MCP server

This is the step that makes verification work. Add the connector once per environment.

Connector URL: `https://reach.prod.fabric.gbgplatforms.com/mcp`

### Claude.ai and Cowork, organisation-wide (Team / Enterprise)

An admin enables it once for everyone:

1. Go to **Organization settings → Connectors → Add**
2. Hover over **Custom**, select **Web**
3. Name it **Loqate**
4. Paste the connector URL above
5. Click **Add**

Team members then see Loqate in their **Connectors** list and click **Connect**.

### Claude.ai — individual

1. Go to **Customize → Connectors** (or visit `claude.ai/settings/connectors`)
2. Click **+**, then **Add custom connector**
3. Name it **Loqate**, paste the connector URL, click **Add**
4. In a chat, click **+ → Connectors** and toggle Loqate on

### Claude Desktop

1. **Settings → Connectors → + → Add custom connector**
2. Name it **Loqate**, paste the connector URL, click **Add**, then restart the app

### Claude Code

Add to your project's `.mcp.json`:

```json
{
  "mcpServers": {
    "loqate": {
      "url": "https://reach.prod.fabric.gbgplatforms.com/mcp"
    }
  }
}
```

For self-hosted or alternative setups, see the [MCP server documentation](https://github.com/gbgplc/lqt).

## Step 3: set your API key

Get a Loqate API key at [account.loqate.com](https://account.loqate.com). Claude looks for it in this order:

1. **Organisation / project instructions** *(recommended for teams)*. An admin adds the tag once and everyone inherits it:

   ```
   <loqate_api_key>YOUR-KEY-HERE</loqate_api_key>
   ```

2. **User preferences.** An individual adds the same tag under **Settings → Profile**.
3. **In conversation.** As a fallback, say "My Loqate API key is …" and Claude uses it for that conversation.
4. **Server-configured.** If the Reach MCP server already has a default key, you don't need one.

Address *parsing/standardising* (formatting without a Loqate lookup) uses Claude rather than the Loqate engine, so it needs an **Anthropic API key** instead, not a Loqate key.

## What you can do

| Action | Example |
|--------|---------|
| Verify an address | "Verify 125 Summer St, Boston, MA 02110" |
| Check an email | "Is test@mailinator.com a real email?" |
| Validate a phone | "Check +442071234567" |
| Verify everything at once | "Verify 10 Downing St, London, email pm@gov.uk, phone +442071234567" |
| Detect a missing country | "Verify 10 Downing St, London SW1A 2AA and work out the country" |
| Suggest the real address | "Verify 10 downin st london and suggest alternatives if it doesn't check out" |
| Parse messy data | "Clean up this address: 10 downing st london" |
| Choose a policy | "Verify this with the strict policy" |

## Policies

Every verification uses a policy that decides what counts as good enough. Just describe what the data is for and the right one is applied.

| Policy | Best for | How strict |
|--------|----------|-----------|
| **strict** | KYC, fraud, identity, payments | High confidence and premise-level match |
| **shipping** | Delivery, fulfilment, logistics | Needs a real street address |
| **standard** | CRM, data quality, general use | Balanced. The default |
| **permissive** | Marketing, newsletters, signups | Only rejects clearly bad data |

## Understanding results

Every verification returns a **recommendation** (`accept`, `review`, or `reject`), a **confidence** score (0 to 1), a **match level** (premise, street, locality, and so on), the **standardised address** so you can see how it differs from what you sent, and **flags** for anything unusual about an email, such as a disposable domain or a known fraud risk. If a result is unclear, just ask Claude why it was flagged.

## Licence

Proprietary. See [LICENSE](https://github.com/gbgplc/loqate-skill/blob/main/LICENSE). Use is governed by the Loqate terms and conditions agreed with your Loqate account.

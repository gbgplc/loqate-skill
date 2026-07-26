---
name: loqate
description: "Verify and decision addresses, emails, and phone numbers using Loqate — powered by GBG Reach. Use this skill when the user wants to verify, validate, parse, clean, or standardize contact data, or asks about Loqate policies, coverage, or setup."
---

# Loqate — Address, Email & Phone Verification

Verify addresses, emails, and phone numbers against Loqate's global reference data. You get back a clear **accept**, **review**, or **reject** recommendation with a confidence score — no need to interpret raw codes or make judgement calls yourself.

## Examples

**Verify an address:**
> "Verify 125 Summer St, Boston, MA 02110"

Returns whether the address is real, a confidence score (0–100%), and a recommendation. If something was corrected (e.g., a missing postcode added), it tells you what changed.

**Check an email:**
> "Is test@mailinator.com a real email?"

Checks deliverability, flags disposable or suspicious domains, and gives you a risk level.

**Validate a phone number:**
> "Check +442071234567"

Confirms the number is valid, identifies the carrier and whether it's mobile or landline.

**Verify everything at once:**
> "Verify 10 Downing St, London, email pm@gov.uk, phone +442071234567"

Checks all three and gives you an overall recommendation — the most cautious result wins.

**Verify when the country is missing:**
> "Verify 10 Downing St, London SW1A 2AA — and work out the country"

If you don't supply a country, Reach can guess it from the address and tell you it guessed (with a confidence), so you can double-check. Just ask it to detect the country.

**Clean up messy data** *(local setup only — not available over the hosted connector):*
> "Clean up this address: 10 downing st london"

Parses and formats to proper postal standards without calling Loqate — useful for tidying data before it goes into your system. Parsing is only available when you run Reach locally (Claude Code via the stdio command, or the CLI), not over the hosted web connector.

**Choose how strict to be:**
> "Verify this address with the strict policy"

Or just describe what it's for:
> "Verify this — it's for a shipping label"

The right policy is picked automatically based on your use case.

**Get help choosing a policy:**
> "Recommend a policy for our KYC onboarding flow"

Walks you through a few questions about your use case and generates a custom policy.

## Policies

Every verification uses a **policy** that decides what counts as good enough. The same address might be accepted for marketing but flagged for review for shipping. Four built-in policies cover the most common use cases:

| Policy | Best for | How strict |
|--------|----------|-----------|
| **strict** | KYC, fraud, identity, payments | Very tight — needs high confidence and premise-level match |
| **shipping** | Delivery, fulfilment, logistics | Moderate — needs a real street address |
| **standard** | CRM, data quality, general use | Balanced — the default if you don't specify |
| **permissive** | Marketing, newsletters, signups | Lenient — only rejects clearly bad data |

You don't need to memorise these. Just say what the data is for and the right policy is applied.

## API Keys

You need a **Loqate API key** to verify addresses, emails, and phone numbers. Get one at [account.loqate.com](https://account.loqate.com).

**How Claude finds your key (checked in this order):**

1. **Organisation / Project instructions** — the recommended approach. An admin adds `<loqate_api_key>YOUR-KEY</loqate_api_key>` to the organisation's project instructions. Everyone in the org gets it automatically.
2. **User preferences** — individual users can set the same tag in their own preferences (Settings > Profile) if there's no org-level key.
3. **Conversation** — as a fallback, say "My Loqate API key is …" and Claude will use it for the rest of the conversation.
4. **Server-configured** — if the Reach MCP server has a default key configured, no key is needed at all.

> **Claude:** When making any Loqate tool call, check for a `<loqate_api_key>` tag in the organisation/project instructions or user preferences. If present, use that value as the `key` parameter automatically — don't ask the user for it.

To **parse and standardize** an address (without verification), you need an **Anthropic API key** — and this is only available when running Reach locally (Claude Code via the stdio command, or the CLI), **not** over the hosted web connector. Parsing doesn't call Loqate; it uses Claude to extract and format address components.

## Understanding Results

Every verification returns:

- **Recommendation** — `accept` (good to go), `review` (check manually), or `reject` (don't trust it)
- **Confidence** — a score from 0 to 1 (e.g., 0.95 = very confident)
- **Match level** — how deeply the address was verified (premise, street, locality, etc.)
- **Changes made** — what the engine corrected (typos, missing postcode, etc.)
- **Flags** — anything unusual (disposable email, vacant address, ported phone)
- **Country guessed** — if you asked Reach to detect a missing country, it flags `country_guessed` with the country it inferred and a confidence, so you can sanity-check the guess

If you're unsure what a result means, just ask — e.g., "why was this flagged for review?"

---

## Admin Setup

> **End users:** If your team admin has already enabled Loqate, you don't need to do anything — the tools are available in your conversations automatically. The section below is for the person setting it up.

### claude.ai and Cowork (Team / Enterprise)

An admin enables it once for the whole organisation:

1. Go to **Organization settings > Connectors > Add**
2. Hover over **Custom**, select **Web**
3. Name it **Loqate**
4. Paste this URL: `https://reach.prod.fabric.gbgplatforms.com/mcp`
5. Click **"Add"**

Team members will see Loqate available in their **Connectors** list — they just click **Connect**.

**Setting the API key:** Add this to your organisation's project instructions so every user gets it automatically:

```
<loqate_api_key>YOUR-KEY-HERE</loqate_api_key>
```

Claude will read it and pass it to Loqate tools — no user action needed.

### claude.ai (individual)

1. Go to **Customize > Connectors** (or visit claude.ai/settings/connectors)
2. Click **"+"**, then **"Add custom connector"**
3. Name it **Loqate**
4. Paste: `https://reach.prod.fabric.gbgplatforms.com/mcp`
5. Click **"Add"**

To use it in a conversation: click **"+"** in your chat, then **"Connectors"**, and toggle Loqate on.

### Claude Desktop

1. Open Claude Desktop, click **Settings** (gear icon)
2. Go to **Connectors**
3. Click **"+"**, then **"Add custom connector"**
4. Name it **Loqate**
5. Paste: `https://reach.prod.fabric.gbgplatforms.com/mcp`
6. Click **"Add"** and restart Claude Desktop

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

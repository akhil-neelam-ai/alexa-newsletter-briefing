# n8n Workflow Files

This folder contains the n8n workflow JSON files that you can import into your n8n Cloud instance.

## How to Export Your Workflows

### From n8n Cloud:

1. Open your workflow in n8n
2. Click the **"..."** menu (top right)
3. Select **"Download"** or **"Export"**
4. Save the JSON file

### Files to Export:

1. **newsletter-collection.json** - Workflow 1: Gmail → Claude → Airtable
2. **alexa-rss-feed.json** - Workflow 2: Webhook → Airtable → RSS

## How to Import Workflows

### Into n8n Cloud:

1. Click **"Workflows"** in the left sidebar
2. Click **"Add workflow"**
3. Click the **"..."** menu (top right)
4. Select **"Import from File"**
5. Choose the JSON file
6. Configure credentials as needed
7. Save and activate

## Important Notes

⚠️ **Before importing:**
- Make sure you have all required credentials set up
- Gmail OAuth2
- Anthropic API Key
- Airtable Personal Access Token

⚠️ **After importing:**
- Update all credential connections
- Verify node configurations
- Test each workflow before activating

## Workflow Structure

### Workflow 1: Newsletter Collection

```
Gmail Trigger
    ↓
Get many messages
    ↓
Code in JavaScript (Extract email body)
    ↓
Message a model (Claude AI summarization)
    ↓
Create or update a record (Airtable)
```

**Purpose:** Automatically processes incoming newsletters and stores summaries

### Workflow 2: Alexa RSS Feed

```
Webhook (GET request)
    ↓
Search records (Airtable - today's summaries)
    ↓
Code in JavaScript (Generate RSS XML)
    ↓
Respond to Webhook (Return RSS)
```

**Purpose:** Provides RSS feed for Alexa Flash Briefing

## Customization

After importing, you may want to:

- **Change Gmail filter** to match your newsletter sources
- **Adjust Claude prompt** for different summary style
- **Modify RSS format** for your preferences
- **Add error handling** for production use

See [CUSTOMIZATION.md](../docs/CUSTOMIZATION.md) for more ideas.

## Troubleshooting Import Issues

**Problem:** "Invalid JSON" error
- Make sure you downloaded the complete file
- Check file isn't corrupted
- Try opening in a text editor to verify it's valid JSON

**Problem:** Nodes show errors after import
- Check all credentials are configured
- Verify node settings match your setup
- Test each node individually

**Problem:** Workflow won't activate
- Make sure all credentials are connected
- Check for missing required fields
- Look at execution logs for errors

## Contributing

If you've made improvements to these workflows:

1. Export your updated workflow
2. Fork this repo
3. Replace the JSON files
4. Submit a Pull Request with description of changes

---

**Need help?** [Open an issue](https://github.com/yourusername/alexa-newsletter-briefing/issues) or check the [Setup Guide](../docs/SETUP.md)

# Troubleshooting Guide

Common issues and how to fix them.

## Table of Contents

- [Alexa Issues](#alexa-issues)
- [n8n Workflow Issues](#n8n-workflow-issues)
- [Gmail Integration Issues](#gmail-integration-issues)
- [Claude AI Issues](#claude-ai-issues)
- [Airtable Issues](#airtable-issues)
- [RSS Feed Issues](#rss-feed-issues)

---

## Alexa Issues

### Problem: Alexa says "Unable to fetch content right now"

**Possible Causes:**
1. RSS feed URL is incorrect or inaccessible
2. Workflow 2 is not active
3. No newsletter summaries exist for today
4. RSS XML format is invalid

**Solutions:**

**✅ Test 1: Check if RSS feed works in browser**
```
Open: https://yourname.app.n8n.cloud/webhook/alexa-tech-briefing
```
- Should show RSS XML
- If it shows an error, go to n8n and check Workflow 2

**✅ Test 2: Verify Workflow 2 is active**
1. Go to n8n Cloud
2. Open "Alexa - Tech Briefing RSS" workflow
3. Check if toggle at top is **ON** (green)
4. If OFF, turn it on and try again

**✅ Test 3: Check if Airtable has today's data**
1. Open your Airtable base
2. Look for rows with today's date
3. If empty, run Workflow 1 manually or wait for a newsletter

**✅ Test 4: Verify Alexa has correct URL**
1. Go to [Alexa Developer Console](https://developer.amazon.com/alexa/console/ask)
2. Open your Flash Briefing skill
3. Check the Feed URL matches your n8n webhook URL exactly
4. Click SAVE again

---

### Problem: Alexa says "You're up to date with your flash briefing"

**Cause:** No new content in your RSS feed

**Solutions:**

**✅ Check Airtable has today's summaries**
- Open Airtable
- Filter by today's date
- If empty, newsletters haven't been processed yet

**✅ Send a test newsletter**
1. Forward a newsletter to yourself
2. Make sure sender domain matches your Gmail filter
3. Wait 1-2 minutes for processing
4. Check Airtable for new entry

**✅ Manually trigger Workflow 1**
1. Go to n8n → Workflow 1
2. Click "Execute workflow" at bottom
3. Select a test email from Gmail
4. Verify it processes and creates Airtable entry

---

### Problem: Alexa reads the wrong content or gibberish

**Cause:** RSS feed has HTML tags or malformed text

**Solutions:**

**✅ Check your RSS feed output**
```
Open: https://yourname.app.n8n.cloud/webhook/alexa-tech-briefing
```
- Look at the `<description>` tags
- Should contain clean, readable text
- No HTML tags like `<p>`, `<div>`, etc.

**✅ Fix the Code node in Workflow 1**
- The HTML stripping code might have an issue
- Check the Code node that extracts EmailBody
- Test with: `console.log(emailBody)` to debug

---

### Problem: Alexa skill not showing up in app

**Cause:** Skill not enabled or not in Development mode

**Solutions:**

**✅ Enable Development mode**
1. Go to Alexa Developer Console → Your Skill
2. Click "Test" tab
3. Toggle "Development" mode ON

**✅ Enable skill on device**
1. Alexa app → More → Skills & Games
2. Your Skills → Dev tab
3. Find your skill → Enable

**✅ Add to Flash Briefing rotation**
1. Alexa app → More → Settings → Flash Briefing
2. Get more Flash Briefing content
3. Enable your skill
4. Drag to reorder

---

## n8n Workflow Issues

### Problem: Workflow 1 not triggering automatically

**Possible Causes:**
1. Workflow is not active
2. Gmail OAuth expired
3. Gmail filter doesn't match incoming emails

**Solutions:**

**✅ Verify workflow is active**
1. Open Workflow 1
2. Check toggle at top is green (ON)
3. If red (OFF), turn it on

**✅ Check Gmail OAuth connection**
1. Go to Credentials in n8n
2. Find your Gmail OAuth2 credential
3. Should show "Connected" status
4. If not, click to reconnect and reauthorize

**✅ Test the Gmail filter**
1. Click on Gmail Trigger node
2. Click "Execute node"
3. Should pull recent emails matching your filter
4. If no emails, adjust the filter syntax

**Example filters:**
```
# Match specific domains
from:(techcrunch.com OR theverge.com)

# Match any Substack newsletter
from:substack.com

# Match specific sender
from:newsletters@nytimes.com

# Combine multiple conditions
from:(techcrunch.com OR substack.com) is:unread
```

---

### Problem: Workflow shows error in executions

**Solutions:**

**✅ Check execution details**
1. Go to n8n → Executions (left sidebar)
2. Find the failed execution (red icon)
3. Click to open
4. Look at which node failed
5. Read the error message

**Common errors and fixes:**

| Error | Solution |
|-------|----------|
| "Invalid credentials" | Reconnect the credential in Credentials section |
| "Rate limit exceeded" | Wait a few minutes, you hit API limits |
| "Timeout" | Node took too long, increase timeout in Settings |
| "Invalid JSON" | Check expression syntax in node fields |
| "Required field missing" | Fill in all required fields in node config |

---

### Problem: Workflow runs but no data in output

**Possible Causes:**
1. Input data is empty
2. Expression paths are wrong
3. Node execution skipped

**Solutions:**

**✅ Check each node's output**
1. Execute workflow
2. Click on each node after execution
3. Look at OUTPUT tab
4. Verify data is flowing correctly

**✅ Verify expression paths**
- Click on node with expressions
- Look for red error indicators
- Check that referenced nodes exist
- Verify JSON paths are correct

Example correct paths:
```javascript
// From Gmail Trigger
{{ $json.subject }}
{{ $json.from.text }}

// From Code node named "Code in JavaScript"
{{ $('Code in JavaScript').item.json.EmailBody }}

// From Claude/Anthropic node
{{ $('Message a model').item.json.content[0].text }}
```

---

## Gmail Integration Issues

### Problem: Gmail OAuth keeps disconnecting

**Cause:** Google requires periodic re-authentication

**Solutions:**

**✅ Reconnect Gmail OAuth**
1. n8n → Credentials
2. Find Gmail OAuth2 credential
3. Click the credential
4. Click "Reconnect"
5. Sign in and authorize again

**✅ Check Google Cloud Console**
1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. APIs & Services → Credentials
3. Verify OAuth client ID still exists
4. Check if redirect URI matches your n8n URL

---

### Problem: No emails being fetched

**Solutions:**

**✅ Verify Gmail has matching emails**
1. Go to Gmail web
2. Use the same search query in search box:
   ```
   from:(techcrunch.com OR theverge.com)
   ```
3. Should show matching emails
4. If no results, adjust your filter

**✅ Check Gmail Trigger settings**
1. Event should be "Message Received"
2. Simplify should be OFF
3. Search filter should match your newsletters

**✅ Test with a simple filter first**
```
from:techcrunch.com
```
Once working, expand to more sources.

---

## Claude AI Issues

### Problem: Summaries are too long

**Solutions:**

**✅ Adjust the system prompt**

Change this in the Anthropic node:
```
You are an expert at creating concise, audio-friendly news briefings. 
Convert newsletter content into EXACTLY 2 sentences suitable for 
Alexa to read aloud. Be extremely concise. Maximum 50 words total.
```

**✅ Add word count constraint**

In user message:
```
Summarize this newsletter into exactly 2 sentences (maximum 50 words) 
for an audio briefing:
...
```

---

### Problem: Summaries are too short or vague

**Solutions:**

**✅ Provide more context to Claude**

Adjust system prompt:
```
You are an expert at creating concise, audio-friendly news briefings. 
Convert newsletter content into 3-4 engaging sentences suitable for 
Alexa to read aloud. Focus on the most important news items with 
specific details. Include key facts, numbers, or company names when relevant.
```

**✅ Increase email body character limit**

In Code node, change:
```javascript
// From this:
emailBody = emailBody.substring(0, 4000);

// To this:
emailBody = emailBody.substring(0, 6000);
```

---

### Problem: Claude API errors or rate limits

**Solutions:**

**✅ Check API key is valid**
1. Go to [Anthropic Console](https://console.anthropic.com/)
2. Verify API key is active
3. Check usage limits

**✅ Handle rate limits**
- Claude Haiku has generous limits
- If hitting limits, add delays between requests
- Or upgrade to higher API tier

**✅ Add error handling**

In Anthropic node Options:
- Set timeout to 30 seconds
- Enable "Continue on Fail"

---

## Airtable Issues

### Problem: Data not saving to Airtable

**Possible Causes:**
1. Airtable credential expired
2. Base ID or Table name wrong
3. Field mappings incorrect

**Solutions:**

**✅ Test Airtable connection**
1. Click on Airtable node
2. Click "Execute step"
3. Check for errors in OUTPUT
4. If "Invalid token", reconnect credential

**✅ Verify Base ID and Table name**
1. Go to your Airtable base
2. Check URL for Base ID (starts with `app...`)
3. Verify table name matches exactly (case-sensitive)

**✅ Check field mappings**

Make sure these expressions work:
```javascript
// Date field
{{ $now.toFormat('yyyy-MM-dd') }}

// Title field
{{ $('Code in JavaScript').item.json.Subject }}

// Summary field
{{ $('Message a model').item.json.content[0].text }}

// Source field
{{ $('Code in JavaScript').item.json.From }}
```

---

### Problem: Duplicate entries in Airtable

**Cause:** Workflow ran multiple times for same email

**Solutions:**

**✅ Use Gmail labels to prevent duplicates**

Modify Gmail Trigger:
- Add filter: `is:unread`
- After processing, mark email as read
- Or apply a label

**✅ Add deduplication logic**

Before Airtable node, add a Code node:
```javascript
// Check if entry already exists for this email
const title = $input.item.json.Subject;
const date = new Date().toISOString().split('T')[0];

// Query Airtable first to check for duplicates
// (Would need additional Airtable Search node before this)

return { json: $input.item.json };
```

---

## RSS Feed Issues

### Problem: RSS feed returns empty XML

**Cause:** No records in Airtable for today

**Solutions:**

**✅ Check Airtable filter formula**

In Workflow 2, Airtable Search node:
```
IS_SAME({Date}, TODAY(), 'day')
```

This formula gets only today's records.

**✅ Verify data exists**
1. Open Airtable
2. Check if any rows have today's date
3. If not, process a newsletter first

**✅ Test with broader date range**

Temporarily change filter to:
```
IS_AFTER({Date}, DATEADD(TODAY(), -7, 'days'))
```
This gets last 7 days of data for testing.

---

### Problem: RSS feed has invalid XML

**Cause:** Special characters breaking XML format

**Solutions:**

**✅ Add XML escaping to Code node**

In Workflow 2, RSS generation Code node:

```javascript
// Add this function
function escapeXml(unsafe) {
  return unsafe
    .replace(/&/g, "&amp;")
    .replace(/</g, "&lt;")
    .replace(/>/g, "&gt;")
    .replace(/"/g, "&quot;")
    .replace(/'/g, "&apos;");
}

// Use it when building XML
const description = escapeXml(record.json.fields.Summary || '');
```

---

### Problem: RSS feed not updating with new content

**Cause:** Caching or workflow not running

**Solutions:**

**✅ Add cache-busting headers**

In "Respond to Webhook" node, add headers:
```
Content-Type: application/rss+xml; charset=utf-8
Cache-Control: no-cache, no-store, must-revalidate
Expires: 0
```

**✅ Test with browser hard refresh**
```
Windows/Linux: Ctrl + Shift + R
Mac: Cmd + Shift + R
```

**✅ Check workflow execution**
1. Go to n8n Executions
2. Verify Workflow 2 ran when you accessed the URL
3. Check if any errors occurred

---

## General Debugging Tips

### Use n8n's Debug Features

**1. Execute Individual Nodes**
- Click a node → "Execute step"
- See output immediately
- Don't need to run full workflow

**2. View Execution Data**
- After workflow runs, click any node
- Switch between Schema/Table/JSON views
- Copy data for debugging

**3. Use Console Logs**

In Code nodes:
```javascript
console.log('Debug info:', variable);
return { json: data };
```

View logs in Executions → Click execution → View logs

### Check Service Status

If issues persist, check service status:
- [n8n Status](https://status.n8n.io/)
- [Anthropic Status](https://status.anthropic.com/)
- [Google Workspace Status](https://www.google.com/appsstatus)
- [Airtable Status](https://status.airtable.com/)
- [Amazon Alexa Status](https://developer.amazon.com/support/status)

### Still Stuck?

1. **Search n8n Community**: [community.n8n.io](https://community.n8n.io/)
2. **Check GitHub Issues**: Look for similar problems
3. **Open an Issue**: [Create issue](https://github.com/yourusername/alexa-newsletter-briefing/issues/new) with:
   - What you're trying to do
   - What's happening instead
   - Error messages
   - Screenshots of workflow/config
4. **Contact me**: [LinkedIn](https://www.linkedin.com/in/akhilneelam/)

---

**Pro tip:** When asking for help, always include:
- Which node is failing
- Full error message
- Screenshot of node configuration
- Sample of input data (with sensitive info removed)

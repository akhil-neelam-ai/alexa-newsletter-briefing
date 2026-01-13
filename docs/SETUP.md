# Complete Setup Guide

This guide walks you through every step of setting up your Alexa Newsletter Briefing system.

**Estimated time:** 60-90 minutes

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Part 1: n8n Cloud Setup](#part-1-n8n-cloud-setup)
3. [Part 2: Gmail Integration](#part-2-gmail-integration)
4. [Part 3: Claude AI Configuration](#part-3-claude-ai-configuration)
5. [Part 4: Airtable Database](#part-4-airtable-database)
6. [Part 5: Workflow 1 - Newsletter Collection](#part-5-workflow-1---newsletter-collection)
7. [Part 6: Workflow 2 - RSS Feed Generation](#part-6-workflow-2---rss-feed-generation)
8. [Part 7: Alexa Flash Briefing Skill](#part-7-alexa-flash-briefing-skill)
9. [Part 8: Testing & Activation](#part-8-testing--activation)

---

## Prerequisites

Before starting, gather these accounts and credentials:

### Required Accounts
- [ ] n8n Cloud account (14-day free trial available)
- [ ] Google account with Gmail
- [ ] Google Cloud Console access (for Gmail API)
- [ ] Anthropic account (for Claude API)
- [ ] Airtable account (free tier)
- [ ] Amazon Developer account (free)
- [ ] Alexa device (Echo, Echo Dot, Echo Show, etc.)

### Tools Needed
- Web browser (Chrome, Firefox, Safari)
- Text editor for copying credentials
- Your Alexa device connected to WiFi

---

## Part 1: n8n Cloud Setup

### Step 1.1: Create n8n Cloud Account

1. Go to [https://n8n.io/cloud/](https://n8n.io/cloud/)
2. Click **"Start for free"** or **"Try n8n Cloud"**
3. Sign up with your email
4. Verify your email address
5. Choose the **Starter plan** (14-day free trial, then $20/month)
6. Complete the onboarding

**Result:** You'll get a permanent URL like: `https://yourname.app.n8n.cloud`

### Step 1.2: Familiarize with n8n Interface

1. Click **"Workflows"** in the left sidebar
2. Click **"Add workflow"** to create a test workflow
3. Explore the node library (click the **+** button)
4. Delete the test workflow

**You're now ready to import the actual workflows!**

---

## Part 2: Gmail Integration

### Step 2.1: Enable Gmail API in Google Cloud

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or use an existing one:
   - Click the project dropdown at the top
   - Click **"New Project"**
   - Name it: `Newsletter Automation`
   - Click **"Create"**

3. Enable Gmail API:
   - Go to **APIs & Services** → **Library**
   - Search for "Gmail API"
   - Click **"Enable"**

### Step 2.2: Create OAuth Credentials

1. Go to **APIs & Services** → **Credentials**
2. Click **"Configure Consent Screen"**
   - User Type: **External**
   - Click **"Create"**
   
3. Fill in OAuth consent screen:
   - App name: `Newsletter Briefing`
   - User support email: [your email]
   - Developer contact: [your email]
   - Click **"Save and Continue"**

4. Add Scopes:
   - Click **"Add or Remove Scopes"**
   - Search for `Gmail API`
   - Select: `https://www.googleapis.com/auth/gmail.readonly`
   - Select: `https://www.googleapis.com/auth/gmail.modify`
   - Click **"Update"** → **"Save and Continue"**

5. Add Test Users:
   - Click **"Add Users"**
   - Add your Gmail address
   - Click **"Save and Continue"**

6. Create OAuth Client ID:
   - Go back to **Credentials**
   - Click **"+ CREATE CREDENTIALS"** → **"OAuth client ID"**
   - Application type: **Web application**
   - Name: `n8n Gmail Integration`
   - **Authorized redirect URIs**: Add:
     ```
     https://yourname.app.n8n.cloud/rest/oauth2-credential/callback
     ```
     *(Replace `yourname` with your actual n8n instance name)*
   - Click **"CREATE"**

7. **Copy your credentials:**
   - Client ID: `xxxxx.apps.googleusercontent.com`
   - Client Secret: `GOCSPX-xxxxx`
   
   **⚠️ Save these somewhere safe!**

### Step 2.3: Add Gmail Credentials in n8n

1. In n8n Cloud, click **"Credentials"** in the left sidebar
2. Click **"+ Add Credential"**
3. Search for **"Gmail OAuth2 API"**
4. Fill in:
   - **Client ID**: [paste from Google Cloud]
   - **Client Secret**: [paste from Google Cloud]
5. Click **"Connect my account"**
6. Sign in with your Gmail account
7. Click **"Allow"** to grant permissions
8. Credential should show as **"Connected"** ✓

---

## Part 3: Claude AI Configuration

### Step 3.1: Get Anthropic API Key

1. Go to [Anthropic Console](https://console.anthropic.com/)
2. Sign up or log in
3. Click **"API Keys"** in the left menu
4. Click **"Create Key"**
5. Name it: `Newsletter Summarizer`
6. **Copy the API key** (starts with `sk-ant-...`)
   
   **⚠️ You can only see this once! Save it securely!**

### Step 3.2: Add Claude Credentials in n8n

1. In n8n Cloud, go to **"Credentials"**
2. Click **"+ Add Credential"**
3. Search for **"Anthropic"**
4. Paste your API key
5. Click **"Save"**

---

## Part 4: Airtable Database

### Step 4.1: Create Airtable Base

1. Go to [Airtable](https://airtable.com)
2. Sign up or log in
3. Click **"Add a base"** → **"Start from scratch"**
4. Name it: **"Newsletter Briefings"**

### Step 4.2: Configure Table Structure

The default table will be called "Table 1" - rename it to **"Newsletter briefings"**

Add these fields:

| Field Name | Field Type | Notes |
|------------|------------|-------|
| **Date** | Date | For tracking when newsletter arrived |
| **Title** | Single line text | Email subject line |
| **Summary** | Long text | AI-generated summary |
| **Source** | Single line text | Email sender |

**Steps:**
1. Click the **"+"** next to existing columns to add new fields
2. Click the dropdown arrow on column names to change type
3. Delete any default fields you don't need

### Step 4.3: Get Airtable Credentials

1. Click your **profile icon** (top right) → **"Developer hub"**
2. Click **"Create new token"**
3. Configure:
   - **Name**: `n8n Integration`
   - **Scopes**: Check these:
     - `data.records:read`
     - `data.records:write`
     - `schema.bases:read`
   - **Access**: Click **"Add a base"** → Select **"Newsletter briefings"**
4. Click **"Create token"**
5. **Copy the token** (starts with `pat...`)

   **⚠️ Save this! You can't see it again!**

### Step 4.4: Get Your Base ID

1. Go to your **"Newsletter briefings"** base
2. Look at the URL:
   ```
   https://airtable.com/appXXXXXXXXXXXXXX/...
   ```
3. Copy the part starting with `app...` (that's your Base ID)

### Step 4.5: Add Airtable Credentials in n8n

1. In n8n Cloud, go to **"Credentials"**
2. Click **"+ Add Credential"**
3. Search for **"Airtable Personal Access Token"**
4. Paste your token
5. Click **"Save"**

---

## Part 5: Workflow 1 - Newsletter Collection

### Step 5.1: Import Workflow

1. In n8n, click **"Workflows"**
2. Click **"Add workflow"**
3. Name it: **"Newsletter Collection"**
4. Click the **"..."** menu (top right) → **"Import from File"**
5. Select `workflows/newsletter-collection.json` from this repo
6. The workflow nodes should appear

### Step 5.2: Configure Gmail Trigger Node

1. Click on the **"Gmail Trigger"** node
2. Select your Gmail OAuth2 credential
3. Configure:
   - **Event**: `Message Received`
   - **Simplify**: Turn **OFF** ❌
   - **Options** → **Search**:
     ```
     from:(techcrunch.com OR theverge.com OR substack.com OR nytimes.com)
     ```
     *(Add your preferred newsletter sources)*

4. Click **"Execute node"** to test
   - It should fetch a recent newsletter
   - Check the OUTPUT to see the email data

### Step 5.3: Configure Code Node (Extract Email Body)

1. Click on the **"Code in JavaScript"** node
2. The code should already be there (from import)
3. Click **"Execute step"** to test
4. Check OUTPUT - you should see:
   - Subject
   - From
   - EmailBody (extracted text)

**The code does:**
- Extracts HTML content from Gmail
- Strips HTML tags to get plain text
- Limits to first 4000 characters for Claude

### Step 5.4: Configure Claude (Anthropic) Node

1. Click on the **"Message a model"** node
2. Select your Anthropic credential
3. Configure:
   - **Model**: `claude-3-5-haiku-20241022`
   - **Prompt Type**: Keep default
   
4. **System Message** (in Options):
   ```
   You are an expert at creating concise, audio-friendly news briefings. 
   Convert newsletter content into 2-3 engaging sentences suitable for 
   Alexa to read aloud. Focus on the most important news items. Be 
   conversational and natural-sounding.
   ```

5. **User Message**:
   ```
   Summarize this newsletter into 2-3 sentences for an audio briefing:

   Subject: {{ $json.Subject }}
   From: {{ $json.From }}

   Content:
   {{ $json.EmailBody }}
   ```

6. Click **"Execute step"** to test
7. Check OUTPUT - you should see a concise summary!

### Step 5.5: Configure Airtable Node

1. Click on the **"Create or update a record"** node
2. Select your Airtable credential
3. Configure:
   - **Resource**: `Record`
   - **Operation**: `Create`
   - **Base ID**: Select **"Newsletter briefings"** from dropdown
   - **Table**: Select **"Newsletter briefings"** table

4. **Field Mappings:**

   **Date Field:**
   - Click in Date field
   - Switch to **Expression** mode (click the `=` icon)
   - Enter: `{{ $now.toFormat('yyyy-MM-dd') }}`

   **Title Field:**
   - Switch to Expression mode
   - Enter: `{{ $('Code in JavaScript').item.json.Subject }}`

   **Summary Field:**
   - Switch to Expression mode
   - Enter: `{{ $('Message a model').item.json.content[0].text }}`

   **Source Field:**
   - Switch to Expression mode
   - Enter: `{{ $('Code in JavaScript').item.json.From }}`

5. Click **"Execute step"** to test
6. **Check your Airtable** - you should see a new row!

### Step 5.6: Save and Activate Workflow 1

1. Click **"Save"** (top right)
2. Toggle the workflow to **Active** (switch at top)
3. The workflow will now run automatically when newsletters arrive!

---

## Part 6: Workflow 2 - RSS Feed Generation

### Step 6.1: Import Workflow

1. Click **"Workflows"** → **"Add workflow"**
2. Name it: **"Alexa - Tech Briefing RSS"**
3. Import `workflows/alexa-rss-feed.json`

### Step 6.2: Configure Webhook Trigger

1. Click on the **"Webhook"** node
2. Configure:
   - **HTTP Method**: `GET`
   - **Path**: `alexa-tech-briefing`
   - **Respond**: `Using 'Respond to Webhook' Node`

3. **Copy the Production URL:**
   ```
   https://yourname.app.n8n.cloud/webhook/alexa-tech-briefing
   ```
   **⚠️ Save this URL! You'll need it for Alexa!**

### Step 6.3: Configure Airtable Search Node

1. Click on the **"Search records"** node
2. Select your Airtable credential
3. Configure:
   - **Resource**: `Record`
   - **Operation**: `Search`
   - **Base ID**: `Newsletter briefings`
   - **Table**: `Newsletter briefings`
   - **Filter By Formula**:
     ```
     IS_SAME({Date}, TODAY(), 'day')
     ```
   - **Return All**: Toggle **ON** ✓

4. Click **"Execute step"** to test
   - Should return today's newsletter summaries

### Step 6.4: Configure RSS Generation Code Node

1. Click on the **"Code in JavaScript"** node
2. The RSS generation code should already be there
3. Click **"Execute step"** to test
4. Check OUTPUT - you should see RSS XML!

**The code does:**
- Combines all today's summaries into one briefing
- Generates proper RSS XML format for Alexa
- Includes title, description, pub date, GUID

### Step 6.5: Configure Respond to Webhook Node

1. Click on the **"Respond to Webhook"** node
2. Configure:
   - **Respond With**: `Text`
   - **Response Body**: `{{ $json.rss }}`
   - **Options** → **Response Headers**:
     - **Name**: `Content-Type`
     - **Value**: `application/rss+xml; charset=utf-8`

### Step 6.6: Save and Activate Workflow 2

1. Click **"Save"**
2. Toggle to **Active**

### Step 6.7: Test the RSS Feed

Open this URL in your browser:
```
https://yourname.app.n8n.cloud/webhook/alexa-tech-briefing
```

You should see XML that looks like:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<rss version="2.0">
  <channel>
    <title>Akhil's Tech Briefing</title>
    ...
  </channel>
</rss>
```

---

## Part 7: Alexa Flash Briefing Skill

### Step 7.1: Create Alexa Skill

1. Go to [Alexa Developer Console](https://developer.amazon.com/alexa/console/ask)
2. Sign in or create an Amazon Developer account
3. Click **"Create Skill"**

4. Configure skill basics:
   - **Skill name**: `Tech Newsletter Briefing` (or your preferred name)
   - **Primary locale**: English (US)
   - **Type of experience**: Other
   - **Model**: Flash Briefing
   - **Hosting**: Provision your own
5. Click **"Create skill"**

### Step 7.2: Configure Flash Briefing Feed

1. Click **"+ Add new feed"**

2. Fill in feed details:
   - **Preamble**: `In your tech briefing` or `Here's your tech briefing`
   - **Name**: `Tech Newsletter Briefing`
   - **Content update frequency**: `Daily`
   - **Content type**: `Text`
   - **Content genre**: `Technology` or `Science & Technology`
   - **Feed URL**: 
     ```
     https://yourname.app.n8n.cloud/webhook/alexa-tech-briefing
     ```
   - **Icon**: Upload a 512x512 PNG image (optional)

3. Click **"Add"**
4. Click **"SAVE"** (top of page)

### Step 7.3: Test the Skill

1. Go to the **"Test"** tab
2. Enable **"Development"** mode (toggle at top)
3. In the Alexa Simulator, type:
   ```
   ask tech newsletter briefing for my briefing
   ```

**Or test on your actual device:**
- Say: **"Alexa, what's my flash briefing?"**

---

## Part 8: Testing & Activation

### Step 8.1: Add Skill to Your Alexa Device

1. Open the **Alexa app** on your phone
2. Go to **More** → **Skills & Games**
3. Tap **"Your Skills"** → **"Dev"** tab
4. Find **"Tech Newsletter Briefing"**
5. Tap **"Enable"**

### Step 8.2: Configure Flash Briefing Order

1. In Alexa app: **More** → **Settings** → **Flash Briefing**
2. Tap **"Get more Flash Briefing content"**
3. Find your skill and enable it
4. Go back to **Flash Briefing** settings
5. **Drag to reorder** your briefing sources
6. Put "Tech Newsletter Briefing" where you want it

### Step 8.3: Test End-to-End

**Morning routine test:**

1. **Send yourself a test newsletter** (or wait for a real one)
2. **Check Workflow 1 executed** (in n8n → Executions)
3. **Verify Airtable has the entry** (open your base)
4. **Test the RSS feed** (open webhook URL in browser)
5. **Ask Alexa**: "Alexa, what's my flash briefing?"

**You should hear your newsletter summary! 🎉**

### Step 8.4: Monitor & Debug

**Check n8n Executions:**
- Go to n8n → Click **"Executions"** in left sidebar
- See history of workflow runs
- Click on any execution to debug

**Check Airtable Data:**
- Open your base regularly
- Verify summaries are being stored
- Check for any blank/error entries

**Test Alexa Response:**
- Use the Alexa app to see text of what Alexa would say
- Go to Activity to see past briefings

---

## 🎉 Congratulations!

Your Alexa Newsletter Briefing system is now live!

**What happens now:**
1. New newsletters arrive in Gmail
2. n8n automatically extracts and summarizes them
3. Summaries are stored in Airtable
4. Each morning, ask Alexa for your briefing
5. Alexa combines and reads all summaries

**Next Steps:**
- [Customize your briefing](CUSTOMIZATION.md)
- [Troubleshoot issues](TROUBLESHOOTING.md)
- [Add more features](../README.md#customization-ideas)

---

**Need help?** [Open an issue](https://github.com/yourusername/alexa-newsletter-briefing/issues) or reach out on [LinkedIn](https://www.linkedin.com/in/akhilneelam/)

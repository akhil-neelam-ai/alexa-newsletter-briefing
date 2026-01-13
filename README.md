# Alexa Newsletter Briefing 📰🤖

> Transform your daily newsletters into an AI-powered audio briefing delivered through Alexa

[![n8n](https://img.shields.io/badge/n8n-Cloud-blue)](https://n8n.io)
[![Claude AI](https://img.shields.io/badge/Claude-AI-purple)](https://anthropic.com)

## 📺 Demo

[Watch the demo video](https://www.linkedin.com/posts/akhilneelam_techautomation-ai-productivityhack-activity-7416624475321913362-uPJW?utm_source=share&utm_medium=member_desktop&rcm=ACoAABBO9jYBzpbFmyeJuaql55xs2TnXfE7QS58) 

**What it sounds like:**
> "Alexa, what's my flash briefing?"
> 
> *"Here's your tech briefing: Google has announced a new AI commerce protocol allowing merchants to offer discounts directly in AI search results, while Meta is expanding its green energy footprint by signing power deals with three nuclear companies..."*

## 🎯 What It Does

This automation system:

1. **Monitors Gmail** for newsletters from your favorite sources (TechCrunch, The Verge, Substack, etc.)
2. **Extracts content** from HTML emails and cleans the text
3. **Summarizes with AI** using Claude 3.5 Haiku to create concise, audio-friendly summaries
4. **Stores in Airtable** with date tracking for historical reference
5. **Generates RSS feed** that combines all daily summaries
6. **Delivers via Alexa** as a Flash Briefing skill

**Result:** Get all your tech news in one 2-minute audio briefing while having breakfast ☕

## 💡 Why I Built This

I was subscribed to 5+ tech newsletters (TechCrunch, The Verge, Stratechery, etc.) but rarely had time to read them all. Each morning, my inbox had dozens of unread articles. I wanted a way to:

- Stay informed without spending 30+ minutes scrolling
- Consume news while doing other morning tasks
- Get summaries, not full articles
- Have everything in one place

This project solves that by turning my email clutter into a 2-minute daily audio briefing.

## 🏗️ Architecture

```
┌─────────────┐
│   Gmail     │ Newsletters arrive
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  n8n Cloud  │ Workflow 1: Newsletter Collection
│   Workflow  │ ├─ Gmail Trigger (monitors inbox)
│      #1     │ ├─ Code Node (extract HTML content)
│             │ ├─ Claude AI (summarize to 2-3 sentences)
│             │ └─ Airtable (store summary + metadata)
└─────────────┘
       │
       ▼
┌─────────────┐
│  Airtable   │ Stores all newsletter summaries
│   Database  │ Fields: Date, Title, Summary, Source
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  n8n Cloud  │ Workflow 2: Alexa RSS Feed
│   Workflow  │ ├─ Webhook (receives Alexa requests)
│      #2     │ ├─ Airtable Search (get today's summaries)
│             │ ├─ Code Node (generate RSS XML)
│             │ └─ Respond (return RSS feed)
└──────┬──────┘
       │
       ▼
┌─────────────┐
│Alexa Flash  │ Reads combined briefing aloud
│  Briefing   │ "Alexa, what's my flash briefing?"
└─────────────┘
```

## 🛠️ Tech Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Automation** | [n8n Cloud](https://n8n.io) | Workflow orchestration |
| **Email** | Gmail API | Newsletter monitoring |
| **AI Summarization** | [Claude 3.5 Haiku](https://anthropic.com) | Text summarization |
| **Storage** | [Airtable](https://airtable.com) | Summary database |
| **Voice** | Alexa Skills Kit | Audio delivery |


## 🚀 Quick Start

### Prerequisites

- [ ] n8n Cloud account ([Sign up](https://n8n.io/cloud/))
- [ ] Google account with Gmail
- [ ] Anthropic API key ([Get one](https://console.anthropic.com/))
- [ ] Airtable account ([Sign up](https://airtable.com/signup))
- [ ] Amazon Developer account ([Sign up](https://developer.amazon.com/))
- [ ] Alexa device (Echo, Echo Dot, etc.)

### Installation Steps

**Step 1:** Set up n8n Cloud and import workflows
```bash
# Download the workflow files
git clone https://github.com/yourusername/alexa-newsletter-briefing.git
cd alexa-newsletter-briefing
```

**Step 2:** Import workflows into n8n
- Go to your n8n Cloud instance
- Import `workflows/newsletter-collection.json`
- Import `workflows/alexa-rss-feed.json`

**Step 3:** Configure credentials
- Gmail OAuth2 (see [Gmail Setup Guide](docs/SETUP.md#gmail-setup))
- Anthropic API Key
- Airtable Personal Access Token

**Step 4:** Create Airtable base
- Create base: "Newsletter Briefings"
- Add fields: Date, Title, Summary, Source
- Copy Base ID

**Step 5:** Set up Alexa Flash Briefing skill
- Create skill in Alexa Developer Console
- Add feed URL from n8n webhook
- Enable on your device

📖 **[Full Setup Guide →](docs/SETUP.md)**

## 📁 Project Structure

```
alexa-newsletter-briefing/
├── workflows/
│   ├── newsletter-collection.json    # Workflow 1: Gmail → Claude → Airtable
│   └── alexa-rss-feed.json          # Workflow 2: Webhook → RSS Feed
├── docs/
│   ├── SETUP.md                     # Detailed setup instructions
│   ├── TROUBLESHOOTING.md           # Common issues and solutions
│   ├── CUSTOMIZATION.md             # How to customize for your needs
│   └── screenshots/                 # Setup screenshots
│       ├── gmail-setup.png
│       ├── n8n-workflow1.png
│       ├── n8n-workflow2.png
│       ├── airtable-base.png
│       └── alexa-console.png
├── examples/
│   ├── sample-rss-output.xml        # Example RSS feed
│   └── sample-airtable-data.csv     # Example data structure
├── LICENSE
└── README.md
```

## 🎨 Customization Ideas

**Newsletter Sources:**
- Add more sources to Gmail filter: `from:(axios.com OR bloomberg.com OR reuters.com)`
- Create topic-specific briefings (AI news, crypto, etc.)

**AI Summarization:**
- Adjust Claude's tone (more formal, more casual)
- Change summary length (1 sentence vs 3 sentences)
- Add specific focus areas (e.g., "focus on startup funding news")

**Delivery Options:**
- Add SMS notifications for breaking news
- Create a web dashboard to view summaries
- Send daily email digest instead of/in addition to Alexa

**Advanced Features:**
- Sentiment analysis on news
- Categorization by topic
- Historical trend analysis
- Voice recordings instead of TTS

## 🐛 Troubleshooting

**Problem:** Alexa says "Unable to fetch content"
- ✅ Verify RSS feed URL works in browser
- ✅ Check that Workflow 2 is active in n8n
- ✅ Ensure Airtable has records for today's date

**Problem:** No newsletters being processed
- ✅ Verify Gmail OAuth connection is active
- ✅ Check Gmail filter syntax
- ✅ Test with a manual test email

**Problem:** Summaries are too long/short
- ✅ Adjust Claude's system prompt
- ✅ Modify character limit in Code node
- ✅ Test with different newsletter formats

📖 **[Full Troubleshooting Guide →](docs/TROUBLESHOOTING.md)**

## 🤝 Contributing

Contributions are welcome! Here are some ways you can help:

- 🐛 Report bugs and issues
- 💡 Suggest new features
- 📖 Improve documentation
- 🔧 Submit pull requests

Please read [CONTRIBUTING.md](CONTRIBUTING.md) for details on our code of conduct and the process for submitting pull requests.

## 👤 Author

**Akhil Neelam**
- 🎓 MBA Student at UC Berkeley Haas (Class of 2027)
- 🔗 [LinkedIn](https://www.linkedin.com/in/akhilneelam/)
- 📧 akhil_neelam@berkeley.edu

## 🙏 Acknowledgments

- Built as a weekend project during my MBA at UC Berkeley Haas
- Inspired by the need to stay updated without information overload
- Thanks to the [n8n community](https://community.n8n.io/) for workflow inspiration
- Shoutout to Anthropic for Claude's excellent summarization capabilities

## 📚 Related Projects

- [n8n Workflow Templates](https://n8n.io/workflows/)
- [Alexa Flash Briefing Documentation](https://developer.amazon.com/docs/flashbriefing/understand-the-flash-briefing-skill-api.html)
- [Claude API Cookbook](https://github.com/anthropics/anthropic-cookbook)

## ⭐ Star History

If this project helped you, please consider giving it a star! It helps others discover it.

---

*Have questions? reach out on [LinkedIn](https://www.linkedin.com/in/akhilneelam/)*

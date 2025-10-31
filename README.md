# 🎬 UGC Video Automation Bot

> Automated User-Generated Content (UGC) video creation through Telegram/WhatsApp using AI models and n8n workflows.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![n8n](https://img.shields.io/badge/n8n-workflow-FF6D5A)](https://n8n.io)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-14+-316192.svg)](https://www.postgresql.org/)

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Demo](#demo)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Configuration](#configuration)
- [Usage](#usage)
- [Development](#development)
- [Contributing](#contributing)
- [License](#license)

## Overview

This project automates the creation of professional UGC-style videos for products through conversational interfaces such as Telegram and WhatsApp.  
Users upload product images, select styles, and receive AI-generated videos within minutes.

**Core Tech Stack**
- **n8n** – No-code workflow automation  
- **PostgreSQL** – State and session management  
- **AI Models** – Runway, Replicate, Luma Labs, Stability AI  
- **Storage** – AWS S3 / Google Cloud Storage  

**Use Cases**  
E-commerce product showcases, UGC content for marketers, agency-scale content creation, and automated video pipelines for startups.

---

## ✨ Features

### Core Functionality
- 💬 **Multi-Channel Support** - Telegram & WhatsApp bots
- 🎨 **Multiple AI Models** - Runway Gen-3/4, Kling, Luma, Stability AI
- 🔄 **Revision System** - Iterative improvements with feedback
- ⚡ **Async Generation** - Non-blocking video creation
- 📱 **Social Media Integration** - Direct posting to Instagram/Facebook
- 👤 **Face Compositing** - Optional character face replacement
- 💰 **Cost Tracking** - Per-generation cost monitoring

### Advanced Features
- 🔗 **Parent-Child Linking** - Revision history tracking
- 🎯 **State Machine** - 10-state conversation flow
- 📊 **Analytics** - User metrics and generation statistics
- 🔍 **Error Recovery** - Automatic retry and fallback
- 🌐 **Webhook Support** - Real-time message handling

## 🎥 Demo

### User Flow

```
1. User: /start
   → Bot: "Welcome! Upload your product image 📸"

2. User: [uploads image]
   → Bot: "Image received! Choose your video style 🎨"

3. User: [selects "Fashion Walk"]
   → Bot: "Select character or product-only 👤"

4. User: [selects character]
   → Bot: "Choose AI model 🎬"

5. User: [selects "Runway Gen-3, 10 seconds"]
   → Bot: "Generation started! ETA: 2 min ⏱️"
   [Video generates in background]

6. Bot: [sends completed video]
   → "Your video is ready! 🎉"
   [Approve & Post | Request Changes | Start Over]

7. User: [Approve & Post]
   → Bot: "Posted to Instagram! 📸"
```

### Sample Output

| Input | Output |
|-------|--------|
| Product image + "Cinematic walk" | 10-second vertical video (1080x1920) |
| Generation time | 60-180 seconds (model-dependent) |
| Cost per video | $0.05 - $1.20 (model-dependent) |

## 🏗️ Architecture

### System Overview

```
┌─────────────────────────────────────────────────┐
│             Users (Telegram/WhatsApp)           │
└────────────────────┬────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────┐
│              n8n Master Workflow                │
│  • Message routing (10-state machine)           │
│  • Session management                           │
│  • Async sub-workflow execution                 │
└─────────────┬───────────────────────────────────┘
              │
    ┌─────────┼─────────┐
    ▼         ▼         ▼
┌────────┐ ┌──────┐ ┌──────────┐
│ Image  │ │Video │ │ Social   │
│Process │ │Gen   │ │ Media    │
│(Sub)   │ │(Sub) │ │ (Sub)    │
└────────┘ └──────┘ └──────────┘
              │
    ┌─────────┼─────────┐
    ▼         ▼         ▼
┌──────┐ ┌──────┐ ┌──────┐
│  DB  │ │  S3  │ │ AI   │
│(PG)  │ │      │ │Models│
└──────┘ └──────┘ └──────┘
```

### Workflows

| Workflow | Purpose |
|----------|---------|
| **Master Workflow** | Message routing & state management |
| **Image_Processing_Workflow** | Download, validate, resize, store images |
| **Video_Generation_Workflow** | Generate videos via AI APIs (async) |

### State Machine

```
NEW → WAITING_IMAGE → WAITING_PROMPT → WAITING_CHARACTER 
→ WAITING_MODEL → PROCESSING → WAITING_CONFIRMATION 
→ [READY_TO_POST | WAITING_REVISION_INPUT] → COMPLETED
```

## 📦 Prerequisites

### Required Services

- **n8n** (v1.0+) - Workflow automation
- **PostgreSQL** (v14+) - Database
- **AWS S3** or **Google Cloud Storage** - File storage
- **Telegram Bot Token** - Free from [@BotFather](https://t.me/botfather)
- **WhatsApp Business API** - From Facebook Developer Console

### AI Model Providers (Choose ≥2)

| Provider | Model | Cost/Video | Signup Credit |
|----------|-------|------------|---------------|
| [Replicate](https://replicate.com) | Kling, Wan, Hailuo | $0.05-0.15 | $10 free |
| [Runway](https://runwayml.com) | Gen-3 Turbo, Gen-4 | $0.50-1.00 | None |
| [Luma Labs](https://lumalabs.ai) | Dream Machine | $1.20 | None |
| [Stability AI](https://stability.ai) | Video Diffusion | $0.80 | $5 free |

### Optional

- **Instagram Graph API** - For posting to Instagram
- **Facebook Graph API** - For posting to Facebook Pages

## 🚀 Quick Start

### 1. Clone Repository

```bash
git clone https://github.com/yourusername/ugc-video-automation.git
cd ugc-video-automation
```

### 2. Setup Database

```bash
# Create database
createdb ugc_video_automation

# Run schema
psql -U postgres -d ugc_video_automation -f database_schema.sql

# Verify tables
psql -U postgres -d ugc_video_automation -c "\dt"
```

### 3. Install n8n

**Docker (Recommended):**
```bash
docker-compose up -d
```

**NPM:**
```bash
npm install n8n -g
n8n start
```

Access at: http://localhost:5678

### 4. Import Workflows

```bash
# Via n8n CLI
n8n import:workflow --input=workflows/Image_Processing_Workflow.json
n8n import:workflow --input=workflows/Video_Generation_Workflow.json
n8n import:workflow --input=workflows/Master_Workflow.json

# Or via UI: Settings → Workflows → Import from File
```

### 5. Configure Credentials

In n8n UI, add credentials for:

```yaml
Required:
  - UGC_Postgres (PostgreSQL connection string)
  - UGC_Telegram_Bot (Bot token from @BotFather)
  - UGC_WhatsApp (Access token + Phone Number ID)
  - UGC_S3_Storage (AWS Access Key + Secret)
  - UGC_Replicate (Replicate API key)

Optional:
  - UGC_Runway (Runway API key)
  - UGC_Luma (Luma API key)
  - Instagram_API (Access token)
  - Facebook_API (Page access token)
```

### 6. Activate Master Workflow

1. Open "Master Workflow" in n8n
2. Toggle "Active" in top-right corner
3. Copy webhook URLs from Telegram/WhatsApp trigger nodes

### 7. Configure Webhooks

**Telegram:**
```bash
curl -X POST \
  https://api.telegram.org/bot<YOUR_BOT_TOKEN>/setWebhook \
  -d "url=<YOUR_N8N_WEBHOOK_URL>"
```

**WhatsApp:**
- Go to Facebook Developer Console → WhatsApp → Configuration
- Paste webhook URL
- Verify token: `ugc_webhook_verify_token`
- Subscribe to: `messages`

### 8. Test

Send `/start` to your Telegram bot!

## ⚙️ Configuration

### Environment Variables

```bash
# .env (for Docker Compose)
DATABASE_URL=postgres://user:pass@host:5432/ugc_video_automation
TELEGRAM_BOT_TOKEN=123456789:ABCdef...
WHATSAPP_TOKEN=EAABsbCS...
AWS_ACCESS_KEY_ID=AKIA...
AWS_SECRET_ACCESS_KEY=abc123...
S3_BUCKET_NAME=ugc-video-storage-xyz
REPLICATE_API_KEY=r8_...
RUNWAY_API_KEY=rw_...
WEBHOOK_URL=https://your-n8n.com
```

### Adding AI Models

```sql
-- Insert into models_config table
INSERT INTO models_config (
  model_id, display_name, provider, 
  api_endpoint, api_credential_name, 
  cost_per_second, default_length, max_length
) VALUES (
  'new-model-id', 'Display Name', 'provider',
  'https://api.provider.com/v1/generate',
  'n8n_Credential_Name',
  0.10, 10, 15
);
```

Then update `Video_Generation_Workflow` to add API integration for the new model.

### Customizing Messages

Edit Code nodes named `Format: [Message Type]` in workflows. Example:

```javascript
// Node: "Format: Welcome Message"
const message = `👋 Welcome to UGC Video Creator!

Upload your product image to start! 📸`;

return [{ json: { message_text: message } }];
```

## 📖 Usage

### For End Users

1. **Start:** Send `/start` to the bot
2. **Upload:** Send product image
3. **Style:** Select video style/prompt
4. **Character:** Choose character or product-only
5. **Generate:** Select AI model & duration
6. **Review:** Receive video in 1-3 minutes
7. **Action:** Approve, revise, or start over

## 🛠️ Development

### Project Structure

```
ugc-video-automation/
├── workflows/
│   ├── Master_Workflow.json
│   ├── Image_Processing_Workflow.json
│   └── Video_Generation_Workflow.json
├── database_schema.sql
├── docker-compose.yml
├── .env.example
├── docs/
│   ├── STICKY_NOTES.md
│   ├── ARCHITECTURE.md
│   └── API_REFERENCE.md
└── README.md
```

## 🤝 Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add AmazingFeature'`)
4. Push to branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request


## 📄 License

This project is licensed under the MIT License - see [LICENSE](LICENSE) file for details.

**⭐ Star this repo if you find it useful!**

</div>

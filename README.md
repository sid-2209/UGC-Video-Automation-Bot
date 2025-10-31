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
- [Deployment](#deployment)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

## 🎯 Overview

This project automates the creation of professional UGC-style videos for products through conversational interfaces (Telegram/WhatsApp). Users upload product images, select video styles, choose AI models, and receive polished videos in 1-3 minutes.

**Built with n8n** - No-code workflow automation platform  
**Database** - PostgreSQL for state management  
**AI Models** - Runway, Replicate, Luma Labs, Stability AI  
**Storage** - AWS S3 / Google Cloud Storage  

### Use Cases

- 🛍️ **E-commerce:** Product video generation for social media
- 📱 **Marketers:** Rapid UGC content creation
- 🎨 **Agencies:** Scalable video production for clients
- 🚀 **Startups:** Automated content marketing

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

| Workflow | Purpose | Nodes | Active |
|----------|---------|-------|--------|
| **Master Workflow** | Message routing & state management | ~35 | ✅ Yes |
| **Image_Processing_Workflow** | Download, validate, resize, store images | ~20 | ❌ No |
| **Video_Generation_Workflow** | Generate videos via AI APIs (async) | ~50 | ❌ No |

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

### For Developers

**Query User Sessions:**
```sql
SELECT current_state, context, updated_at 
FROM user_sessions 
WHERE user_id = 'telegram_123';
```

**Check Generation Status:**
```sql
SELECT generation_id, status, model_name, created_at
FROM video_generations 
ORDER BY created_at DESC 
LIMIT 5;
```

**View Error Logs:**
```sql
SELECT error_type, error_message, created_at
FROM error_logs 
ORDER BY created_at DESC 
LIMIT 10;
```

**Monitor Costs:**
```sql
SELECT 
  DATE(created_at) as date,
  model_name,
  COUNT(*) as videos,
  SUM(cost) as total_cost
FROM video_generations
WHERE created_at > NOW() - INTERVAL '7 days'
GROUP BY DATE(created_at), model_name
ORDER BY date DESC;
```

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

### Testing

**End-to-End Test:**
```bash
# 1. Start services
docker-compose up -d

# 2. Run test script
python tests/test_workflow.py

# 3. Manual test
# Send /start to your bot and complete full flow
```

**Database Tests:**
```sql
-- Test session creation
SELECT * FROM get_or_create_session('telegram_test', 'telegram');

-- Test generation record
INSERT INTO video_generations (
  session_id, model_id, prompt_text, 
  image_url, video_length, status
) VALUES (1, 'runway-gen3-turbo', 'test prompt', 
  'https://s3.../test.jpg', 10, 'pending')
RETURNING generation_id;
```

### Adding New Features

**Example: Add New State**

1. Add state to database constraint:
```sql
ALTER TABLE user_sessions DROP CONSTRAINT IF EXISTS valid_session_state;
ALTER TABLE user_sessions ADD CONSTRAINT valid_session_state 
CHECK (current_state IN ('NEW', 'WAITING_IMAGE', ..., 'NEW_STATE'));
```

2. Add new output to Main Router Switch node in Master Workflow

3. Create node chain for new state

4. Update state transition logic

### Code Style

- **SQL:** Uppercase keywords, snake_case for identifiers
- **JavaScript:** camelCase, descriptive variable names
- **n8n Nodes:** Clear names (`Format: Welcome Message`, not `Code1`)

## 🚀 Deployment

### Docker Compose (Production)

```yaml
# docker-compose.prod.yml
version: '3.8'

services:
  n8n:
    image: docker.n8n.io/n8nio/n8n
    restart: unless-stopped
    ports:
      - "5678:5678"
    environment:
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=postgres
      - DB_POSTGRESDB_DATABASE=n8n
      - DB_POSTGRESDB_USER=n8n
      - DB_POSTGRESDB_PASSWORD=${N8N_DB_PASSWORD}
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=${N8N_USER}
      - N8N_BASIC_AUTH_PASSWORD=${N8N_PASSWORD}
      - WEBHOOK_URL=${WEBHOOK_URL}
    volumes:
      - n8n_data:/home/node/.n8n
    depends_on:
      - postgres

  postgres:
    image: postgres:15
    restart: unless-stopped
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
      - POSTGRES_DB=ugc_video_automation
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

volumes:
  n8n_data:
  postgres_data:
```

```bash
# Deploy
docker-compose -f docker-compose.prod.yml up -d

# View logs
docker-compose logs -f n8n

# Backup database
docker-compose exec postgres pg_dump -U postgres ugc_video_automation > backup.sql
```

### Scaling

For high volume (100+ concurrent users):

1. **Load Balancer** - nginx/HAProxy for multiple n8n instances
2. **Database Replication** - PostgreSQL primary + replicas
3. **Queue System** - Redis for async job processing
4. **CDN** - CloudFront for S3 content delivery

See [DEPLOYMENT.md](docs/DEPLOYMENT.md) for detailed scaling guide.

## 🐛 Troubleshooting

### Common Issues

**Bot Not Responding:**
```bash
# Check webhook
curl https://api.telegram.org/bot<TOKEN>/getWebhookInfo

# Reset webhook
curl -X POST https://api.telegram.org/bot<TOKEN>/setWebhook \
  -d "url=<NEW_URL>"
```

**Database Errors:**
```bash
# Verify tables exist
psql -U postgres -d ugc_video_automation -c "\dt"

# Re-run schema if missing
psql -U postgres -d ugc_video_automation -f database_schema.sql
```

**Video Generation Timeout:**
- Check "Wait for Sub-Workflow Completion" = **FALSE** in Branch 4
- This is the most common issue - MUST be async!

**Images Not Uploading:**
```bash
# Test S3 access
aws s3 ls s3://your-bucket-name/

# Check credentials
aws sts get-caller-identity
```

### Debug Queries

```sql
-- Check session state
SELECT user_id, current_state, context 
FROM user_sessions 
WHERE updated_at > NOW() - INTERVAL '1 hour';

-- Check stuck generations
SELECT * FROM video_generations 
WHERE status IN ('pending', 'processing')
  AND created_at < NOW() - INTERVAL '10 minutes';

-- Recent errors
SELECT * FROM error_logs 
ORDER BY created_at DESC 
LIMIT 20;
```

### Getting Help

- **Documentation:** Check [docs/](docs/) folder
- **n8n Community:** https://community.n8n.io
- **Issues:** Open a GitHub issue
- **Discord:** [Join our Discord](https://discord.gg/your-invite)

## 🤝 Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add AmazingFeature'`)
4. Push to branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

### Development Setup

```bash
# Clone your fork
git clone https://github.com/yourusername/ugc-video-automation.git

# Install dependencies
npm install

# Setup pre-commit hooks
npm run prepare

# Run tests
npm test
```

### Guidelines

- Write descriptive commit messages
- Update documentation for new features
- Add tests for new functionality
- Follow existing code style
- Update CHANGELOG.md

## 📊 Performance

### Benchmarks

| Metric | Value |
|--------|-------|
| Response time (async) | <2 seconds |
| Video generation | 60-180 seconds |
| Concurrent users (single instance) | 50-100 |
| Database queries | <50ms (with indexes) |
| S3 upload | 2-5 seconds |

### Optimization

- **Database indexes** on user_id, session_id, status fields
- **CloudFront CDN** for video delivery
- **Connection pooling** for API requests
- **Async execution** for long-running tasks
- **Caching** for model configs and prompts

## 💰 Cost Estimation

### Monthly Costs (1,000 videos/month)

| Service | Cost |
|---------|------|
| n8n Cloud | $20-50 |
| PostgreSQL (Railway/Supabase) | $5-20 |
| AWS S3 (100GB) | $2-5 |
| AI Models (Runway Gen-3) | $500-1,000 |
| Telegram/WhatsApp | Free |
| **Total** | **~$527-1,075/month** |

**Cost per video:** $0.50-1.00 (varies by AI model)

### Reducing Costs

- Use cheaper AI models (Replicate > Runway)
- Implement daily limits per user
- Cache similar generations
- Use S3 lifecycle policies (move to Glacier after 30 days)
- Self-host AI models (requires GPU server)

## 📄 License

This project is licensed under the MIT License - see [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [n8n](https://n8n.io) - Workflow automation platform
- [Runway ML](https://runwayml.com) - AI video generation
- [Replicate](https://replicate.com) - AI model hosting
- [Luma Labs](https://lumalabs.ai) - Dream Machine
- [Stability AI](https://stability.ai) - Video diffusion models

## 📞 Contact & Support

- **GitHub Issues:** [Report bugs or request features](https://github.com/yourusername/ugc-video-automation/issues)
- **Email:** your.email@example.com
- **Twitter:** [@yourhandle](https://twitter.com/yourhandle)
- **Discord:** [Join our community](https://discord.gg/your-invite)

## 🗺️ Roadmap

### Q1 2025
- [ ] Batch video generation
- [ ] Template library
- [ ] Analytics dashboard
- [ ] User authentication

### Q2 2025
- [ ] Mobile app (React Native)
- [ ] A/B testing for prompts
- [ ] Video editing features
- [ ] TikTok integration

### Q3 2025
- [ ] Multi-language support
- [ ] AI voice-overs
- [ ] Scheduled posting
- [ ] Team collaboration features

See [ROADMAP.md](docs/ROADMAP.md) for detailed plans.

---

<div align="center">

**⭐ Star this repo if you find it useful!**

Made with ❤️ by [Your Name](https://github.com/yourusername)

[Report Bug](https://github.com/yourusername/ugc-video-automation/issues) · [Request Feature](https://github.com/yourusername/ugc-video-automation/issues) · [Documentation](docs/)

</div>

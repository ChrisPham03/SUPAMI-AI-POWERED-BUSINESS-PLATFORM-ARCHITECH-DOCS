# 🧠 SUPAMI - AI-Powered RAG Platform

<p align="center">
  <img src="https://img.shields.io/badge/status-production-green" alt="Status">
  <img src="https://img.shields.io/badge/next.js-15.5.9-black" alt="Next.js">
  <img src="https://img.shields.io/badge/fastapi-0.109-009688" alt="FastAPI">
  <img src="https://img.shields.io/badge/docker-ready-2496ED" alt="Docker">
  <img src="https://img.shields.io/badge/terraform-1.5+-7B42BC" alt="Terraform">
  <img src="https://img.shields.io/badge/AWS-deployed-FF9900" alt="AWS">
</p>

<p align="center">
  <strong>🌐 Live at: <a href="https://supami.com">https://supami.com</a></strong>
</p>

---

**SUPAMI** (formerly BrainBox) is a cloud-native, multi-tenant **RAG (Retrieval-Augmented Generation)** platform. It allows businesses to upload proprietary knowledge (PDFs), configure custom AI Agents, and integrate intelligent chatbots into their applications.

## ✨ Features

- 🤖 **Custom AI Agents** - Create personalized chatbots with custom system prompts
- 📄 **PDF Knowledge Base** - Upload documents to build agent knowledge
- 🔍 **Vector Search** - Semantic search powered by Qdrant
- 📅 **Booking System** - Built-in appointment scheduling with AI
- 🔐 **Multi-tenant** - Secure data isolation per user
- 🚀 **Production Ready** - HTTPS, CI/CD, automated backups

---

## 🏗 Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                  https://supami.com 🔒                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   ┌─────────┐    ┌─────────┐    ┌─────────┐               │
│   │ Next.js │◄──►│  Nginx  │◄──►│ Internet│               │
│   │  :3000  │    │ :80/443 │    │         │               │
│   └────┬────┘    └─────────┘    └─────────┘               │
│        │                                                   │
│   ┌────▼────┐    ┌─────────┐    ┌─────────┐              │
│   │ FastAPI │◄──►│  Redis  │◄──►│ Celery  │              │
│   │  :8000  │    │  :6379  │    │ Worker  │              │
│   └────┬────┘    └─────────┘    └────┬────┘              │
│        │                              │                   │
│   ┌────▼────┐    ┌─────────┐    ┌────▼────┐             │
│   │Postgres │    │ Qdrant  │    │   S3    │             │
│   │  :5432  │    │  :6333  │    │ Bucket  │             │
│   └─────────┘    └─────────┘    └─────────┘             │
│                                                          │
│   🖥️ EC2 t4g.micro (ARM64) | 💰 ~$0/month (Free Tier)   │
└──────────────────────────────────────────────────────────┘
```

## 🛠 Tech Stack

| Layer | Technology | Purpose |
|-------|------------|---------|
| **Frontend** | Next.js 15.5.9, React 18, Tailwind CSS | Dashboard UI |
| **Backend** | FastAPI, Python 3.11 | REST API |
| **AI/ML** | OpenAI, LangChain | LLM & Embeddings |
| **Vector DB** | Qdrant | Semantic search |
| **Database** | PostgreSQL 15 | Relational data |
| **Queue** | Redis + Celery | Async processing |
| **Proxy** | Nginx | SSL termination |
| **Infrastructure** | Terraform, AWS | IaC |
| **CI/CD** | GitHub Actions | Auto-deployment |
| **SSL** | Let's Encrypt | HTTPS certificates |

---

## 📁 Project Structure

```
SUPAMI-2.0/
├── brainbox-server/          # Backend (Docker Compose root)
│   ├── app/
│   │   ├── routers/          # API endpoints
│   │   │   ├── auth.py       # Authentication
│   │   │   ├── agents.py     # Agent CRUD
│   │   │   ├── chat.py       # Chat endpoint
│   │   │   ├── bookings.py   # Booking system
│   │   │   ├── ingestion.py  # File upload
│   │   │   └── public.py     # Public chat API
│   │   ├── services/         # Business logic
│   │   ├── tools/            # AI tools
│   │   ├── core/             # Config & security
│   │   ├── main.py           # FastAPI app
│   │   ├── worker.py         # Celery worker
│   │   ├── models.py         # SQLAlchemy models
│   │   └── database.py       # DB connections
│   ├── nginx/                # Nginx configs
│   ├── certbot/              # SSL certificates
│   ├── docker-compose.yml    # Container orchestration
│   ├── Dockerfile            # API container
│   └── requirements.txt      # Python dependencies
│
├── brainbox-web/             # Frontend
│   ├── src/
│   │   ├── app/              # Next.js pages
│   │   └── components/       # React components
│   ├── package.json
│   ├── tailwind.config.ts
│   └── Dockerfile
│
├── brainbox-terraform/       # Infrastructure as Code
│   ├── main.tf               # AWS resources
│   ├── variables.tf          # Input variables
│   ├── outputs.tf            # Output values
│   └── backend.tf            # S3 remote state
│
├── .github/
│   └── workflows/
│       └── deploy.yml        # CI/CD pipeline
│
├── DEPLOYMENT_GUIDE.md       # Detailed deployment docs
└── README.md                 # This file
```

---

## 🚀 Quick Start

### Prerequisites

- Docker Desktop
- AWS CLI v2 (configured)
- Terraform >= 1.5
- Node.js 20+
- Git

### Local Development

```bash
# Clone repository
git clone https://github.com/ChrisPham03/SUPAMI-2.0.git
cd SUPAMI-2.0

# Create environment file
cd brainbox-server
cp .env.example .env
# Edit .env with your API keys

# Start all services
docker compose up -d --build

# Access the app
open http://localhost:3000      # Frontend
open http://localhost/v1/docs   # API Docs
```

### Production Deployment

See [DEPLOYMENT_GUIDE.md](./DEPLOYMENT_GUIDE.md) for complete instructions.

**Quick deploy:**

```bash
# SSH into server
ssh -i ~/.ssh/brainbox-key.pem ec2-user@16.52.187.210

# Pull and rebuild
cd ~/SUPAMI-2.0
git pull origin main
cd brainbox-server
docker compose up -d --build
```

---

## ⚙️ Environment Variables

Create `.env` in `brainbox-server/`:

```ini
# Core
ENV=production
SECRET_KEY=your-32-char-secret-key

# AI Provider
OPENAI_API_KEY=sk-proj-...

# AWS (IAM Role handles auth on EC2)
AWS_REGION=ca-central-1
S3_BUCKET_NAME=your-bucket-name

# Database
POSTGRES_SERVER=postgres
POSTGRES_USER=chrisv
POSTGRES_PASSWORD=your-secure-password
POSTGRES_DB=brainbox_app
DATABASE_URL=postgresql://chrisv:password@postgres:5432/brainbox_app

# Infrastructure
REDIS_URL=redis://redis:6379/0
QDRANT_URL=http://db:6333
QDRANT_COLLECTION=brainbox_docs
```

---

## 🔄 CI/CD Pipeline

Automatic deployment on push to `main`:

```yaml
# .github/workflows/deploy.yml
name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to EC2
        uses: appleboy/ssh-action@v1.0.3
        with:
          host: ${{ secrets.SERVER_HOST }}
          username: ${{ secrets.SERVER_USER }}
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          script: |
            cd ~/SUPAMI-2.0
            git pull origin main
            cd brainbox-server
            docker compose up -d --build
```

### Required Secrets

| Secret | Value |
|--------|-------|
| `SERVER_HOST` | `16.52.187.210` |
| `SERVER_USER` | `ec2-user` |
| `SSH_PRIVATE_KEY` | Contents of SSH private key |

---

## 🔒 Security

- ✅ HTTPS with Let's Encrypt (auto-renewal)
- ✅ JWT authentication
- ✅ IAM roles (no hardcoded AWS credentials)
- ✅ Multi-tenant data isolation
- ✅ Security groups (minimal port exposure)
- ✅ Encrypted Terraform state in S3

---

## 💾 Backups

Automated daily backups at 3:00 AM UTC:

```bash
# Manual backup
~/backup.sh

# List backups
aws s3 ls s3://brainbox-storage-345594599917/backups/

# Restore
~/restore.sh postgres_2025-12-30_03-00-00.sql.gz
```

---

## 📊 Monitoring

- **UptimeRobot** - Uptime monitoring (https://uptimerobot.com)
- **Docker Stats** - `docker stats`
- **Logs** - `docker compose logs -f [service]`

---

## 🐛 Troubleshooting

### Build Fails (Out of Memory)

```bash
# Enable swap on EC2
sudo swapon /swapfile
free -h  # Should show 2GB swap
```

### Database Connection Issues

```bash
# Reset database
docker compose down -v
docker compose up -d --build
```

### SSL Certificate Issues

```bash
# Force renewal
cd ~/SUPAMI-2.0/brainbox-server
docker compose stop proxy
docker run --rm -p 80:80 \
  -v $(pwd)/certbot/conf:/etc/letsencrypt \
  certbot/certbot renew --force-renewal
docker compose start proxy
```

### View Logs

```bash
docker compose logs -f api      # Backend
docker compose logs -f web      # Frontend
docker compose logs -f worker   # Celery
docker compose logs -f proxy    # Nginx
```

---

## 📚 API Documentation

Interactive API docs available at:
- **Production:** https://supami.com/v1/docs
- **Local:** http://localhost/v1/docs

### Key Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/v1/auth/register` | Register new user |
| POST | `/v1/auth/login` | Get JWT token |
| GET | `/v1/agents/` | List user's agents |
| POST | `/v1/agents/` | Create new agent |
| POST | `/v1/files` | Upload PDF |
| POST | `/v1/chat` | Chat with agent |
| POST | `/v1/public/chat` | Public chat (no auth) |
| GET | `/v1/bookings/` | List bookings |

---

## 💰 Cost

| Resource | Monthly Cost |
|----------|--------------|
| EC2 t4g.micro | $0 (Free Tier) |
| S3 Storage | $0 (Free Tier) |
| Data Transfer | $0 (Free Tier) |
| **Total** | **~$0** |

*After free tier: ~$8/month*

---

## 🗺 Roadmap

- [ ] Add multi-language support
- [ ] Implement rate limiting
- [ ] Add WebSocket for real-time chat
- [ ] Kubernetes migration option
- [ ] Add analytics dashboard
- [ ] Mobile app (React Native)

---

## 🤝 Contributing

1. Fork the repository
2. Create feature branch (`git checkout -b feature/amazing`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing`)
5. Open Pull Request

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

---

## 👤 Author

**Chris Pham**
- GitHub: [@ChrisPham03](https://github.com/ChrisPham03)
- Email: chrisph0303@gmail.com

---

## 🙏 Acknowledgments

- [OpenAI](https://openai.com) - LLM provider
- [LangChain](https://langchain.com) - AI framework
- [Qdrant](https://qdrant.tech) - Vector database
- [Vercel](https://vercel.com) - Next.js creators
- [Let's Encrypt](https://letsencrypt.org) - Free SSL

.

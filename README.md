# OpenSOAR Deploy

Deployment configurations for the [OpenSOAR](https://github.com/opensoar-hq/opensoar) SOAR platform.

## Quick Start

```bash
# Clone
git clone https://github.com/opensoar-hq/opensoar-deploy.git
cd opensoar-deploy

# Configure
cp .env.example .env
# Edit .env with your settings

# Start
docker compose up -d

# Open UI
open http://localhost:3000
```

## Services

| Service | Image | Port | Purpose |
|---------|-------|------|---------|
| api | opensoar | 8000 | FastAPI backend |
| worker | opensoar | — | Celery playbook executor |
| ui | opensoar-ui | 3000 | React frontend |
| postgres | postgres:16 | 5432 | Database |
| redis | redis:7 | 6379 | Broker + cache |
| migrate | opensoar | — | Database migrations (runs once) |

## Development

For local development with source mounting:

```bash
# Assumes repos are siblings: ../opensoar, ../opensoar-ui
docker compose -f docker-compose.yml -f docker-compose.dev.yml up -d
```

## Architecture

```
                    ┌──────────┐
                    │    UI    │ :3000
                    │  (nginx) │
                    └────┬─────┘
                         │ /api proxy
                    ┌────▼─────┐
                    │   API    │ :8000
                    │(FastAPI) │
                    └──┬───┬───┘
                       │   │
              ┌────────┘   └────────┐
              ▼                     ▼
        ┌──────────┐         ┌──────────┐
        │ Postgres │         │  Redis   │
        │   :5432  │         │  :6379   │
        └──────────┘         └─────┬────┘
                                   │
                              ┌────▼─────┐
                              │  Worker  │
                              │ (Celery) │
                              └──────────┘
```

## Part of OpenSOAR

- [opensoar](https://github.com/opensoar-hq/opensoar) — Core platform
- [opensoar-ui](https://github.com/opensoar-hq/opensoar-ui) — Frontend
- [opensoar-sdk](https://github.com/opensoar-hq/opensoar-sdk) — Python SDK
- [opensoar-integrations](https://github.com/opensoar-hq/opensoar-integrations) — Community integrations

# OpenSOAR Deploy

Deployment configurations for the [OpenSOAR](https://github.com/opensoar-hq/opensoar-core) SOAR platform.

**OpenSOAR is a PwnKit Labs product.**

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
| api | ghcr.io/opensoar-hq/opensoar-core-api | 8000 | FastAPI backend |
| worker | ghcr.io/opensoar-hq/opensoar-core-worker | — | Celery playbook executor |
| ui | ghcr.io/opensoar-hq/opensoar-core-ui | 3000 | React frontend |
| postgres | postgres:16-alpine | 5432 | Database |
| redis | redis:7-alpine | 6379 | Broker + cache |
| migrate | ghcr.io/opensoar-hq/opensoar-core-migrate | — | Database migrations (runs once) |

## Development

For local development with source mounting:

```bash
# Assumes opensoar-core repo is a sibling: ../opensoar-core
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

- [opensoar-core](https://github.com/opensoar-hq/opensoar-core) — Core platform (API, UI, playbook engine)
- [opensoar-sdk](https://github.com/opensoar-hq/opensoar-sdk) — Python SDK
- [opensoar-integrations](https://github.com/opensoar-hq/opensoar-integrations) — Community integrations

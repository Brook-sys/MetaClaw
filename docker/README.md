# MetaClaw Docker

Docker configurations for MetaClaw.

## Variants

| File | Description | Use Case |
|------|-------------|----------|
| `Dockerfile.base` | Skills only (lightweight) | Most users |
| `Dockerfile.rl` | With RL training | Training with GPU |
| `Dockerfile.full` | All extras | Full development |

## Quick Start

### Skills Only (Recommended)

```bash
docker-compose -f docker-compose.base.yml up -d
```

### With RL Training

```bash
docker-compose -f docker-compose.rl.yml up -d
```

### Full (All Features)

```bash
docker-compose -f docker-compose.full.yml up -d
```

## Configuration

After starting the container, access it to run setup:

```bash
docker exec -it metaclaw metaclaw setup
```

### Via Environment Variables

You can override configuration via environment variables:

```yaml
environment:
  - METACLAW_MODE=skills_only  # or "rl", "auto"
  - LLM_PROVIDER=kimi
  - LLM_API_KEY=your-api-key
```

### Via Volume Mount

Configuration is stored in `/home/alpine/.metaclaw` (Docker volume). To use your own config:

```yaml
volumes:
  - ./config:/home/alpine/.metaclaw:ro
```

## Ports

| Port | Service |
|------|---------|
| 30000 | MetaClaw Proxy API |

## Persistence

Data is stored in Docker volume `metaclaw-config`:
- `~/.metaclaw/config.yaml` - Configuration
- `~/.metaclaw/skills/` - Custom skills
- `~/.metaclaw/memory/` - Long-term memory

## GPU Support (RL)

For RL training with GPU, use `docker-compose.rl.yml` with NVIDIA Docker:

```yaml
deploy:
  resources:
    reservations:
      devices:
        - driver: nvidia
          count: all
          capabilities: [gpu]
```

## Commands

```bash
# Build images
docker build -t metaclaw:base -f docker/Dockerfile.base .
docker build -t metaclaw:rl -f docker/Dockerfile.rl .
docker build -t metaclaw:full -f docker/Dockerfile.full .

# Run specific mode
docker run -p 30000:30000 metaclaw:base metaclaw start --mode skills_only

# Check logs
docker logs metaclaw

# Stop
docker stop metaclaw
```

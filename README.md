# Keycloak Observability Stack

Production-ready monitoring stack for Keycloak with Prometheus metrics and Grafana dashboards, featuring OAuth2/OIDC authentication.

## Architecture

- **Keycloak** - Identity and access management with metrics enabled
- **Prometheus** - Time-series metrics collection (15s scrape interval, 30d retention)
- **Grafana** - Metrics visualization with OAuth authentication
- **PostgreSQL** - Keycloak backend (ephemeral storage)

## Quick Start

```bash
# Start the stack
docker compose up -d

# Verify services
docker compose ps
```

## Access Points

| Service | URL | Credentials |
|---------|-----|-------------|
| Grafana | http://localhost:3000 | OAuth via Keycloak |
| Keycloak | http://localhost:8080 | admin / keycloak |
| Prometheus | http://localhost:9090 | - |

## Key Features

- **Pre-configured OAuth** - Grafana authenticates via Keycloak OIDC
- **JVM Monitoring** - Heap memory, GC metrics, thread counts, class loading
- **Connection Pooling** - Agroal datasource metrics (idle, active, leak detection)
- **Auto-provisioned** - Dashboard and datasource provisioning on startup
- **Structured Logging** - JSON logs with rotation (10MB × 3 files)

## Metrics Coverage

```yaml
JVM Metrics:
  - Memory: heap usage, committed, max
  - GC: collection count/time by collector
  - Threads: live, daemon, peak counts
  - Classes: loaded, unloaded counts

Connection Pool:
  - Active/idle/awaiting connections
  - Acquisition time (avg, max, total)
  - Creation time, leak detection
  - Flush and reap operations
```

## Security Notes

- Keycloak runs in dev mode (`start-dev`)
- PostgreSQL uses tmpfs (ephemeral storage)
- SSL verification disabled for OAuth (local development)
- Default credentials should be changed for production

## Monitoring

Access the Keycloak dashboard in Grafana after OAuth login. Pre-configured panels include:
- System resources (CPU, load average)
- Memory utilization trends
- Connection pool health
- GC behavior analysis

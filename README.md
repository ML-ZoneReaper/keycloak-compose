# Keycloak Observability Stack

Production-ready monitoring stack for Keycloak with Prometheus metrics and Grafana dashboards, featuring OAuth2/OIDC authentication.

## Architecture

- **Keycloak** - Identity and access management with metrics enabled
- **Prometheus** - Time-series metrics collection (15s scrape interval, 30d retention)
- **Grafana** - Metrics visualization with OAuth authentication
- **PostgreSQL** - Keycloak backend (ephemeral storage)

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) v20.10.7+
- [Docker Compose](https://docs.docker.com/compose/install/) v2.0.0+

## Quick Start

```bash
# Clone the repository
git clone https://github.com/ML-ZoneReaper/keycloak-compose.git
cd keycloak-compose

# Start the stack
docker compose up -d

# Verify services are running
docker compose ps

# Follow logs in real-time
docker compose logs -f
```

## Access Points

| Service | URL | Credentials |
|---------|-----|-------------|
| Grafana | http://localhost:3000 | admin / grafana |
| Keycloak | http://localhost:8080 | admin / keycloak |
| Prometheus | http://localhost:9090 | - |

### First-Time Setup

1. Access Keycloak Admin Console at http://localhost:8080
2. Log in with default credentials (`admin` / `keycloak`)
3. Grafana will authenticate through Keycloak OAuth automatically

## Configuration

Environment variables in `.env`:

**Important**: Modify credentials before deploying to production.

## Key Features

- **Pre-configured OAuth** - Grafana authenticates via Keycloak OIDC
- **Auto-provisioned Dashboards** - Pre-loaded Keycloak monitoring dashboard
- **JVM Monitoring** - Heap memory, GC metrics, thread counts, class loading
- **Connection Pooling** - Agroal datasource metrics (idle, active, leak detection)
- **Structured Logging** - JSON logs with rotation (10MB × 3 files)
- **Data Retention** - 30-day metric retention in Prometheus

## Metrics Coverage

### JVM Metrics
- **Memory**: heap usage, committed memory, max heap
- **Garbage Collection**: collection count/time by collector type
- **Threads**: live threads, daemon threads, peak thread count
- **Class Loading**: loaded classes, unloaded classes, current count

### Connection Pool (Agroal)
- Active/idle/awaiting connections
- Acquisition time (average, maximum, total)
- Connection creation time and count
- Leak detection events
- Flush and reap operations
- Maximum concurrent connections

### System Metrics
- Available processors
- System load average
- CPU utilization

## Management Commands

### Service Control
```bash
# Start services
docker compose up -d

# Stop services
docker compose down

# Stop and remove volumes (destroys data)
docker compose down -v

# Restart specific service
docker compose restart keycloak

# View service status
docker compose ps
```

### Monitoring & Logs
```bash
# Follow all logs
docker compose logs -f

# Follow specific service logs
docker compose logs -f keycloak
docker compose logs -f prometheus
docker compose logs -f grafana

# View resource usage
docker stats
```

### Data Management
```bash
# Remove all containers, volumes, and images
docker compose down -v --rmi all

# Clean up Docker system (use with caution)
docker system prune -a -f --volumes
```

## Monitoring

Access the pre-configured Keycloak dashboard in Grafana after OAuth login:

**Dashboard Panels Include:**
- System resources (CPU, load average)
- Memory utilization trends over time
- Connection pool health and saturation
- Garbage collection behavior analysis
- Thread count and daemon threads
- Class loading statistics

**Prometheus Metrics Endpoint:**
- Keycloak metrics: http://keycloak:9000/metrics
- Prometheus self-metrics: http://prometheus:9090/metrics

## Security Notes

⚠️ **Development Configuration**
- Keycloak runs in dev mode (`start-dev`)
- PostgreSQL uses tmpfs (ephemeral storage - data lost on restart)
- SSL verification disabled for OAuth (local development only)
- Default credentials are insecure

🔒 **Production Recommendations**
- Change all default passwords in `.env`
- Enable production mode: replace `start-dev` with `start`
- Use persistent volumes for PostgreSQL
- Enable SSL/TLS for all services
- Configure proper network isolation
- Implement secret management (Vault, etc.)
- Review and harden Keycloak realm settings

## Troubleshooting

### Services Won't Start

**Check port conflicts:**
```bash
# Verify ports 3000, 8080, 9090 are available
lsof -i :3000
lsof -i :8080
lsof -i :9090
```

**Inspect service health:**
```bash
# Check container status
docker compose ps

# View recent logs
docker compose logs --tail=50 keycloak
```

**Verify no conflicting containers:**
```bash
docker ps -a | grep -E "keycloak|grafana|prometheus|postgres"
```

### Common Issues

| Issue | Solution |
|-------|----------|
| Port already in use | Stop conflicting services or change ports in `.env` |
| Keycloak won't connect to DB | Check PostgreSQL logs: `docker compose logs postgres` |
| Grafana OAuth fails | Verify `KC_HOSTNAME` and `GF_HOSTNAME` match your setup |
| Metrics not appearing | Check Prometheus targets: http://localhost:9090/targets |

### Health Checks
```bash
# PostgreSQL
docker compose exec postgres pg_isready -U keycloak

# Prometheus
curl http://localhost:9090/-/healthy

# Grafana
curl http://localhost:3000/api/health
```

## Resources

- [Keycloak Documentation](https://www.keycloak.org/documentation)
- [Keycloak Metrics](https://www.keycloak.org/server/configuration-metrics)
- [Prometheus Querying](https://prometheus.io/docs/prometheus/latest/querying/basics/)
- [Grafana Dashboards](https://grafana.com/grafana/dashboards/)
- [Docker Compose Reference](https://docs.docker.com/compose/compose-file/)

# claude-code-observability

A self-hosted monitoring stack for [Claude Code](https://claude.ai/code). Claude Code already emits OpenTelemetry data; this stack gives you somewhere useful to send it.

**Stack:** OpenTelemetry Collector → Prometheus (metrics) + Loki (logs) → Grafana

## What you get

- Token usage and cost burn rate by model and session
- API latency (avg, P99) and error rates
- Context window proximity and compaction events
- Tool performance — success rate and duration per tool
- Cache efficiency (input vs cache read token ratio)
- Per-session and per-developer cost breakdowns
- Code output — lines added/removed and commit rate

## Requirements

- Docker and Docker Compose
- Claude Code with OTEL export configured (see below)

## Setup

**1. Clone and start the stack:**

```bash
git clone https://github.com/KB1SLN-Labs/claude-code-observability.git
cd claude-code-observability
docker compose up -d
```

**2. Configure Claude Code to export telemetry:**

Add the following to your Claude Code `settings.json` (usually at `~/.claude/settings.json`):

```json
{
  "env": {
    "OTEL_EXPORTER_OTLP_ENDPOINT": "http://localhost:4318",
    "OTEL_EXPORTER_OTLP_PROTOCOL": "http/protobuf"
  }
}
```

Restart Claude Code after saving.

**3. Open Grafana:**

Navigate to [http://localhost:3000](http://localhost:3000). Dashboards load automatically — no login required.

## Data retention

Prometheus retains 30 days of metrics by default. Loki retains logs until disk pressure triggers cleanup. Both can be adjusted in `docker-compose.yml`.

## Ports

| Port | Service |
|------|---------|
| 3000 | Grafana |
| 4317 | OTLP gRPC (collector) |
| 4318 | OTLP HTTP (collector) |
| 9090 | Prometheus |
| 3100 | Loki |

## Stopping

```bash
docker compose down
```

To remove all stored data:

```bash
docker compose down -v
```

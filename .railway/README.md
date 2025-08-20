# ClickHouse Railway Deployment

This directory contains configuration files for deploying ClickHouse on [Railway](https://railway.app).

## One-Click Deploy

Deploy ClickHouse to Railway using the pre-configured template:

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/deploy/BOypRG?referralCode=CG2P3Y&utm_medium=integration&utm_source=template&utm_campaign=generic)

**Template URLs:**
- **Direct Deploy:** https://railway.com/deploy/BOypRG?referralCode=CG2P3Y&utm_medium=integration&utm_source=template&utm_campaign=generic
- **Marketplace:** https://railway.com/deploy/clickhouse-1

## Manual Deployment

1. **Deploy to Railway**
   ```bash
   railway up --service clickhouse
   ```

2. **Test the deployment**
   ```bash
   curl -u "default:your_password" "https://your-app.railway.app/" -d "SELECT 1"
   ```

## Configuration

### Environment Variables

Configure these in Railway's dashboard or via CLI:

| Variable | Description | Default | Required |
|----------|-------------|---------|----------|
| `CLICKHOUSE_IMAGE_TAG` | ClickHouse Docker image tag ([available tags](https://hub.docker.com/r/clickhouse/clickhouse-server/tags)) | `25.7.4.11` | No |
| `CLICKHOUSE_USER` | Username to create (replaces 'default' user) | `default` | No |
| `CLICKHOUSE_PASSWORD` | User password (required for network access) | *(none)* | **Yes** |
| `CLICKHOUSE_DB` | Database to create on startup | *(none)* | No |
| `CLICKHOUSE_ACCESS_MANAGEMENT` | Grant user access management privileges (0/1) | `0` | No |
| `CLICKHOUSE_SKIP_USER_SETUP` | Skip user configuration - WARNING: Security risk (0/1) | `0` | No |

### Image Tag Management

Change ClickHouse Docker image without modifying code:
```bash
# Set in Railway dashboard
CLICKHOUSE_IMAGE_TAG=24.8.1.1
```

Supported tag formats:
- `25.7.4.11` - Specific version
- `25.7.4.11-alpine` - Alpine variant
- `latest` - Latest stable (not recommended for production)
- `head` - Development build
- `24.8-lts` - LTS branch

## Files

- `Dockerfile` - Builds ClickHouse container with Railway-specific configuration
- `network.xml` - Network configuration for Railway's dynamic PORT binding
- `../railway.json` - Railway deployment configuration with health check settings

## Testing

### Health Check

Railway automatically monitors the `/ping` endpoint (configured in `railway.json`).

Manual test:
```bash
curl "https://your-app.railway.app/ping"
# Expected: Ok.
```

### Query Execution
```bash
curl -u "default:password" "https://your-app.railway.app/" \
  -d "SELECT version()"
```

### Database Connection
```bash
clickhouse-client \
  --host your-app.railway.app \
  --port 443 \
  --secure \
  --user default \
  --password your_password
```

## Troubleshooting

### 502 Bad Gateway
- **Cause**: ClickHouse not binding to Railway's PORT
- **Fix**: Ensure `network.xml` is properly configured

### Logs Show as "error" Level
- **Cause**: ClickHouse outputs startup messages to stderr, Railway interprets all stderr as errors
- **Impact**: Cosmetic only - these are informational messages, not actual errors
- **Example messages that appear as errors but are normal**:
  - `Processing configuration file '/etc/clickhouse-server/config.xml'`
  - `Merging configuration file '/etc/clickhouse-server/config.d/railway.xml'`
  - `Logging trace to /var/log/clickhouse-server/clickhouse-server.log`
- **Note**: This is a Railway platform limitation and doesn't affect ClickHouse functionality

## Resources

- [ClickHouse Documentation](https://clickhouse.com/docs)
- [Railway Documentation](https://docs.railway.app)
- [ClickHouse Docker Hub](https://hub.docker.com/r/clickhouse/clickhouse-server)
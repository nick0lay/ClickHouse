# ClickHouse Railway Deployment

This directory contains configuration files for deploying ClickHouse on [Railway](https://railway.app).

## One-Click Deploy

Deploy ClickHouse to Railway using the pre-configured template:

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/deploy/PmxhM-?referralCode=CG2P3Y)

**Template URL:** https://railway.com/deploy/PmxhM-?referralCode=CG2P3Y

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
| `CLICKHOUSE_DEFAULT_ACCESS_MANAGEMENT` | Grant user access management privileges (0/1) | `0` | No |

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

### Authentication Failed
- **Cause**: Password not set or incorrect
- **Fix**: Set `CLICKHOUSE_PASSWORD` in Railway environment variables

### Container Restart Loop
- **Cause**: Configuration error or resource limits
- **Fix**: Check Railway logs: `railway logs`

### Logs Show as "error" Level
- **Cause**: ClickHouse outputs startup messages to stderr, Railway interprets all stderr as errors
- **Impact**: Cosmetic only - these are informational messages, not actual errors
- **Example messages that appear as errors but are normal**:
  - `Processing configuration file '/etc/clickhouse-server/config.xml'`
  - `Merging configuration file '/etc/clickhouse-server/config.d/railway.xml'`
  - `Logging trace to /var/log/clickhouse-server/clickhouse-server.log`
- **Note**: This is a Railway platform limitation and doesn't affect ClickHouse functionality

## Security Notes

1. **Always set a strong password** for production deployments
2. **Avoid using** Railway's `${{secret()}}` template (generates new password each time)
3. **Generate secure passwords**:
   ```bash
   openssl rand -base64 32
   ```

## Advanced Configuration

To add custom ClickHouse configuration:

1. Create additional XML config files in `.railway/`
2. Add COPY instructions to `Dockerfile`
3. Files will be merged with default configuration

Example custom config:
```xml
<clickhouse>
    <max_concurrent_queries>100</max_concurrent_queries>
    <max_memory_usage>10737418240</max_memory_usage>
</clickhouse>
```

## Resources

- [ClickHouse Documentation](https://clickhouse.com/docs)
- [Railway Documentation](https://docs.railway.app)
- [ClickHouse Docker Hub](https://hub.docker.com/r/clickhouse/clickhouse-server)
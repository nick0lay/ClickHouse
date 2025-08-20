# Railway Template Publishing Information

## Short Description (75 characters)
Deploy ClickHouse OLAP database with one click - version-agnostic & secure

## Template Overview

# Deploy and Host ClickHouse on Railway

ClickHouse is an open-source column-oriented database for real-time analytics, processing billions of rows per second with 100-1000x faster query performance than traditional databases for analytical workloads.

## About Hosting ClickHouse

Hosting ClickHouse requires server configuration, dependency management, networking setup, and security hardening. This template eliminates that complexity with a pre-configured deployment using official Docker images. Set a password, choose any ClickHouse version, and deploy instantly—no Docker expertise or infrastructure management required.

## Common Use Cases

- **Real-time Analytics**: Process streaming data from applications, IoT devices, and logs
- **Business Intelligence**: Power dashboards with sub-second responses on massive datasets  
- **Time-series Analysis**: Store and query metrics, events, and monitoring data efficiently
- **Data Warehousing**: Replace traditional warehouses with faster analytical performance

## Dependencies for ClickHouse Hosting

- **Official ClickHouse Images**: Uses `clickhouse/clickhouse-server` from Docker Hub
- **Secure Authentication**: Requires password for network access
- **Persistent Storage**: Automatic data persistence via Railway volumes

### Deployment Dependencies

- **ClickHouse Docker Tags**: https://hub.docker.com/r/clickhouse/clickhouse-server/tags
- **ClickHouse Documentation**: https://clickhouse.com/docs

### Implementation Details

**Key Differentiator - Version Flexibility:**
Unlike fixed-version deployments, this template supports any ClickHouse Docker tag:

```bash
CLICKHOUSE_IMAGE_TAG=latest           # Latest stable (default, recommended)
CLICKHOUSE_IMAGE_TAG=25.7.4.11        # Specific version
CLICKHOUSE_IMAGE_TAG=25.7.4.11-alpine # Alpine variant
CLICKHOUSE_IMAGE_TAG=25.7             # Latest patch in branch
CLICKHOUSE_IMAGE_TAG=head             # Development build
```

**Environment Variables:**
- `CLICKHOUSE_PASSWORD` (Required): User password for database access
- `CLICKHOUSE_IMAGE_TAG` (Optional): ClickHouse Docker image tag ([available tags](https://hub.docker.com/r/clickhouse/clickhouse-server/tags)) (default: latest)
- `CLICKHOUSE_USER` (Optional): Username to create (default: default)
- `CLICKHOUSE_DB` (Optional): Database to create on startup
- `CLICKHOUSE_ACCESS_MANAGEMENT` (Optional): Grant user access management privileges (default: 0)
- `CLICKHOUSE_SKIP_USER_SETUP` (Optional): Skip user configuration - WARNING: Security risk (default: 0)

## Why Deploy ClickHouse on Railway?

<!-- Recommended: Keep this section as shown below -->
Railway is a singular platform to deploy your infrastructure stack. Railway will host your infrastructure so you don't have to deal with configuration, while allowing you to vertically scale your ClickHouse instance as your data grows.

By deploying ClickHouse on Railway, you are one step closer to supporting a complete full-stack application with minimal burden. Host your servers, databases, AI agents, and more on Railway.
<!-- End recommended section -->
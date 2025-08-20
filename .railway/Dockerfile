# Use official pre-built ClickHouse image (no building from source), could be 
# overridden by Railway environment variable `CLICKHOUSE_VERSION`
ARG CLICKHOUSE_VERSION=25.7.4.11
FROM clickhouse/clickhouse-server:${CLICKHOUSE_VERSION}

# Copy network configuration to force external binding
COPY .railway/network.xml /etc/clickhouse-server/config.d/railway.xml

# Railway requires explicit port exposure
EXPOSE 8123
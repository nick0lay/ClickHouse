# Use official pre-built ClickHouse image (no building from source)
# Can be overridden by Railway environment variable `CLICKHOUSE_IMAGE_TAG`
ARG CLICKHOUSE_IMAGE_TAG
FROM clickhouse/clickhouse-server:${CLICKHOUSE_IMAGE_TAG:-25.7.4.11}

# Copy network configuration to force external binding
COPY .railway/network.xml /etc/clickhouse-server/config.d/railway.xml

# Railway requires explicit port exposure
EXPOSE 8123
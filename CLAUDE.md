# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Common Development Commands

### Building ClickHouse

```bash
# Create build directory
mkdir build
cd build

# Configure CMake (Debug build recommended for development)
cmake -DCMAKE_BUILD_TYPE=Debug ..

# Build with Ninja (preferred)
ninja

# Build with specific parallelism
ninja -j8

# Minimal build (without optional libraries)
cmake -DCMAKE_BUILD_TYPE=Debug -DENABLE_LIBRARIES=0 ..

# Build without Rust components
cmake -DCMAKE_BUILD_TYPE=Debug -DENABLE_RUST=0 ..
```

### Running ClickHouse

```bash
# Start server (from build directory)
./programs/clickhouse server

# Connect with client
./programs/clickhouse client
```

### Testing

```bash
# Run a single functional test
./tests/clickhouse-test 01428_hash_set_nan_key

# Run all functional tests
./tests/clickhouse-test

# Run tests matching a pattern
./tests/clickhouse-test substring

# Run unit tests
cd build && ninja test

# Run integration tests (requires Docker)
cd tests/integration
./runner --parallel 4 --tmp test_name
```

### Code Quality

```bash
# Run clang-tidy (if configured)
ninja clickhouse-tidy

# Format code
clang-format -i src/path/to/file.cpp
```

### Git Workflow

**IMPORTANT**: This repository has two remotes:
- `origin`: The fork repository (where PRs should be created)
- `upstream`: The main ClickHouse repository (for syncing updates)

**Always create pull requests to `origin`, NOT to `upstream`.**

```bash
# Check your remotes
git remote -v

# Create a new branch for your work
git checkout -b feature-branch

# Make changes and commit
git add .
git commit -m "Description of changes"

# Push to origin (NOT upstream)
git push origin feature-branch

# Create PR using gh CLI to origin
gh pr create --repo nick0lay/ClickHouse

# Sync with upstream main branch
git fetch upstream
git checkout master
git merge upstream/master
git push origin master
```

## Railway Deployment

ClickHouse can be deployed to Railway cloud platform using the configuration in `.railway/` directory.

### Quick Deploy

```bash
# Link to Railway project
railway link -p your-project-name

# Deploy to Railway
railway up --service clickhouse

# Check deployment status
railway status

# View logs
railway logs
```

### Required Environment Variables

Set these in Railway dashboard or CLI:

- `CLICKHOUSE_PASSWORD` - Database password (required)
- `CLICKHOUSE_IMAGE_TAG` - ClickHouse Docker image tag (default: latest)
- `CLICKHOUSE_USER` - Username (default: default)
- `CLICKHOUSE_DB` - Default database to create

### Testing Deployment

```bash
# Health check
curl "https://your-app.railway.app/ping"

# Execute query
curl -u "default:password" "https://your-app.railway.app/" -d "SELECT 1"

# Check version
curl -u "default:password" "https://your-app.railway.app/" -d "SELECT version()"
```

### Image Tag Management

Change ClickHouse Docker image via environment variable without code changes:

```bash
# Set in Railway dashboard
CLICKHOUSE_IMAGE_TAG=24.8.1.1

# Redeploy
railway up --service clickhouse
```

See `.railway/README.md` for detailed Railway deployment documentation.

## High-Level Architecture

ClickHouse is a column-oriented database management system optimized for OLAP workloads. The codebase is organized as follows:

### Core Components

- **src/Server/**: Main server implementation, HTTP/TCP handlers, and server configuration
- **src/Client/**: Client implementation and connection handling
- **src/Storages/**: Storage engines (MergeTree family, Memory, Distributed, etc.)
- **src/Interpreters/**: Query execution engine, AST interpretation, and optimization
- **src/Parsers/**: SQL parser implementation
- **src/Functions/**: Built-in SQL functions and operators
- **src/AggregateFunctions/**: Aggregate function implementations
- **src/DataTypes/**: Data type system and serialization
- **src/Processors/**: Query pipeline and data processing framework
- **src/IO/**: Input/output abstractions, compression, and network protocols
- **src/Common/**: Shared utilities, data structures, and platform abstractions
- **src/Core/**: Core types, settings, and block structures
- **src/Columns/**: Column storage implementations
- **src/Formats/**: Data format parsers and serializers
- **src/Dictionaries/**: External dictionary support
- **src/Access/**: Authentication, authorization, and access control

### Query Execution Flow

1. **Parsing**: SQL query → AST (Abstract Syntax Tree) via `src/Parsers/`
2. **Analysis**: AST analysis and optimization via `src/Analyzer/` (new) or `src/Interpreters/` (legacy)
3. **Planning**: Query plan generation via `src/Planner/`
4. **Execution**: Pipeline-based execution via `src/Processors/`
5. **Storage**: Data reading/writing through storage engines in `src/Storages/`

### Storage Architecture

- **MergeTree**: Primary storage engine family optimized for analytical queries
  - Data stored in parts (immutable segments)
  - Background merges consolidate parts
  - Primary key index for efficient filtering
  - Column-oriented storage with compression
- **Distributed**: Sharding and replication coordination
- **ReplicatedMergeTree**: Automatic replication via ZooKeeper/Keeper

### Testing Infrastructure

- **Functional tests**: SQL-based tests in `tests/queries/`
- **Integration tests**: Python-based multi-node tests in `tests/integration/`
- **Unit tests**: Google Test-based tests throughout the codebase
- **Performance tests**: Benchmarks in `tests/performance/`
- **Stress tests**: Long-running stability tests
- **Fuzz tests**: Fuzzing harnesses in `tests/fuzz/`

### Build System

- CMake-based build system
- Supports multiple compilers (Clang recommended)
- Optional Rust components for certain features
- Extensive use of third-party libraries in `contrib/`
- Platform support: Linux (primary), macOS, FreeBSD

### Key Design Principles

- **Vectorized execution**: Process data in batches for CPU efficiency
- **Column storage**: Optimize for analytical queries
- **Parallel processing**: Utilize all available CPU cores
- **Memory efficiency**: Streaming processing, avoid loading entire datasets
- **Type safety**: Strong typing throughout the codebase
- **Zero-copy**: Minimize data copying where possible
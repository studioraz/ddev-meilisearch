# ddev-meilisearch

[![add-on registry](https://img.shields.io/badge/DDEV-Add--on_Registry-blue)](https://addons.ddev.com)
[![tests](https://github.com/itaymesh/ddev-meilisearch/actions/workflows/tests.yml/badge.svg?branch=main)](https://github.com/itaymesh/ddev-meilisearch/actions/workflows/tests.yml?query=branch%3Amain)

## Overview

[Meilisearch](https://www.meilisearch.com/) is a fast, open-source search engine that delivers instant, typo-tolerant full-text search with a simple HTTP API.

This add-on integrates Meilisearch into your [DDEV](https://ddev.com) project, providing a persistent local search service accessible from both your application and your browser.

## Installation

```bash
ddev add-on get itaymesh/ddev-meilisearch
ddev restart
```

After installation, make sure to commit the `.ddev` directory to version control.

## Usage

| Command | Description |
|---|---|
| `ddev launch :7700` | Open Meilisearch in your browser |
| `ddev describe` | View service status and used ports |
| `ddev logs -s meilisearch` | View Meilisearch logs |

The Meilisearch API is available at:

- **HTTP**: `http://<project>.ddev.site:7700`
- **HTTPS**: `https://<project>.ddev.site:7700`

### Health check

```bash
curl https://<project>.ddev.site:7700/health
# {"status":"available"}
```

### Searching (example)

```bash
# Add documents
curl -X POST 'https://<project>.ddev.site:7700/indexes/movies/documents' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer masterKey' \
  --data '[{"id": 1, "title": "Carol"}]'

# Search
curl 'https://<project>.ddev.site:7700/indexes/movies/search?q=carol' \
  -H 'Authorization: Bearer masterKey'
```

## Configuration

### Master Key

By default, the master key is set to `masterKey`. To use a custom master key, set the `MEILISEARCH_MASTER_KEY` environment variable in `.ddev/.env.meilisearch`:

```bash
ddev dotenv set .ddev/.env.meilisearch --meilisearch-master-key="your-secure-key"
ddev restart
```

Make sure to commit `.ddev/.env.meilisearch` to version control.

### Meilisearch Version

To pin a specific Meilisearch version:

```bash
ddev dotenv set .ddev/.env.meilisearch --meilisearch-tag="v1.8"
ddev add-on get itaymesh/ddev-meilisearch
ddev restart
```

### All customization options

| Variable | Flag | Default |
|---|---|---|
| `MEILISEARCH_TAG` | `--meilisearch-tag` | `latest` |
| `MEILISEARCH_MASTER_KEY` | `--meilisearch-master-key` | `masterKey` |
| `MEILISEARCH_ENV` | `--meilisearch-env` | `development` |

## Data Persistence

Meilisearch data is stored in a Docker named volume (`meilisearch`) and survives `ddev restart`. To completely reset the Meilisearch data:

```bash
ddev stop
docker volume rm ddev-$(ddev status -j | docker run -i --rm ddev/ddev-utilities jq -r '.raw.name')_meilisearch
ddev start
```

## Credits

**Contributed and maintained by [@itaymesh](https://github.com/itaymesh)**

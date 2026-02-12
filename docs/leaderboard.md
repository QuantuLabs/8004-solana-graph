# Leaderboard (GraphQL v2)

The GraphQL API exposes ordering and filtering primitives that can be used to build leaderboards.

If you need the legacy deterministic `sort_key` ranking, refer to the legacy REST v1 docs and enable `API_MODE=hybrid` on your self-hosted indexer.

## Endpoint

```
POST /v2/graphql
```

All examples below assume:

```bash
GRAPHQL_URL="https://8004-indexer-production.up.railway.app/v2/graphql"
```

## Examples

### Top agents by quality score (ATOM-enabled)

```bash
curl -sS "$GRAPHQL_URL" \
  -H "content-type: application/json" \
  --data '{
    "query":"query { agents(first: 20, where: { atomEnabled: true, trustTier_gte: 2 }, orderBy: qualityScore, orderDirection: desc) { id owner totalFeedback solana { trustTier qualityScore confidence riskScore diversityRatio } } }"
  }'
```

### Top agents by total feedback (activity leaderboard)

```bash
curl -sS "$GRAPHQL_URL" \
  -H "content-type: application/json" \
  --data '{
    "query":"query { agents(first: 20, orderBy: totalFeedback, orderDirection: desc) { id owner totalFeedback solana { trustTier qualityScore } } }"
  }'
```

### Top agents in one collection

```bash
curl -sS "$GRAPHQL_URL" \
  -H "content-type: application/json" \
  --data '{
    "query":"query($collection: String!) { agents(first: 20, where: { collection: $collection }, orderBy: qualityScore, orderDirection: desc) { id owner totalFeedback solana { collection trustTier qualityScore } } }",
    "variables": { "collection": "COLLECTION_PUBKEY" }
  }'
```

# Stats (GraphQL v2)

Global and per-agent statistics.

## Endpoint

```
POST /v2/graphql
```

All examples below assume:

```bash
GRAPHQL_URL="https://8004-indexer-production.up.railway.app/v2/graphql"
```

## Queries

- `protocols(first, skip): [Protocol!]!` (global rollups for the current network)
- `globalStats(id: ID!): GlobalStats` (global rollups)
- `agentStats(id: ID!): AgentStats` (per-agent aggregates)

## Examples

### Global protocol rollups

```bash
curl -sS "$GRAPHQL_URL" \
  -H "content-type: application/json" \
  --data '{
    "query":"query { protocols { id totalAgents totalFeedback totalValidations tags } }"
  }'
```

### Global stats (explicit ID)

```bash
curl -sS "$GRAPHQL_URL" \
  -H "content-type: application/json" \
  --data '{
    "query":"query($id: ID!) { globalStats(id: $id) { id totalAgents totalFeedback totalValidations totalProtocols tags } }",
    "variables": { "id": "solana-devnet" }
  }'
```

### Per-agent stats

```bash
curl -sS "$GRAPHQL_URL" \
  -H "content-type: application/json" \
  --data '{
    "query":"query($id: ID!) { agentStats(id: $id) { id totalFeedback averageFeedbackValue totalValidations completedValidations averageValidationScore lastActivity } }",
    "variables": { "id": "sol:ASSET_PUBKEY" }
  }'
```


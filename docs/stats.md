# Stats (GraphQL v2)

Global and per-agent statistics.

## Endpoint

```http
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

Response (example):

```json
{
  "data": {
    "protocols": [
      {
        "id": "solana-devnet",
        "totalAgents": "136",
        "totalFeedback": "420",
        "totalValidations": "0",
        "tags": ["tag_a", "tag_b"]
      }
    ]
  }
}
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

Response (example):

```json
{
  "data": {
    "globalStats": {
      "id": "solana-devnet",
      "totalAgents": "136",
      "totalFeedback": "420",
      "totalValidations": "0",
      "totalProtocols": "1",
      "tags": ["tag_a", "tag_b"]
    }
  }
}
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

Response (example):

```json
{
  "data": {
    "agentStats": {
      "id": "sol:ASSET_PUBKEY",
      "totalFeedback": "12",
      "averageFeedbackValue": "95.00",
      "totalValidations": "0",
      "completedValidations": "0",
      "averageValidationScore": null,
      "lastActivity": "1700000000"
    }
  }
}
```

# Collections (GraphQL v2)

Agents include a `collection` field (Metaplex collection pubkey).

Current 8004 deployments use a **single** collection for agents (per network).
The `collection` field is still exposed for completeness and debugging, but most clients can ignore it.

If you still want to read it, query any agent and select `solana.collection`.

## Endpoint

```http
POST /v2/graphql
```

All examples below assume:

```bash
GRAPHQL_URL="https://8004-indexer-production.up.railway.app/v2/graphql"
```

## Examples

### Get the current agent collection

```bash
curl -sS "$GRAPHQL_URL" \
  -H "content-type: application/json" \
  --data '{
    "query":"query { agents(first: 1, orderBy: createdAt, orderDirection: desc) { solana { collection } } }"
  }'
```

Response (example):

```json
{
  "data": {
    "agents": [
      {
        "solana": { "collection": "COLLECTION_PUBKEY" }
      }
    ]
  }
}
```

### Filter by collection (optional)

Most deployments will return the same results since there is only one collection, but the filter is available:

```bash
curl -sS "$GRAPHQL_URL" \
  -H "content-type: application/json" \
  --data '{
    "query":"query($collection: String!) { agents(first: 10, where: { collection: $collection }, orderBy: createdAt, orderDirection: desc) { id owner createdAt } }",
    "variables": { "collection": "COLLECTION_PUBKEY" }
  }'
```

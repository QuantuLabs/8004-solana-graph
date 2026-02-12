# Sub-Registries (Collections) (GraphQL v2)

The 8004 Agent Registry uses a sharded architecture: one logical registry backed by many on-chain collections.

In GraphQL v2, the indexer does not currently expose a "list collections" query.
Instead, you typically:

1. Discover a collection pubkey on-chain (via SDK / program calls)
2. Filter agents by `collection`

## Endpoint

```
POST /v2/graphql
```

All examples below assume:

```bash
GRAPHQL_URL="https://8004-indexer-production.up.railway.app/v2/graphql"
```

## Examples

### List agents in a collection

```bash
curl -sS "$GRAPHQL_URL" \
  -H "content-type: application/json" \
  --data '{
    "query":"query($collection: String!) { agents(first: 50, where: { collection: $collection }, orderBy: createdAt, orderDirection: desc) { id owner solana { collection } } }",
    "variables": { "collection": "COLLECTION_PUBKEY" }
  }'
```


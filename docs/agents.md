# Agents (GraphQL v2)

List registered agents (Metaplex Core assets) and their ATOM reputation stats.

## Endpoint

```
POST /v2/graphql
```

All examples below assume:

```bash
GRAPHQL_URL="https://8004-indexer-production.up.railway.app/v2/graphql"
```

## IDs

GraphQL uses namespaced IDs:

- Agent: `sol:<asset_pubkey>`

## Queries

- `agent(id: ID!): Agent`
- `agents(first, skip, where, orderBy, orderDirection): [Agent!]!`

## Filters

The `agents(where: AgentFilter)` input supports:

- `id`, `id_in`
- `owner`, `owner_in`
- `agentWallet`
- `collection`
- `atomEnabled`
- `trustTier_gte`
- `totalFeedback_gt`, `totalFeedback_gte`
- `createdAt_gt`, `createdAt_lt` (unix seconds)

## Ordering

Use `orderBy: AgentOrderBy`:

- `createdAt` (default)
- `updatedAt`
- `totalFeedback`
- `qualityScore`
- `trustTier`

## Examples

### Fetch one agent (with Solana extension)

```bash
curl -sS "$GRAPHQL_URL" \
  -H "content-type: application/json" \
  --data '{
    "query":"query($id: ID!) { agent(id: $id) { id owner agentURI agentWallet createdAt updatedAt totalFeedback solana { assetPubkey collection atomEnabled trustTier qualityScore confidence riskScore diversityRatio verificationStatus feedbackDigest responseDigest revokeDigest } } }",
    "variables": { "id": "sol:ASSET_PUBKEY" }
  }'
```

### Fetch one agent (with registration file)

```bash
curl -sS "$GRAPHQL_URL" \
  -H "content-type: application/json" \
  --data '{
    "query":"query($id: ID!) { agent(id: $id) { id owner agentURI registrationFile { name description image active mcpEndpoint mcpTools a2aEndpoint a2aSkills oasfSkills oasfDomains hasOASF } } }",
    "variables": { "id": "sol:ASSET_PUBKEY" }
  }'
```

### List latest agents

```bash
curl -sS "$GRAPHQL_URL" \
  -H "content-type: application/json" \
  --data '{
    "query":"query($first: Int!) { agents(first: $first, orderBy: createdAt, orderDirection: desc) { id owner createdAt totalFeedback solana { trustTier qualityScore } } }",
    "variables": { "first": 10 }
  }'
```

### Agents by owner

```bash
curl -sS "$GRAPHQL_URL" \
  -H "content-type: application/json" \
  --data '{
    "query":"query($owner: String!) { agents(first: 20, where: { owner: $owner }, orderBy: createdAt, orderDirection: desc) { id owner createdAt } }",
    "variables": { "owner": "OWNER_WALLET" }
  }'
```

### Agents in one collection

```bash
curl -sS "$GRAPHQL_URL" \
  -H "content-type: application/json" \
  --data '{
    "query":"query($collection: String!) { agents(first: 20, where: { collection: $collection }, orderBy: createdAt, orderDirection: desc) { id owner solana { collection } } }",
    "variables": { "collection": "COLLECTION_PUBKEY" }
  }'
```

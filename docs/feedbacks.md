# Feedbacks (GraphQL v2)

List feedback events (client reviews of agents).

## Endpoint

```
POST /v2/graphql
```

All examples below assume:

```bash
GRAPHQL_URL="https://8004-indexer-production.up.railway.app/v2/graphql"
```

## IDs

- Agent: `sol:<asset_pubkey>`
- Feedback: `sol:<asset>:<client>:<feedback_index>`

## Queries

- `feedback(id: ID!): Feedback`
- `feedbacks(first, skip, where, orderBy, orderDirection): [Feedback!]!`

## Filters

The `feedbacks(where: FeedbackFilter)` input supports:

- `agent` (Agent ID)
- `clientAddress`
- `tag1`, `tag2`
- `endpoint`
- `isRevoked`
- `createdAt_gt`, `createdAt_lt` (unix seconds)

## Examples

### Feedbacks for one agent

```bash
curl -sS "$GRAPHQL_URL" \
  -H "content-type: application/json" \
  --data '{
    "query":"query($agent: ID!) { feedbacks(first: 20, where: { agent: $agent }, orderBy: createdAt, orderDirection: desc) { id clientAddress feedbackIndex value isRevoked createdAt solana { score valueRaw valueDecimals txSignature blockSlot runningDigest verificationStatus } } }",
    "variables": { "agent": "sol:ASSET_PUBKEY" }
  }'
```

### Feedbacks by endpoint

```bash
curl -sS "$GRAPHQL_URL" \
  -H "content-type: application/json" \
  --data '{
    "query":"query($endpoint: String!) { feedbacks(first: 20, where: { endpoint: $endpoint }, orderBy: createdAt, orderDirection: desc) { id agent { id } clientAddress tag1 tag2 createdAt } }",
    "variables": { "endpoint": "https://example.com/api" }
  }'
```

### Get one feedback (and first page of responses)

```bash
curl -sS "$GRAPHQL_URL" \
  -H "content-type: application/json" \
  --data '{
    "query":"query($id: ID!) { feedback(id: $id) { id agent { id } clientAddress feedbackIndex value tag1 tag2 endpoint feedbackURI feedbackHash isRevoked createdAt revokedAt responses(first: 10) { id responder responseUri createdAt } solana { score txSignature blockSlot runningDigest } } }",
    "variables": { "id": "sol:ASSET:CLIENT:0" }
  }'
```


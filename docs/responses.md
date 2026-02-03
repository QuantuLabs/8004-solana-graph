# Responses API

List feedback responses (agent/owner replies to client feedback).

## Endpoints

```
GET /rest/v1/responses
GET /rest/v1/feedback_responses
```

Both endpoints are identical (SDK compatibility).

## Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `feedback_id` | string | Filter by feedback ID |
| `asset` | string | Filter by agent asset ID |
| `client_address` | string | Filter by original feedback client |
| `feedback_index` | string | Filter by feedback index |
| `status` | string | Verification status filter |
| `limit` | number | Max results (default: 100) |
| `offset` | number | Pagination offset |

**Note:** When filtering by `asset`, `client_address`, and `feedback_index`, the API first looks for the parent feedback, then returns its responses. If the feedback isn't indexed yet, it returns orphan responses.

## Response Schema

```typescript
interface FeedbackResponse {
  id: string;                    // Unique response ID
  feedback_id: string | null;    // Parent feedback ID (null for orphans)
  asset: string;                 // Agent asset pubkey
  client_address: string;        // Original feedback client
  feedback_index: string;        // Parent feedback index (BigInt as string)
  responder: string;             // Who responded (owner/agent wallet)
  response_uri: string | null;   // Response content URI
  response_hash: string | null;  // SHA-256 of response (hex)
  running_digest: string | null; // Hash-chain digest (hex)
  status: string;                // PENDING | FINALIZED | ORPHANED
  verified_at: string | null;    // When verified
  block_slot: number;            // Solana slot
  tx_signature: string;          // Transaction signature
  created_at: string;            // ISO timestamp
}
```

## Examples

### Get responses for a feedback
```bash
curl "https://api.example.com/rest/v1/responses?feedback_id=eq.FeedbackId123"
```

### Get responses by agent + client + index
```bash
curl "https://api.example.com/rest/v1/responses?asset=eq.Agent&client_address=eq.Client&feedback_index=eq.0"
```

### Get all responses for an agent
```bash
curl "https://api.example.com/rest/v1/feedback_responses?asset=eq.AgentPubkey"
```

## Response Example

```json
[
  {
    "id": "resp_abc123...",
    "feedback_id": "8Rz4Nqk...:5FHwkrd...:0",
    "asset": "8Rz4NqkPsXdVNgmSi2wYBRJbT123...",
    "client_address": "5FHwkrdxntdK4N6Z4gXQ...",
    "feedback_index": "0",
    "responder": "7YmP3kL2nQwR...",
    "response_uri": "ipfs://QmResponse...",
    "response_hash": "b2c3d4e5...",
    "running_digest": "a1b2c3d4...",
    "status": "FINALIZED",
    "verified_at": "2024-01-15T11:00:00.000Z",
    "block_slot": 123456800,
    "tx_signature": "6Uz0yL...",
    "created_at": "2024-01-15T10:30:00.000Z"
  }
]
```

## Orphan Responses

When a response is indexed before its parent feedback (e.g., due to indexer restart), it's stored as an "orphan response" and returned with `feedback_id: null`. Once the parent feedback is indexed, orphans are reconciled.

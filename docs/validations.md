# Validations API

List validation requests and responses (third-party certifications).

## Endpoint

```
GET /rest/v1/validations
```

## Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `asset` | string | Filter by agent asset ID |
| `validator` | string | Filter by validator address |
| `nonce` | number | Filter by nonce (allows multiple validations) |
| `responded` | boolean | Filter by response status |
| `status` | string | Chain status filter |
| `limit` | number | Max results (default: 100) |
| `offset` | number | Pagination offset |

## Response Schema

```typescript
interface Validation {
  id: string;                    // Composite ID: asset:validator:nonce
  asset: string;                 // Agent asset pubkey
  validator_address: string;     // Validator pubkey
  nonce: number;                 // Allows multiple validations from same validator
  requester: string;             // Who requested the validation
  request_uri: string | null;    // Request details URI
  request_hash: string | null;   // SHA-256 of request (hex)
  response: number | null;       // Validation score 0-100 (null if pending)
  response_uri: string | null;   // Response details URI
  response_hash: string | null;  // SHA-256 of response (hex)
  tag: string | null;            // Validation type tag (e.g., "oasf-v0.8.0")
  status: "PENDING" | "RESPONDED"; // Request status
  chain_status: string;          // PENDING | FINALIZED | ORPHANED
  chain_verified_at: string | null;
  block_slot: number;            // Solana slot
  tx_signature: string;          // Transaction signature
  created_at: string;            // ISO timestamp (request time)
  updated_at: string;            // ISO timestamp (response time or request time)
}
```

## Examples

### Get validations for an agent
```bash
curl "https://api.example.com/rest/v1/validations?asset=eq.AgentPubkey"
```

### Get validations by a specific validator
```bash
curl "https://api.example.com/rest/v1/validations?validator=eq.ValidatorPubkey"
```

### Get only responded validations
```bash
curl "https://api.example.com/rest/v1/validations?responded=eq.true"
```

### Get pending validations
```bash
curl "https://api.example.com/rest/v1/validations?responded=eq.false"
```

### Get specific validation
```bash
curl "https://api.example.com/rest/v1/validations?asset=eq.Agent&validator=eq.Validator&nonce=eq.0"
```

## Response Example

```json
[
  {
    "id": "8Rz4Nqk...:9KmLpQw...:0",
    "asset": "8Rz4NqkPsXdVNgmSi2wYBRJbT123...",
    "validator_address": "9KmLpQwR5tYz...",
    "nonce": 0,
    "requester": "5FHwkrdxntdK4N6Z4gXQ...",
    "request_uri": "ipfs://QmRequest...",
    "request_hash": "e5f6g7h8...",
    "response": 92,
    "response_uri": "ipfs://QmResponse...",
    "response_hash": "f6g7h8i9...",
    "tag": "oasf-v0.8.0",
    "status": "RESPONDED",
    "chain_status": "FINALIZED",
    "chain_verified_at": "2024-01-15T14:00:00.000Z",
    "block_slot": 123457000,
    "tx_signature": "8Wb2AN...",
    "created_at": "2024-01-15T13:00:00.000Z",
    "updated_at": "2024-01-15T13:30:00.000Z"
  }
]
```

## Validation Flow

1. **Request**: Requester creates validation request with `request_uri` and designated `validator`
2. **Response**: Validator responds with score (0-100) and optional `response_uri`
3. **Immutable**: Validations are permanent on-chain (no close/delete)

## Tags

The `tag` field categorizes validations:
- `oasf-v0.8.0` - OASF standard compliance
- Custom tags defined by validators

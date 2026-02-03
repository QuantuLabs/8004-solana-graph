# Revocations API

List feedback revocation events.

## Endpoint

```
GET /rest/v1/revocations
```

## Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `asset` | string | Filter by agent asset ID |
| `client` | string | Filter by client wallet |
| `order` | string | Sort order: `revoke_count.desc` |
| `status` | string | Verification status filter |
| `limit` | number | Max results (default: 100) |
| `offset` | number | Pagination offset |

## Response Schema

```typescript
interface Revocation {
  id: string;                    // Unique revocation ID
  asset: string;                 // Agent asset pubkey
  client_address: string;        // Client who revoked
  feedback_index: string;        // Which feedback was revoked (BigInt as string)
  feedback_hash: string | null;  // Hash of revoked feedback (hex)
  slot: number;                  // Solana slot when revoked
  original_score: number | null; // Score before revocation
  atom_enabled: boolean;         // Was ATOM enabled at revocation
  had_impact: boolean;           // Did revocation affect agent's reputation
  running_digest: string | null; // Hash-chain digest (hex)
  revoke_count: number;          // Total revocations by this client
  tx_signature: string;          // Transaction signature
  status: string;                // PENDING | FINALIZED | ORPHANED
  verified_at: string | null;    // When verified
  created_at: string;            // ISO timestamp
}
```

## Examples

### Get revocations for an agent
```bash
curl "https://api.example.com/rest/v1/revocations?asset=eq.AgentPubkey"
```

### Get revocations by client
```bash
curl "https://api.example.com/rest/v1/revocations?client=eq.ClientWallet"
```

### Order by most revocations
```bash
curl "https://api.example.com/rest/v1/revocations?order=revoke_count.desc"
```

## Response Example

```json
[
  {
    "id": "rev_abc123...",
    "asset": "8Rz4NqkPsXdVNgmSi2wYBRJbT123...",
    "client_address": "5FHwkrdxntdK4N6Z4gXQ...",
    "feedback_index": "5",
    "feedback_hash": "c3d4e5f6...",
    "slot": 123456900,
    "original_score": 45,
    "atom_enabled": true,
    "had_impact": true,
    "running_digest": "d4e5f6g7...",
    "revoke_count": 3,
    "tx_signature": "7Va1zM...",
    "status": "FINALIZED",
    "verified_at": "2024-01-15T12:00:00.000Z",
    "created_at": "2024-01-15T11:30:00.000Z"
  }
]
```

## Notes

- `had_impact` indicates whether the revocation changed the agent's ATOM reputation
- `revoke_count` tracks the total number of revocations by this client for any agent
- Revocations are irreversible on-chain

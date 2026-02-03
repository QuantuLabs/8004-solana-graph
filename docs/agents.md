# Agents API

List registered AI agents (Metaplex Core NFTs).

## Endpoint

```
GET /rest/v1/agents
```

## Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `id` | string | Filter by agent asset ID (Metaplex Core asset pubkey) |
| `owner` | string | Filter by owner wallet address |
| `collection` | string | Filter by collection address |
| `agent_wallet` | string | Filter by agent's signing wallet |
| `status` | string | Verification status filter |
| `limit` | number | Max results (default: 100, max: 1000) |
| `offset` | number | Pagination offset (max: 10000) |

## Response Schema

```typescript
interface Agent {
  asset: string;              // Metaplex Core asset pubkey (unique ID)
  owner: string;              // Current owner wallet
  agent_uri: string | null;   // Metadata URI (IPFS/Arweave)
  agent_wallet: string | null; // Agent's signing wallet
  collection: string;         // Collection pubkey
  nft_name: string | null;    // NFT display name
  atom_enabled: boolean;      // ATOM reputation engine enabled
  status: string;             // PENDING | FINALIZED | ORPHANED
  verified_at: string | null; // ISO timestamp when verified
  created_at: string;         // ISO timestamp
  updated_at: string;         // ISO timestamp
}
```

## Examples

### List all agents
```bash
curl "https://api.example.com/rest/v1/agents"
```

### Get agents by owner
```bash
curl "https://api.example.com/rest/v1/agents?owner=eq.WalletPubkey123"
```

### Get agents in a collection
```bash
curl "https://api.example.com/rest/v1/agents?collection=eq.CollectionPubkey"
```

### Get single agent by ID
```bash
curl "https://api.example.com/rest/v1/agents?id=eq.AgentAssetPubkey"
```

### Only finalized agents
```bash
curl "https://api.example.com/rest/v1/agents?status=eq.FINALIZED"
```

## Response Example

```json
[
  {
    "asset": "8Rz4NqkPsXdVNgmSi2wYBRJbT123...",
    "owner": "5FHwkrdxntdK4N6Z4gXQ...",
    "agent_uri": "https://arweave.net/abc123",
    "agent_wallet": "7YmP3kL2nQwR...",
    "collection": "9KmLpQwR5tYz...",
    "nft_name": "My AI Agent #42",
    "atom_enabled": true,
    "status": "FINALIZED",
    "verified_at": "2024-01-15T10:30:00.000Z",
    "created_at": "2024-01-15T10:00:00.000Z",
    "updated_at": "2024-01-15T10:30:00.000Z"
  }
]
```

# Registries API

List agent registries (collections).

## Endpoint

```
GET /rest/v1/registries
```

## Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `collection` | string | Filter by collection address |
| `status` | string | Verification status filter |
| `limit` | number | Max results (default: 100) |
| `offset` | number | Pagination offset |

## Response Schema

```typescript
interface Registry {
  id: string;                 // Collection pubkey
  collection: string;         // Collection pubkey (same as id)
  registryType: string;       // "BASE" or "USER"
  authority: string | null;   // Registry authority wallet
  slot: string | null;        // Creation slot (BigInt as string)
  txSignature: string | null; // Creation transaction
  status: string;             // PENDING | FINALIZED | ORPHANED
  verified_at: string | null; // When verified
  createdAt: string;          // ISO timestamp
  updatedAt: string;          // ISO timestamp
}
```

## Registry Types

| Type | Description |
|------|-------------|
| `BASE` | The main registry created during program initialization |
| `USER` | User-created custom registries |

## Examples

### List all registries
```bash
curl "https://api.example.com/rest/v1/registries"
```

### Get specific registry
```bash
curl "https://api.example.com/rest/v1/registries?collection=eq.CollectionPubkey"
```

### Only finalized registries
```bash
curl "https://api.example.com/rest/v1/registries?status=eq.FINALIZED"
```

## Response Example

```json
[
  {
    "id": "9KmLpQwR5tYz...",
    "collection": "9KmLpQwR5tYz...",
    "registryType": "BASE",
    "authority": "5FHwkrdxntdK4N6Z4gXQ...",
    "slot": "123450000",
    "txSignature": "3Xy4wZ...",
    "status": "FINALIZED",
    "verified_at": "2024-01-10T00:00:00.000Z",
    "createdAt": "2024-01-10T00:00:00.000Z",
    "updatedAt": "2024-01-10T00:00:00.000Z"
  },
  {
    "id": "7YmP3kL2nQwR...",
    "collection": "7YmP3kL2nQwR...",
    "registryType": "USER",
    "authority": "8Rz4NqkPsXdV...",
    "slot": "123455000",
    "txSignature": "4Yz5xA...",
    "status": "FINALIZED",
    "verified_at": "2024-01-12T00:00:00.000Z",
    "createdAt": "2024-01-12T00:00:00.000Z",
    "updatedAt": "2024-01-12T00:00:00.000Z"
  }
]
```

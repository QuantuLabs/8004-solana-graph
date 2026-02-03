# Metadata API

List agent metadata key-value pairs.

## Endpoint

```
GET /rest/v1/metadata
```

## Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `asset` | string | Filter by agent asset ID |
| `key` | string | Filter by metadata key |
| `status` | string | Verification status filter |
| `limit` | number | Max results (default: 100, max: 100) |

**Note:** Metadata has a stricter limit (100 vs 1000) because values can be large (up to 100KB each).

## Response Schema

```typescript
interface Metadata {
  id: string;              // Composite: asset:key_hash
  asset: string;           // Agent asset pubkey
  key: string;             // Metadata key (max 32 chars)
  value: string;           // Base64-encoded value
  immutable: boolean;      // Cannot be changed after first set
  status: string;          // PENDING | FINALIZED | ORPHANED
  verified_at: string | null;
}
```

## Examples

### Get all metadata for an agent
```bash
curl "https://api.example.com/rest/v1/metadata?asset=eq.AgentPubkey"
```

### Get specific key
```bash
curl "https://api.example.com/rest/v1/metadata?asset=eq.AgentPubkey&key=eq.description"
```

## Response Example

```json
[
  {
    "id": "8Rz4Nqk...:a1b2c3d4",
    "asset": "8Rz4NqkPsXdVNgmSi2wYBRJbT123...",
    "key": "description",
    "value": "VGhpcyBpcyBteSBBSSBhZ2VudA==",
    "immutable": false,
    "status": "FINALIZED",
    "verified_at": "2024-01-15T10:00:00.000Z"
  },
  {
    "id": "8Rz4Nqk...:e5f6g7h8",
    "asset": "8Rz4NqkPsXdVNgmSi2wYBRJbT123...",
    "key": "model",
    "value": "Z3B0LTQ=",
    "immutable": true,
    "status": "FINALIZED",
    "verified_at": "2024-01-15T10:00:00.000Z"
  }
]
```

## Decoding Values

Values are Base64-encoded. Decode in JavaScript:

```javascript
const decoded = atob(metadata.value);
// or
const decoded = Buffer.from(metadata.value, 'base64').toString('utf-8');
```

## Reserved Keys

Keys starting with `_uri:` are reserved for system-derived metadata from URI fetching:

| Key | Description |
|-----|-------------|
| `_uri:status` | URI fetch status |
| `_uri:name` | Extracted name |
| `_uri:description` | Extracted description |
| `_uri:image` | Extracted image URL |

## Size Limits

- **Key**: Max 32 bytes
- **Value**: Max 256 bytes on-chain (compressed, decompresses to ~100KB)
- **Response**: Max 10MB aggregate decompressed size per request

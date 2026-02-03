# Statistics API

Global and collection-level statistics.

## Endpoints

| Endpoint | Description |
|----------|-------------|
| `/rest/v1/stats` | Global statistics |
| `/rest/v1/global_stats` | Same as `/stats` (alias) |
| `/rest/v1/collection_stats` | Per-collection statistics |
| `/rest/v1/stats/verification` | Verification status breakdown |

---

## Global Stats

```
GET /rest/v1/stats
GET /rest/v1/global_stats
```

### Response Schema

```typescript
interface GlobalStats {
  total_agents: number;      // Total registered agents
  total_feedbacks: number;   // Total feedback events
  total_collections: number; // Total registries
  total_validations: number; // Total validation requests
}
```

### Example

```bash
curl "https://api.example.com/rest/v1/stats"
```

### Response

```json
[
  {
    "total_agents": 12547,
    "total_feedbacks": 2345678,
    "total_collections": 89,
    "total_validations": 4521
  }
]
```

---

## Collection Stats

```
GET /rest/v1/collection_stats
```

### Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `collection` | string | Specific collection (optional) |
| `order` | string | `agent_count.desc` to sort by size |

### Response Schema

```typescript
interface CollectionStats {
  collection: string;        // Collection pubkey
  registry_type: string;     // "BASE" or "USER"
  authority: string | null;  // Registry authority
  agent_count: number;       // Agents in collection
  total_feedbacks: number;   // Total feedbacks for agents in collection
  avg_score: number | null;  // Average feedback score
}
```

### Examples

#### All collections (sorted by agent count)
```bash
curl "https://api.example.com/rest/v1/collection_stats?order=agent_count.desc"
```

#### Specific collection
```bash
curl "https://api.example.com/rest/v1/collection_stats?collection=eq.CollectionPubkey"
```

### Response

```json
[
  {
    "collection": "9KmLpQwR5tYz...",
    "registry_type": "BASE",
    "authority": "5FHwkrdxntdK4N6Z4gXQ...",
    "agent_count": 8542,
    "total_feedbacks": 1847623,
    "avg_score": 78.5
  },
  {
    "collection": "7YmP3kL2nQwR...",
    "registry_type": "USER",
    "authority": "4EGvjrcxmtdH5M5Y3fPR...",
    "agent_count": 1205,
    "total_feedbacks": 234567,
    "avg_score": 82.3
  }
]
```

---

## Verification Stats

```
GET /rest/v1/stats/verification
```

Breakdown of verification status across all data types.

### Response Schema

```typescript
interface VerificationStats {
  agents: StatusCounts;
  feedbacks: StatusCounts;
  validations: StatusCounts;
  registries: StatusCounts;
  metadata: StatusCounts;
  feedback_responses: StatusCounts;
}

interface StatusCounts {
  PENDING: number;    // Awaiting verification
  FINALIZED: number;  // Verified on-chain
  ORPHANED: number;   // Invalidated by reorg
}
```

### Example

```bash
curl "https://api.example.com/rest/v1/stats/verification"
```

### Response

```json
{
  "agents": {
    "PENDING": 42,
    "FINALIZED": 12505,
    "ORPHANED": 3
  },
  "feedbacks": {
    "PENDING": 156,
    "FINALIZED": 2345522,
    "ORPHANED": 12
  },
  "validations": {
    "PENDING": 8,
    "FINALIZED": 4513,
    "ORPHANED": 0
  },
  "registries": {
    "PENDING": 0,
    "FINALIZED": 89,
    "ORPHANED": 0
  },
  "metadata": {
    "PENDING": 23,
    "FINALIZED": 45678,
    "ORPHANED": 5
  },
  "feedback_responses": {
    "PENDING": 12,
    "FINALIZED": 89012,
    "ORPHANED": 2
  }
}
```

---

## Caching

Collection stats are cached for 5 minutes to prevent expensive aggregation queries on every request.

## Limits

- `collection_stats` returns max 50 collections without filter
- Use `collection=eq.X` for specific collection stats

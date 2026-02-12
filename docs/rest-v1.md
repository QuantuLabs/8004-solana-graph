# REST v1 (Legacy)

This document keeps the legacy PostgREST-compatible API reference.

Default 8004 deployments now use GraphQL (`/v2/graphql`).
On GraphQL-only deployments, `/rest/v1/*` returns `410 Gone`.

## Base URL (REST v1)

| Deployment Type | Base URL |
|---|---|
| Legacy Supabase-hosted | `https://uhjytdjxvfbppgjicfly.supabase.co/rest/v1` |
| Self-hosted REST mode | `https://your-indexer.example.com/rest/v1` |

## Authentication

For the legacy Supabase-hosted endpoint, include an API key:

```bash
apikey: sb_publishable_i-ycBRGiolBr8GMdiVq1rA_nwt7N2bq
```

Example:

```bash
curl -H "apikey: sb_publishable_i-ycBRGiolBr8GMdiVq1rA_nwt7N2bq" \
  "https://uhjytdjxvfbppgjicfly.supabase.co/rest/v1/agents"
```

## Endpoint Reference

| Endpoint | Description | Documentation |
|---|---|---|
| `/agents` | List registered agents | [docs/agents.md](agents.md) |
| `/feedbacks` | List feedback events | [docs/feedbacks.md](feedbacks.md) |
| `/feedback_responses` | List feedback responses | [docs/responses.md](responses.md) |
| `/responses` | Alias for feedback responses | [docs/responses.md](responses.md) |
| `/validations` | List validation requests | [docs/validations.md](validations.md) |
| `/registries` | List sub-registries | [docs/registries.md](registries.md) |
| `/metadata` | Agent metadata key-value pairs | [docs/metadata.md](metadata.md) |
| `/leaderboard` | Top agents by trust score | [docs/leaderboard.md](leaderboard.md) |
| `/stats` and `/global_stats` | Global statistics | [docs/stats.md](stats.md) |
| `/collection_stats` | Per-collection statistics | [docs/stats.md](stats.md) |
| `/stats/verification` | Verification status breakdown | [docs/stats.md](stats.md) |
| `/checkpoints/:asset` | Hash-chain checkpoints | [docs/stats.md](stats.md) |
| `/events/:asset/replay-data` | Replay/event reconstruction data | [docs/stats.md](stats.md) |

## Pagination

| Parameter | Description | Default | Max |
|---|---|---|---|
| `limit` | Items per page | `100` | `1000` |
| `offset` | Skip N items | `0` | `10000` |

For total count (when supported), send:

```bash
Prefer: count=exact
```

## Verification Status Filters

- `PENDING` - ingested, waiting verification
- `FINALIZED` - verified on-chain
- `ORPHANED` - invalidated by reorg

Common query patterns:

- `?status=eq.FINALIZED`
- `?status=eq.PENDING`
- `?includeOrphaned=true` (to include orphaned rows)

## PostgREST Operators

| Operator | Example | Description |
|---|---|---|
| `eq` | `?owner=eq.ABC` | Equals |
| `neq` | `?status=neq.ORPHANED` | Not equals |
| `in` | `?feedback_index=in.(1,2,3)` | In list |

## Migration Recommendation

For new integrations, migrate to GraphQL:

- [`../README.md`](../README.md)

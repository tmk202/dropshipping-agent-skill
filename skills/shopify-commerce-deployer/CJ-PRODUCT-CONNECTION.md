# CJ Product Connection Contract

## Inputs

Required:

```text
CJ shop ID
Shopify platform product ID
Shopify platform variant IDs
CJ product ID
CJ variant IDs
Quantity multipliers for bundles
```

## Mapping priority

```text
platformProductId
→ platformVariantId
→ exact SKU
→ exact normalized option values
```

Do not rely on title-only mapping.

## Connection states

```text
UNMAPPED
CANDIDATE
VERIFIED
AMBIGUOUS
BLOCKED
```

## Automatic mapping

The agent may create a connection automatically only when the mapping is unambiguous.

## Human gate

If more than one commercially valid CJ variant can fulfill the Shopify variant, stop and show options.

## Conflict handling

Before creating a connection:

1. Query existing connection.
2. If identical, reuse it.
3. If absent, create it.
4. If conflicting, stop for human approval.
5. Never silently replace an existing different supplier connection.

## Bundles

A Shopify bundle can map to:

```text
CJ single variant × N
```

or:

```text
CJ pre-packed bundle variant × 1
```

If both exist, this is a business/logistics choice and requires human approval unless a previously approved bundle policy resolves it.

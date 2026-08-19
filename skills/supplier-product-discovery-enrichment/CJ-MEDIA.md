# CJ Media Ingestion Contract — Discovery + Enrichment

Media ingestion occurs only after an exact CJ product is selected, except lightweight
main-image/video-availability metadata used during candidate comparison.


## Required behavior

When a CJ supplier product is approved, the agent must actively look for:

```text
main product image
product gallery / image set
variant images
product video metadata
```

An empty generic `media: []` field is not sufficient.

## Normalized image types

```text
MAIN
GALLERY
VARIANT
DESCRIPTION_EMBEDDED
```

## Normalized video fields

```text
source_url
cover_url
duration
width
height
video_type
rights_status
```

## Caching rule

Prefer:

```text
CJ URL
→ controlled asset cache/storage
→ downstream Shopify/store assets
```

over permanent storefront hotlinking.

If caching cannot be completed in the current environment, preserve the source
URL and mark the asset `CACHE_REQUIRED`.

## Product-truth policy

The following must remain faithful to the real product:

```text
shape
color
physical controls
variant
included parts
dimensions
material appearance
mechanism depiction
```

Generated compositions may change background, layout, graphic overlays, and
brand presentation, but must not create a materially different product.
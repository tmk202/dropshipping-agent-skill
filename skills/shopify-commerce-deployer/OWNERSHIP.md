# Commerce Responsibility Ownership — v4

| Responsibility | Owner | AI behavior |
|---|---|---|
| Shopify theme | AI + Shopify | Write |
| Customer-facing product content | AI + Shopify | Write |
| Pricing | AI + Human | Write after approval |
| Supplier intelligence | CJ API | Read / analyze |
| CJ shop detection | CJ API | Read |
| Shopify product query inside CJ | CJ API | Read |
| CJ product connection | AI + CJ API | Write after verified mapping |
| Variant mapping | AI / Human if ambiguous | Decide |
| Live inventory sync | CJ Shopify App | Monitor only |
| Shopify→CJ order sync | CJ Shopify App | Monitor only |
| CJ fulfillment | CJ | Monitor |
| Tracking→Shopify | CJ Shopify App | Monitor only |

## Hard rule

One responsibility = one writer.

If the CJ Shopify App owns inventory, orders, or tracking, the AI must not enable a competing writer for that responsibility.

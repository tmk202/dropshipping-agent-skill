# CJ Authentication & Authorization Contract

## 1. Human-provided credential

In `HYBRID_CJ_API_APP`, the agent requires:

```env
CJ_API_KEY=
```

The user should create/configure this API Key from the CJ API app/settings.

Do not request CJ account passwords.

## 2. Agent-generated runtime credentials

The agent exchanges `CJ_API_KEY` for runtime authentication material.

Rules:

- Keep access/refresh tokens backend-only.
- Never place tokens in frontend JavaScript.
- Never print tokens in logs or reports.
- Do not hard-code TTL.
- Read expiry metadata returned by CJ.
- Refresh/reacquire before expiration.
- Retry a failed authenticated request once after successful refresh.
- Repeated auth failure becomes `AUTH_REQUIRED`.

## 3. Shopify store authorization inside CJ

CJ API access alone is not enough.

The Shopify store must also be authorized in CJ through the CJ Shopify integration.

The agent should verify this through CJ shop APIs.

If the Shopify store cannot be found as an authorized CJ shop, stop and ask the user to:

1. Install the CJdropshipping Shopify App.
2. Sign in to CJ.
3. Authorize the target Shopify store.
4. Return and confirm completion.

Then the agent verifies again through the API.

## 4. Separation of responsibilities

CJ API:
- sourcing intelligence
- shop detection
- store product query
- product connection creation

CJ Shopify App:
- live inventory sync
- order sync
- fulfillment bridge
- tracking sync

# Shopify Authentication Contract — Dev Dashboard Apps

## Own-store automation

For an app created in Shopify Dev Dashboard and installed on a store owned by
the same organization, the user configures:

```env
SHOPIFY_SHOP_DOMAIN=example.myshopify.com
SHOPIFY_CLIENT_ID=...
SHOPIFY_CLIENT_SECRET=...
```

The agent then programmatically performs:

```text
Client ID + Client Secret
        ↓
Client Credentials Grant
        ↓
Short-lived Access Token
        ↓
X-Shopify-Access-Token
        ↓
Admin GraphQL API
```

The runtime access token is not a credential the user should manually copy into
the agent configuration.

## Runtime token policy

- Acquire automatically.
- Keep server-side only.
- Cache securely when useful.
- Track expiration.
- Renew before expiration.
- On 401 caused by expiration, acquire a fresh token once and retry safely.
- Never print the token or Client Secret in logs.
- Never commit the token or Client Secret.

## Other merchants

Do not use client credentials blindly for stores outside the app owner's
organization. Use the appropriate Shopify authorization flow for the app model
(OAuth authorization code / token exchange / Shopify CLI-managed auth).

## App Automation Token

`SHOPIFY_APP_AUTOMATION_TOKEN` is a separate Shopify Dev Dashboard credential
for non-interactive Shopify CLI app deployment in CI/CD.

It is **not** an Admin GraphQL API access token.


## Hybrid CJ App Note

Shopify authentication is still used for store/product/theme deployment. CJ App authorization is a separate integration and does not replace Shopify Admin API authentication.


## v4 CJ note

Shopify Admin authentication and CJ authentication are independent. Shopify uses the configured Shopify app credentials; CJ uses `CJ_API_KEY` and agent-generated CJ runtime tokens. The CJ Shopify App authorization is a third, separate store-linking requirement.

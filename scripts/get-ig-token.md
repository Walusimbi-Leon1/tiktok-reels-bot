# Getting a Long-Lived IG + FB Access Token

Meta requires a **human** to log in through the browser. This guide is the
step-by-step to produce the `IG_ACCESS_TOKEN` secret.

## Prerequisites
- Instagram account switched to **Professional** (Creator or Business).
- That IG account linked to a **Facebook Page** you manage.
- A Facebook App with the Instagram Basic Display + Pages APIs.
  - App ID / App Secret (get from https://developers.facebook.com/apps/).
  - Add "Instagram Basic Display" + "Pages Manage" / "Pages Read" products.
  - Add redirect URI: `https://www.facebook.com/connect/login_success.html`
  - Set App to **Live** mode (or add your FB account as a test user).

## Step 1 — Short-lived user token (IG + FB scopes)
Open in a browser (logged into the IG-linked Facebook account):

```
https://www.facebook.com/v18.0/dialog/oauth?
  client_id=<APP_ID>
  &redirect_uri=https://www.facebook.com/connect/login_success.html
  &scope=instagram-basic,instagram-contentpublish,pages_manage_posts,pages_read_engagement,pages_show_list,pages_manage_engagement
```

Extract `code=<...>` from the redirect URL, then exchange for a user token:

```bash
curl -X GET "https://graph.facebook.com/v18.0/oauth/access_token" \
  -d "client_id=<APP_ID>" \
  -d "client_secret=<APP_SECRET>" \
  -d "grant_type=authorization_code" \
  -d "redirect_uri=https://www.facebook.com/connect/login_success.html" \
  -d "code=<CODE_FROM_ABOVE>"
```

→ `access_token` = short-lived user token (60 days if you got the right scopes).

## Step 2 — Confirm scopes
```bash
curl -X GET "https://graph.facebook.com/v18.0/me/permissions" \
  -d "access_token=<TOKEN>"
```
You should see `instagram-contentpublish`, `pages_manage_posts`, etc. with `"status":"granted"`.

## Step 3 — Get IG Account ID + Page ID
```bash
curl -X GET "https://graph.facebook.com/v18.0/me/accounts" \
  -d "access_token=<TOKEN>" | python3 -m json.tool
```
Find your Page's `id` and `access_token` (page token). Then:
```bash
curl -X GET "https://graph.facebook.com/v18.0/<PAGE_ID>" \
  -d "fields=instagram_business_account,name" \
  -d "access_token=<PAGE_TOKEN>"
```
→ `ig_id` = your **IG_ACCOUNT_ID**.  
**FB_PAGE_ID** = the Page id.

## Step 4 — Store as GitHub secrets
- `IG_ACCESS_TOKEN` — the **user** token (NOT the page token). The IG reels
  endpoint calls from this token on behalf of the IG user.
- `IG_ACCOUNT_ID` — the Instagram Business/Creator user id from step 3.
- `FB_PAGE_ID` — the Facebook Page id.

## Renewal
Long-lived tokens last ~60 days. Set a calendar reminder to re-run Step 1 → 3
before expiry. There is no API-only refresh for IG content publishing.

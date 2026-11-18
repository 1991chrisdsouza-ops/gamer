# Royal Reels Club secure landing page

This is a **single, transparent landing page for all visitors**. There is no user-agent or bot-specific content. It displays a Cloudflare Turnstile widget on the page, confirms 18+ by self-declaration, and validates Cloudflare tokens in a Netlify Function before the Continue button redirects.

## Deploy

1. Put the entire folder in a Git repository (or deploy it using the Netlify CLI). **Do not** use a static-only Netlify drag-and-drop deploy, which may omit Netlify Functions.
2. Create a Netlify site linked to that repository. The `netlify.toml` sets `public` as the publish directory and `netlify/functions` as the functions directory.
3. Create a Cloudflare Turnstile widget in **Managed** mode with your Netlify/website hostnames registered. Copy its Site Key and Secret Key.
4. In **Netlify → Site configuration → Environment variables**, set:
   - `TURNSTILE_SITE_KEY` = public Turnstile site key
   - `TURNSTILE_SECRET_KEY` = private Turnstile secret key (never put this into HTML)
   - `DESTINATION_URL` = **full HTTPS destination**, e.g. `https://example.com/` (replace with your real destination). This URL is intentionally public on the landing page.
5. Redeploy after configuring variables.
6. Visit the deployed site and check that the destination URL is visible, Cloudflare Turnstile appears, age checkbox works, Continue stays disabled until verified, and a valid token allows redirect.
7. Test invalid/reused tokens: the server should reject them. Test mobile layout.

## Tracking

Included existing IDs:
- Microsoft Clarity: `zf0kenincz`
- Google Tag Manager: `GTM-WHQSRLL3`

Check whether the configured tracking is appropriate for visitors and privacy notice/consent obligations. The GTM container still needs the relevant tags configured and published.

## Access and ad review

- The information on the landing page is visible without solving the security check.
- `/.netlify/functions/config` openly publishes the destination URL, so it is not hidden from visitors or reviewers.
- All user agents see the same landing-page content and the same challenge widget. Cloudflare decides whether the visitor must interact; `appearance: 'always'` does not force a click for every visitor.
- Only the **Continue action** requires a valid Turnstile token. This is not a general site-wide firewall.
- Ads reviewers need to inspect the actual destination; do not use bot cloaking, disguised redirects, or configure firewall rules that make the advertised destination uncrawlable.
- The 18+ checkbox is an assertion of age, not an identity check or legally sufficient proof of age.
- A Turnstile pass is not a guarantee of a human visitor; bots may sometimes obtain valid tokens.
- Do not assume neutral copy makes an otherwise restricted service eligible for ads. Verify local laws, platform rules and certification requirements.

## Project files

```
public/index.html
netlify/functions/config.mjs
netlify/functions/verify.mjs
netlify.toml
README.md
```

## Reference documentation

- https://developers.cloudflare.com/turnstile/get-started/client-side-rendering/widget-configurations/
- https://developers.cloudflare.com/turnstile/get-started/server-side-validation/
- https://docs.netlify.com/build/functions/get-started/
- https://support.google.com/adspolicy/answer/16428929?hl=en

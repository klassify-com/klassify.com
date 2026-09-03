# klassify.com

Landing page for Klassify — plain static HTML, no build step, served by
GitHub Pages.

## Publishing

- One page: `index.html`. Edit it, commit, push — Pages redeploys.
- `CNAME` carries the custom domain (`klassify.com`) — do not delete it.
- `.nojekyll` disables Jekyll processing.

## Launch runbook (one-time)

1. Create a **public** repo `klassify.com` under the `klassify-com` org, then
   from this directory:

   ```bash
   git remote add origin git@github.com:klassify-com/klassify.com.git
   git push -u origin main
   ```

2. Repo **Settings → Pages**: Source = "Deploy from a branch", branch `main`,
   folder `/ (root)`. Custom domain: `klassify.com` (matches the CNAME file).
   Tick **Enforce HTTPS** once the certificate is issued (can take a few
   minutes after DNS resolves).

3. **Route 53**, hosted zone `klassify.com` — add records
   ([GitHub Pages custom-domain docs](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site)):

   | Name | Type  | Value |
   |------|-------|-------|
   | `@` (apex) | A | 185.199.108.153, 185.199.109.153, 185.199.110.153, 185.199.111.153 |
   | `www` | CNAME | `klassify-com.github.io` |

   Optionally add the AAAA records listed in the docs for IPv6.
   **Do not touch the MX records** — mail on this domain is live
   (Google Workspace).

   Or from the CLI — `dns/route53-github-pages.json` holds exactly these
   records (A + AAAA + `www` CNAME, `CREATE` only, so it fails instead of
   overwriting anything that already exists):

   ```bash
   ZONE=$(aws route53 list-hosted-zones-by-name --dns-name klassify.com \
            --query 'HostedZones[0].Id' --output text)
   aws route53 change-resource-record-sets --hosted-zone-id "$ZONE" \
            --change-batch file://dns/route53-github-pages.json
   ```

4. Recommended: verify the domain for the org (**Org settings → Pages →
   Verified domains**) so no one else can claim `klassify.com` on Pages.

5. Check `https://klassify.com` and `https://www.klassify.com`.

# DNS Migration Guide
## Pointing altispectra.com → GitHub Pages via Squarespace

**Prepared by:** AI COO (Claude, Cowork mode)
**Date:** 2026-03-27
**Domain registrar:** Squarespace
**Target host:** GitHub Pages

---

## Before you start

This process has two parts done in parallel:
- **GitHub side:** Tell GitHub your custom domain
- **Squarespace side:** Tell your domain where to point

DNS changes take between 10 minutes and 48 hours to propagate worldwide. Your site will continue working at the github.io URL throughout.

---

## Part 1 — Add a CNAME file to your repository

This file tells GitHub Pages which custom domain to serve.

**Step 1.1** — Go to your repository:
https://github.com/alexrobcraig-oss/altispectra-site

**Step 1.2** — Click **"Add file"** → **"Create new file"**

**Step 1.3** — Name the file exactly: `CNAME` (uppercase, no extension)

**Step 1.4** — In the file body, type one line only:
```
altispectra.com
```

**Step 1.5** — Click **"Commit changes"** → **"Commit directly to the main branch"** → **"Commit changes"**

---

## Part 2 — Configure GitHub Pages custom domain

**Step 2.1** — Go to your repository Settings → Pages:
https://github.com/alexrobcraig-oss/altispectra-site/settings/pages

**Step 2.2** — Under **"Custom domain"**, type:
```
altispectra.com
```

**Step 2.3** — Click **Save**. GitHub will run a DNS check (it will likely show a warning at first — that's normal, it resolves after propagation).

Leave **"Enforce HTTPS"** unticked for now. You'll come back to tick it in Part 5.

---

## Part 3 — Add DNS records in Squarespace

**Step 3.1** — Log into Squarespace → **Domains** → click **altispectra.com** → **DNS Settings** → **DNS Records**

**Step 3.2** — Add these **4 A records** (one at a time):

| Type | Host | Value | TTL |
|---|---|---|---|
| A | `@` | `185.199.108.153` | 3600 |
| A | `@` | `185.199.109.153` | 3600 |
| A | `@` | `185.199.110.153` | 3600 |
| A | `@` | `185.199.111.153` | 3600 |

> **`@`** means the root domain (altispectra.com itself). Some registrars show this as blank or as `altispectra.com` — use whatever your interface expects.

**Step 3.3** — Add this **CNAME record**:

| Type | Host | Value | TTL |
|---|---|---|---|
| CNAME | `www` | `alexrobcraig-oss.github.io` | 3600 |

> This makes `www.altispectra.com` redirect to the same site.

**Step 3.4** — Save all records.

> **Note:** Squarespace may have existing A records for their own hosting. Delete any old A records pointing to Squarespace IPs before adding the GitHub ones above. If you're unsure which are Squarespace's, take a screenshot before making changes.

---

## Part 4 — Check propagation

DNS changes don't apply instantly. Here's how to check progress:

**Method A — Online tool (easiest):**
Go to https://dnschecker.org and enter `altispectra.com`. Switch the query type to **A**. You'll see a world map of servers — when most show `185.199.108.153`, propagation is nearly complete.

**Method B — Terminal on your Mac:**
```bash
dig altispectra.com +short
```
When this returns the four GitHub IPs (185.199.108–111.153), propagation is done for your region.

**Method C — Simple browser test:**
Type `http://altispectra.com` in your browser. When the Altispectra site loads (instead of a Squarespace page or error), propagation is complete.

Typical wait time: **10–30 minutes** for most regions. Worst case: 48 hours.

---

## Part 5 — Enforce HTTPS

Only do this **after** altispectra.com is loading correctly in your browser.

**Step 5.1** — Go back to GitHub Pages settings:
https://github.com/alexrobcraig-oss/altispectra-site/settings/pages

**Step 5.2** — Tick the **"Enforce HTTPS"** checkbox.

**Step 5.3** — GitHub will provision a free SSL certificate via Let's Encrypt. This takes 5–15 minutes. After that, `http://altispectra.com` will automatically redirect to `https://altispectra.com`.

---

## Summary checklist

- [ ] `CNAME` file added to repo root containing `altispectra.com`
- [ ] Custom domain set in GitHub Pages settings
- [ ] 4 × A records added in Squarespace DNS
- [ ] 1 × CNAME `www` record added in Squarespace DNS
- [ ] Old Squarespace A records removed
- [ ] Propagation confirmed (dnschecker.org or `dig`)
- [ ] "Enforce HTTPS" ticked in GitHub Pages settings
- [ ] `https://altispectra.com` loading correctly in browser

---

## Troubleshooting

**Site shows "404 Not Found" after domain resolves**
→ Make sure the `CNAME` file in the repo contains exactly `altispectra.com` with no extra spaces or lines.

**GitHub Pages shows "Domain's DNS record could not be retrieved"**
→ DNS hasn't propagated yet. Wait 30 minutes and check again.

**"Enforce HTTPS" is greyed out**
→ DNS hasn't fully propagated or the SSL certificate is still being provisioned. Wait and try again.

**www.altispectra.com works but altispectra.com doesn't (or vice versa)**
→ Double-check that both the A records (for root `@`) and the CNAME (for `www`) are saved correctly in Squarespace.

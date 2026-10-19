# CogCore site

One-page static site for cogcore.dev. Two files, no build step:

- `index.html` — the page
- `style.css` — styles, light and dark theme via `prefers-color-scheme`

Open `index.html` in a browser to preview. Any static host works (GitHub Pages, Cloudflare Pages, Netlify, nginx).

## Before publishing

Search `index.html` for `[` and fill every placeholder:

| Placeholder | Where | What to put |
|---|---|---|
| `[testimonial: …]`, `[name, role, company]` | AI-CRM card | A real quote from a CRM user, or delete the `<blockquote>` block |
| `[legal entity, country]` | FAQ "How do I pay?" and footer | The company that issues invoices |
| `YOUR_FORM_ID` | contact form `action` | Formspree form id (see below) |

### Contact form (Formspree)

1. Sign up at https://formspree.io, create a form, copy its id (looks like `xabcdefg`).
2. Replace `YOUR_FORM_ID` in the form's `action` attribute.
3. In the Formspree dashboard, set the notification address to `hello@cogcore.dev` and restrict the form to the `cogcore.dev` domain.

Any service that accepts a plain HTML `POST` works the same way (Web3Forms, Basin, Getform). Only the `action` URL changes.

## Deploy to GitHub Pages

1. Create a public repository, for example `cogcore-site`, under the GitHub account or organisation you use for CogCore.
2. Put `index.html`, `style.css` and this `README.md` in the repository root and push to `main`:

   ```bash
   git init
   git add index.html style.css README.md CNAME
   git commit -m "CogCore site"
   git branch -M main
   git remote add origin git@github.com:<account>/cogcore-site.git
   git push -u origin main
   ```

3. Add a file named `CNAME` (no extension) in the root containing one line:

   ```
   cogcore.dev
   ```

   Commit it with the rest. GitHub Pages uses it to serve the custom domain.

4. In the repository: Settings → Pages → Build and deployment → Source: "Deploy from a branch", Branch: `main`, folder `/ (root)`. Save.
5. On the same page, under Custom domain, enter `cogcore.dev` and save. Wait for the DNS check to pass, then tick "Enforce HTTPS". The certificate is issued automatically and can take up to an hour after DNS propagates.

`.dev` domains are on the HSTS preload list, so browsers only open them over HTTPS. Until "Enforce HTTPS" is on and the certificate is issued, the site will not open at all. This is expected.

## DNS records for cogcore.dev

At the registrar (Namecheap, Porkbun) open the DNS settings for `cogcore.dev` and add:

| Type | Host | Value | TTL |
|---|---|---|---|
| A | `@` | `185.199.108.153` | auto |
| A | `@` | `185.199.109.153` | auto |
| A | `@` | `185.199.110.153` | auto |
| A | `@` | `185.199.111.153` | auto |
| AAAA | `@` | `2606:50c0:8000::153` | auto |
| AAAA | `@` | `2606:50c0:8001::153` | auto |
| AAAA | `@` | `2606:50c0:8002::153` | auto |
| AAAA | `@` | `2606:50c0:8003::153` | auto |
| CNAME | `www` | `<account>.github.io.` | auto |

Replace `<account>` with your GitHub user or organisation name. Delete any parking records the registrar added by default (usually an A record or a URL redirect on `@`).

These are GitHub's documented addresses; verify them against the current list at https://docs.github.com/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site before adding.

Check propagation:

```bash
dig cogcore.dev +noall +answer
```

You should see the four A records. Then finish step 5 above.

### Email on the same domain

Mail records do not conflict with Pages. Add the MX, SPF and DKIM records your mail provider gives you (Zoho Mail, Yandex 360) alongside the records above. Do not add an A or CNAME record for `@` other than the ones listed.

## Alternative: Cloudflare Pages

1. Cloudflare dashboard → Workers & Pages → Create → Pages → Connect to Git, pick the repository.
2. Framework preset: None. Build command: empty. Output directory: `/`.
3. After the first deploy, Custom domains → add `cogcore.dev`. If the domain's DNS is already on Cloudflare, the record is created for you; otherwise follow the CNAME instructions shown.

## Updating

Edit `index.html` or `style.css`, commit, push. GitHub Pages redeploys within a minute. No cache-busting is needed for a site this size; if a style change does not show up, hard-refresh (Ctrl+Shift+R).

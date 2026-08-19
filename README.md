# platformaccountability.com

Static site for the Accountability Infrastructure project, migrated from Wix to
GitHub Pages. Plain HTML/CSS, no build step, no JS framework.

## Structure

```
/                                   Home page (index.html)
/proposal/                          Proposal page (proposal/index.html)
/about/                             About + contact form (about/index.html)
/assets/css/style.css               Shared stylesheet
/assets/img/                        Site images
/assets/pdf/                        The three whitepaper PDFs
CNAME                               Custom domain for GitHub Pages
```

GitHub Pages serves a directory's `index.html` at the directory's URL, so
`/proposal/index.html` is served at `/proposal` and `/about/index.html` at
`/about` — this preserves the old Wix URLs.

## Editing content

Everything is plain HTML — open the relevant `index.html` and edit the text
directly. There's no templating, so the nav and footer are duplicated at the
top/bottom of each page; if you change the nav or footer, update it in all
three `index.html` files.

Shared visual styling (colors, fonts, spacing) lives in
[`assets/css/style.css`](assets/css/style.css).

## Contact form (Formspree)

The form on the About page posts to
[Formspree](https://formspree.io). Before it will work you need to:

1. Sign up for a free Formspree account at https://formspree.io.
2. Create a new form and copy its endpoint ID (looks like `xyzabcd`).
3. In [`about/index.html`](about/index.html), find this line:

   ```html
   <form id="contact-form" class="contact-form" action="https://formspree.io/f/YOUR_FORMSPREE_ID" method="POST">
   ```

   and replace `YOUR_FORMSPREE_ID` with your real endpoint ID.
4. In the Formspree dashboard, verify the email address you want submissions
   sent to.

The form submits via JavaScript (`fetch`) and shows an inline "Thanks for
submitting!" message on success, without leaving the page — matching the old
Wix behavior. If JavaScript fails or is disabled, the form still submits as a
plain POST and Formspree will show its own default confirmation page.

## GitHub Pages setup

1. Push this repo to GitHub (e.g. as `platformaccountability` under your
   account).
2. In the repo, go to **Settings → Pages**.
3. Under **Build and deployment → Source**, choose **Deploy from a branch**.
4. Set the branch to `main` (or whichever branch you push to) and the folder
   to `/ (root)`.
5. Under **Custom domain**, enter `www.platformaccountability.com` and save.
   (This matches the `CNAME` file already in the repo.) GitHub will start
   checking DNS — this will show an error until you update DNS per below.
6. Once DNS propagates and GitHub verifies the domain, check **Enforce
   HTTPS**.

## DNS records to update at your registrar

Wix's site was serving `www.platformaccountability.com` as the canonical URL
(the apex `platformaccountability.com` should redirect to it), so keep that
setup: point the **apex domain** at GitHub Pages' IPs, and point **www** at
your GitHub Pages hostname.

At your domain registrar (wherever `platformaccountability.com` is
registered — check if that's Wix or a separate registrar), replace the
existing Wix DNS records with:

**A records** for the apex domain (`platformaccountability.com` / `@`):

```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

**AAAA records** for the apex domain (optional, IPv6):

```
2606:50c0:8000::153
2606:50c0:8001::153
2606:50c0:8002::153
2606:50c0:8003::153
```

**CNAME record** for `www`:

```
www.platformaccountability.com  →  YOUR-GITHUB-USERNAME.github.io
```

(Replace `YOUR-GITHUB-USERNAME` with your actual GitHub username or org
name.)

These are GitHub's currently documented Pages IPs
(https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site) —
double-check that page in case they've changed since this was written.

**Important:** Once these records are live and GitHub Pages has verified the
domain, go back into Wix and turn off/remove its hosting and DNS records for
this domain (or move the domain off Wix DNS entirely) so there's no conflict,
and cancel the Wix hosting plan once you've confirmed the new site is live.

## Local preview

From the repo root:

```bash
python3 -m http.server 8000
```

Then open http://localhost:8000/. (Any static file server works — this one
just uses Python's builtin, so nothing extra to install on most systems.)

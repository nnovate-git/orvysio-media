# How media.orvysio.com is set up

Set up Oct 16, 2026 (UTC). This is the record of what was configured, where each setting lives, and
how to fix it if it stops working. Instructions for publishing files are in [CLAUDE.md](CLAUDE.md).

## What it is

- **Purpose:** public URLs for approved social post images and PDFs, so Buffer can attach them to
  LinkedIn and Facebook posts.
- **Repo:** https://github.com/nnovate-git/orvysio-media, public. GitHub Pages on a free account only
  works for public repos.
- **Separate from the app.** The OrVysio app does not use this host, and nothing here is needed to
  run it.

## Settings and where they live

| Piece | Where it is set | Value |
|---|---|---|
| Pages source | Repo Settings, Pages | Deploy from a branch: `main`, `/ (root)` |
| Custom domain | Same page, and the `CNAME` file | `media.orvysio.com` |
| HTTPS | Same page | "Enforce HTTPS" should be checked (owner step, not yet confirmed) |
| Certificate | Automatic (Let's Encrypt, requested and renewed by GitHub) | First one valid until Jan 14, 2027 |
| DNS | GoDaddy, DNS records for orvysio.com | `CNAME` `media` → `nnovate-git.github.io` |
| Crawlers | `robots.txt` | `Disallow: /` for everyone |
| Docs kept off the site | `_config.yml` | Any `*.md` file; no theme files added (`theme: null`) |
| Byte-exact files | `.gitattributes` | PNG, PDF and other images marked binary |
| Claude's GitHub access | https://github.com/settings/installations, Claude, Configure | `orvysio-media` under Repository access |
| Claude cloud network | Claude Code cloud environment, Network access | `*.orvysio.com` under Allowed domains |

## What was done

1. The owner created the empty public repo on GitHub (the Claude GitHub App got a 403 when it tried)
   and gave the Claude app access to it.
2. Claude pushed the first commit: `CNAME`, `robots.txt`, blank `index.html` pages, `README.md`, and
   a test PNG and PDF in `posts/test/`.
3. The owner turned on Pages (`main`, root). The first build succeeded at 01:26 UTC.
4. The owner added the GoDaddy `CNAME` record. GoDaddy's propagation checker showed it in all regions
   within minutes, and GitHub's DNS check passed.
5. GitHub issued the Let's Encrypt certificate.
6. Claude checked the live site over HTTPS: the PNG was served as `image/png` and the PDF as
   `application/pdf`, both identical to the files in the repo; `robots.txt` and the blank index pages
   were served; the certificate was valid.
7. The docs were kept off the site with `_config.yml` (replacing the `.nojekyll` file), first checked
   with a local build using the same builder GitHub uses, then live: the docs return 404 and the
   images are unchanged. That push also showed a push from a Claude session starts a Pages build.
8. The test files in `posts/test/` were removed.

## Keeping it working

- **Keep the GoDaddy `CNAME` record.** Without it the address stops working and the certificate cannot
  renew.
- **Keep the repo public and under `nnovate-git`.** A private repo turns Pages off on a free account.
  If the GitHub account is ever renamed, change the DNS value to `<new-name>.github.io`.
- **Keep `CNAME`, `robots.txt`, `_config.yml` and `.gitattributes`.** Deleting `CNAME` drops the
  custom domain.
- **CAA records** (a DNS setting that limits which companies may issue certificates): if anyone adds
  one to orvysio.com at GoDaddy, also add one that allows `letsencrypt.org`, or the certificate will
  not renew.
- **Size:** GitHub blocks files over 100 MB, and a published Pages site may be no larger than 1 GB.
  Pages also has soft limits of 100 GB of downloads a month and 10 builds an hour. Once posts have gone
  out, old date folders can be deleted to keep the published site small. That does not shrink the repo
  (Git history keeps every file), and GitHub recommends Pages repos stay under 1 GB, so keep each file
  well under 10 MB.
- **Link previews:** `robots.txt` also turns LinkedIn's crawler away, so a post that links to a
  media.orvysio.com URL (instead of attaching the image) gets no preview.
- **Claude access:** keep `orvysio-media` in the Claude app's repository access and `*.orvysio.com`
  in the cloud environment's allowed domains. Select `orvysio-media` when starting a Claude Code
  session that should publish.

## Recommended, not yet done

- **Confirm "Enforce HTTPS":** repo Settings, Pages: the box is checked. Then open
  http://media.orvysio.com/robots.txt in a browser: the address should change to `https://`. A cloud
  session cannot check this, because its proxy only allows HTTPS. Update the table above once done.
- **Verify orvysio.com with GitHub:** signed in as nnovate-git, open https://github.com/settings/pages,
  click "Add a domain", enter `orvysio.com` and click "Add domain". At GoDaddy, add the TXT record it
  shows (name `_github-pages-challenge-nnovate-git`; GoDaddy adds `.orvysio.com`). When the record has
  spread (minutes, up to 24 hours), return to the same page and click "Verify". Keep the TXT record
  afterward. This stops another GitHub account from publishing a site at `media.orvysio.com` if this
  repo is deleted or Pages is turned off while the GoDaddy record still points at GitHub.
- **When retiring the host:** delete the GoDaddy record first, then turn off Pages or delete the repo.

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| New file returns 404 | Build not finished, or wrong path or case | Wait a minute; check the "pages build and deployment" run under the repo's Actions tab; compare the path letter by letter |
| No "pages build and deployment" run within 2 minutes of a push | GitHub did not start a build for that push | Make any small commit on `main` on github.com (for example, edit README.md). GitHub notes a branch site may not publish until someone with admin rights and a verified email pushes |
| Old image still shows after a change | The site is cached for 10 minutes | Publish under a new name instead of replacing a file |
| "There isn't a GitHub Pages site here" | Pages turned off, or `CNAME` deleted | Turn Pages back on (`main`, root) and restore `CNAME` |
| Build failed in the Actions tab | Usually a broken `_config.yml` | Open the failed run's log; revert the last commit |
| Certificate warning, or "Enforce HTTPS" grayed out | DNS record changed, or CAA records | Check the GoDaddy record and CAA records. Then click Remove next to the custom domain, type `media.orvysio.com` again and click Save (this commits `CNAME` to `main`). HTTPS can take up to an hour to return |
| A doc shows on media.orvysio.com | A doc not named `*.md` | Rename it to `.md` or add it to `exclude` in `_config.yml`, push, check its URL returns 404 |
| Claude: "repository not found" | Claude app lost access | https://github.com/settings/installations, Claude, Configure, add `orvysio-media` under Repository access, Save |
| Claude: `CONNECT tunnel failed, response 403` | Network allowance missing | Add `*.orvysio.com` in the cloud environment's Network access settings |

## For the Claude chat that makes the images

Paste this into that chat's or project's instructions:

```
Media host for OrVysio social posts
- Approved images and PDFs are published at https://media.orvysio.com from the public GitHub repo
  nnovate-git/orvysio-media (branch main). Everything there is public.
- File path: posts/YYYY-MM-DD/<brand>-<slug>-<8 random characters>.png (or .pdf), where
  YYYY-MM-DD is the day the post goes out, everything is lowercase, and the 8 characters are fresh
  random letters and digits for each file.
  Example: posts/2026-10-20/orvysio-copilot-adoption-k3x9p2qa.png is served at
  https://media.orvysio.com/posts/2026-10-20/orvysio-copilot-adoption-k3x9p2qa.png
  (never reuse the example's k3x9p2qa).
- Never reuse or overwrite a name; a changed image gets a new name.
- Only final, approved images: no customer data, real people's names or photos, screenshots of real
  dashboards, drafts or credentials.
- With each finished image, give its file name in this pattern. The URL works about a minute after
  the file is pushed to main; the session that publishes it confirms the URL. Open it before
  scheduling the post in Buffer.
```

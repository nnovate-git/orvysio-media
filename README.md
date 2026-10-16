# orvysio-media

Public host for **published** OrVysio social media images and PDFs, served at
https://media.orvysio.com through GitHub Pages. Buffer pulls files from here to post on LinkedIn
and Facebook.

**Everything in this repository is public.** Only put files here that are already approved for
posting. Nothing private: no customer data, real people's names or photos, screenshots of real
dashboards, drafts, contracts, email addresses or credentials.

- [CLAUDE.md](CLAUDE.md): how to add and publish files (read automatically by Claude Code sessions;
  for the Claude chat that makes the images, use the paste block at the end of SETUP.md).
- [SETUP.md](SETUP.md): how the hosting is set up, what keeps it working, and troubleshooting.

These docs are visible here on GitHub but are not served on media.orvysio.com (see `_config.yml`).

## Layout and naming

```
posts/YYYY-MM-DD/<brand>-<slug>-<8 random characters>.png
posts/YYYY-MM-DD/<brand>-<slug>-<8 random characters>.pdf
```

- `YYYY-MM-DD` is the date the post goes out.
- `<brand>` is the brand, for example `orvysio`.
- `<slug>` is a short lowercase description with hyphens, for example `copilot-adoption`.
- The 8 random characters are lowercase letters and digits, so names cannot be guessed.

Example: `posts/2026-10-20/orvysio-copilot-adoption-k3x9p2qa.png` is served at
`https://media.orvysio.com/posts/2026-10-20/orvysio-copilot-adoption-k3x9p2qa.png`.
CLAUDE.md has the full rules.

Add a blank `index.html` to each new date folder, like the one in `posts/`. (GitHub Pages does
not list folder contents anyway; the blank pages make that explicit.)

## Other files

- `CNAME`: the custom domain, `media.orvysio.com`.
- `robots.txt`: asks search engines and other crawlers not to crawl anything.
- `_config.yml`: keeps the docs off the site.
- `.gitattributes`: keeps images and PDFs byte-exact when committed from Windows.

# orvysio-media

Public host for **published** OrVysio social media images and PDFs, served at
https://media.orvysio.com through GitHub Pages. Buffer pulls files from here to post on LinkedIn
and Facebook.

**Everything in this repository is public.** Only put files here that are already approved for
posting. Nothing private: no customer data, screenshots of real dashboards with real names,
drafts, contracts or credentials.

## Layout and naming

```
posts/YYYY-MM-DD/<brand>-<slug>-<8 random characters>.png
posts/YYYY-MM-DD/<brand>-<slug>-<8 random characters>.pdf
```

- `YYYY-MM-DD` is the date the post goes out.
- `<brand>` is the brand, for example `orvysio`.
- `<slug>` is a short lowercase description with hyphens, for example `copilot`.
- The 8 random characters are lowercase letters and digits, so names cannot be guessed.

Example: `posts/2026-10-15/orvysio-copilot-k3x9p2qa.png` is served at
`https://media.orvysio.com/posts/2026-10-15/orvysio-copilot-k3x9p2qa.png`.

Add a blank `index.html` to each new date folder, like the ones already here. (GitHub Pages does
not list folder contents anyway; the blank pages make that explicit.)

## Other files

- `CNAME`: the custom domain, `media.orvysio.com`.
- `robots.txt`: asks search engines not to index anything.
- `.nojekyll`: serves files as they are, without Jekyll processing.
- `posts/test/`: test files that confirm PNGs and PDFs are served with the right content types.

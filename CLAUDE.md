# orvysio-media: instructions for Claude

This repo is the public file host for OrVysio social posts. Every image and PDF pushed to `main` is
published at https://media.orvysio.com within about a minute, so Buffer can attach it to LinkedIn and
Facebook posts. The repo itself is public on GitHub. How the hosting is set up, and how to fix it, is
in [SETUP.md](SETUP.md).

## Rules

- **Public only.** Only images and PDFs the owner has approved for posting. Never customer data, real
  people's names or photos, screenshots of real dashboards, drafts, contracts, email addresses,
  passwords, tokens or keys. If unsure, ask before pushing.
- **Push straight to `main`.** No branch or pull request (owner's decision, Oct 16, 2026).
- **Never overwrite or rename a published file.** A changed image gets a new name. The site is cached
  for 10 minutes and Buffer may already have fetched the old file.
- **Never delete a file before its post has gone out.**
- **Leave `CNAME`, `robots.txt`, `_config.yml`, `.gitattributes` and the blank `index.html` pages
  alone** unless the owner asks. They hold the domain, the request that crawlers stay away, the list
  of docs kept off the site, and the rule that keeps images and PDFs byte-exact.

## Naming

```
posts/YYYY-MM-DD/<brand>-<slug>-<8 random characters>.png
posts/YYYY-MM-DD/<brand>-<slug>-<8 random characters>.pdf
```

- `YYYY-MM-DD`: the day the post goes out.
- `<brand>`: lowercase brand name, for example `orvysio`.
- `<slug>`: a few lowercase words joined by hyphens, for example `copilot-adoption`.
- 8 random characters: lowercase letters and digits, so names cannot be guessed before a post goes
  out. If the owner or the image chat already gave a name that fits this pattern, keep it exactly: a
  Buffer post may already point at it. Never use the example's `k3x9p2qa`. Otherwise generate a new
  string for each file:
  `python3 -c "import secrets, string; print(''.join(secrets.choice(string.ascii_lowercase + string.digits) for _ in range(8)))"`
- Only lowercase letters, digits and hyphens, plus a lowercase `.png` or `.pdf`. URLs are
  case-sensitive, and the site builder skips names that start with `_`, `.`, `#` or `~`.

Example: `posts/2026-10-20/orvysio-copilot-adoption-k3x9p2qa.png` is served at
`https://media.orvysio.com/posts/2026-10-20/orvysio-copilot-adoption-k3x9p2qa.png`.

## Publishing

0. Start from the latest `main`, even if the session opened on another branch:
   `git switch main && git pull --ff-only origin main`. The owner's GitHub settings changes can
   commit to `main`.
1. Put the files in `posts/YYYY-MM-DD/`. For a new date folder, copy `posts/index.html` into it (a
   blank page, so the folder URL shows nothing).
2. Check each file: `file <path>` says PNG image or PDF document, matching the extension, and
   `stat -c %s <path>` is under 10000000 (10 MB, Buffer's image limit; GitHub refuses files over
   100 MB, and the whole site must stay under 1 GB).
3. Stage only that folder (`git add posts/YYYY-MM-DD/`), check `git status` shows nothing else, and
   commit all of the day's files together (GitHub Pages has a soft limit of 10 builds an hour), for
   example `Add copilot-adoption post for 2026-10-20`. End the message with the attribution lines
   from your session instructions.
4. `git push origin main`. If it is rejected, run `git pull --rebase origin main` and push again.
5. Find GitHub's **pages build and deployment** run whose `head_sha` is `git rev-parse HEAD`: in a
   cloud session, the GitHub tool `actions_list` (method `list_workflow_runs`, owner `nnovate-git`,
   repo `orvysio-media`, perPage 5); elsewhere `gh run list -R nnovate-git/orvysio-media -L 3`. It
   usually finishes in under a minute. If no run appears within 2 minutes, or it fails, stop and tell
   the owner (see SETUP.md, Troubleshooting). The site keeps serving the last good build.
6. Check every new URL. The cloud proxy prints `200 Connection Established` even for a missing file,
   so read the status this way:
   `curl -sf -o /dev/null --retry 12 --retry-delay 10 --retry-all-errors -w '%{http_code} %{content_type} %{size_download}\n' https://media.orvysio.com/posts/...`
   It waits up to two minutes and must print `200 image/png` (or `200 application/pdf`) and the same
   byte count as `stat -c %s <path>`. If it still fails, compare the path and case letter by letter.
7. Give the owner the `https://media.orvysio.com/...` URLs.

## Claude Code cloud sessions

- Only HTTPS can be checked through the cloud proxy; `http://` URLs cannot.
- Creating repos and changing Pages settings are owner steps. The GitHub App got a 403 when it tried
  to create this repo, and the proxy blocks GitHub's Pages API.
- For `CONNECT tunnel failed, response 403` or "repository not found", see SETUP.md, Troubleshooting.

## Docs in this repo

Any `*.md` file, in any folder, is kept off the site by `_config.yml`. Name new docs with a lowercase
`.md` extension (a `.txt` or other doc would be published), then check that its URL on the site
returns 404.

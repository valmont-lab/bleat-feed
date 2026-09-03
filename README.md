# Unofficial RSS feed for James Lileks' Bleat

`lileks.com/bleats/` has no RSS feed, so this scrapes it and builds one.

## Why this needs to run somewhere, on a schedule

I can't host a live feed for you — feed readers need to fetch an
`feed.xml` file that gets refreshed regularly. You need to run
`generate_feed.py` on a timer, somewhere with internet access. Two
easy options below.

## The tricky part

The Bleat doesn't use predictable date-based URLs (e.g. no
`/2026/08/31.html`) — it uses an old custom redirect page that points
to whatever the current post is. `generate_feed.py` tries a few ways
to detect that redirect (meta refresh, JS redirect, a plain link),
falling back to treating the front page itself as the post. This
*should* work, but if the site changes or a run grabs the wrong thing,
open an issue... with yourself, and tweak `resolve_latest_url()`. Run
it once manually first and check `feed.xml` before automating it.

## Option A: Cron on your own machine/server

```bash
pip install requests beautifulsoup4
python generate_feed.py   # run once to check it works, inspect feed.xml
```

Then add a daily cron job, e.g. `crontab -e`:

```
0 9 * * * cd /path/to/this/folder && /usr/bin/python3 generate_feed.py >> feed.log 2>&1
```

Point your feed reader at the local `feed.xml` file, or serve the
folder with any static file host (`python -m http.server`, nginx,
etc.) so the reader can fetch it over HTTP.

## Option B: GitHub Actions + GitHub Pages (free, no server needed)

1. Create a new GitHub repo, add `generate_feed.py` to it.
2. Add `update-feed.yml` as `.github/workflows/update-feed.yml`.
3. Push. Run the workflow once manually (Actions tab → Run workflow)
   to generate the first `feed.xml`.
4. In repo Settings → Pages, enable Pages from the `main` branch.
5. Your feed will be live at:
   `https://<your-username>.github.io/<repo-name>/feed.xml`
6. Subscribe to that URL in your feed reader.

## Files

- `generate_feed.py` — the scraper/generator, run this on a schedule
- `update-feed.yml` — optional GitHub Actions workflow (see Option B)
- `state.json` / `feed.xml` — created automatically on first run

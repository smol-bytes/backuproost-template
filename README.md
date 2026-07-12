# 🪺 Your automatic Notion backup

This repository backs up your Notion workspace **every night, automatically, for free** — as readable files with full version history. Set it up once (about 10 minutes of clicking, no coding, no terminal) and never think about it again.

Why you want this: Notion has no automatic backup. Deleted pages are gone from the trash after 30 days, version history only reaches back 7–90 days depending on your plan, and the official export is manual and lossy. This repo is your insurance.

> **Reading this on the template page?** Click the green **Use this template** button (top right) → **Create a new repository** → name it something like `notion-backup` → select **🔒 Private** → **Create repository**. Then follow the steps below *in your new repository*.

## Step 1 — Create a Notion integration *(~3 minutes)*

<!-- screenshot: notion integrations page, "New integration" form -->

1. Open [notion.so/profile/integrations](https://www.notion.so/profile/integrations) (sign in if asked).
2. Click **New integration**. Name it `BackupRoost`, pick your workspace, leave type as **Internal**.
3. Under **Capabilities**, only **Read content** is needed — you can untick the rest.
4. Click **Save**, then **copy the Internal Integration Secret** (a long code starting with `ntn_` or `secret_`). Keep it handy for Step 3.

## Step 2 — Tell Notion which pages to back up

<!-- screenshot: page ••• menu → Connections → BackupRoost -->

1. In Notion, open a **top-level page** you want backed up.
2. Click the **•••** menu (top-right) → **Connections** → **➕ Add connection** → choose **BackupRoost**.
3. Repeat for each top-level page or teamspace. **All sub-pages are included automatically** — you only need to connect the top of each tree.

## Step 3 — Give this repository the secret

<!-- screenshot: repo Settings → Secrets and variables → Actions → New repository secret -->

1. In **this repository** on GitHub: **Settings** → **Secrets and variables** → **Actions**.
2. Click **New repository secret**.
3. Name: `NOTION_TOKEN` (exactly like that). Value: paste the secret from Step 1.
4. Click **Add secret**.

## Step 4 — Turn it on

<!-- screenshot: Actions tab, enable button, then Run workflow -->

1. Open the **Actions** tab of this repository. If GitHub asks, click **"I understand my workflows, go ahead and enable them."**
2. Click **Notion backup** in the left sidebar → **Run workflow** → green **Run workflow** button.
3. Wait a minute, then refresh. A green checkmark means your first backup is done — your pages are now in the [`pages/`](pages/) folder. *(Large workspaces sync at ~3 pages/second on the first run; after that only changed pages are fetched, so nightly runs are fast.)*

That's it. Every night at 03:00 UTC a new backup commit appears — no further action ever needed.

## What's in your backup

| Path | What it is |
|---|---|
| `pages/<name>/<Title>.md` | Each page as Markdown — click it to read right here on GitHub |
| `pages/<name>/page.json` | The raw Notion data for that page (enables future automated restore) |
| `data-sources/` | Your database definitions |
| `manifest-latest.json` | Report of the last run — synced/skipped/failed counts and warnings |

Every version of every page is kept forever: open any file → **History** to see it as it was on any past day.

## Getting a page back

Lost a page in Notion? Open its `.md` file here, click **Raw**, copy everything, and paste into a new Notion page — Notion converts the Markdown back automatically. For an older version, use the file's **History** first. (One-click automated restore is on the roadmap.)

## Troubleshooting

- **Run failed, log mentions `401`** — the `NOTION_TOKEN` secret is missing or wrong. Redo Step 3.
- **"Found 0 pages"** — the integration isn't connected to any page. Redo Step 2.
- **A warning about page count dropping** — you unshared pages, or something was mass-deleted in Notion. Your backed-up files are kept either way; check `manifest-latest.json`.
- **Backups stopped after ~2 months** — GitHub pauses schedules in repos with no activity. Open the **Actions** tab and click re-enable.

## Honest limitations

- Only pages **connected to the integration** (Step 2) are backed up — Notion offers no "everything, forever" access.
- Comments, page permissions, and Notion's own version history can't be exported by any tool using the official API.
- Images and file attachments currently appear as links that expire after ~1 hour (downloading them into the backup is on the roadmap).

---

Powered by [BackupRoost](https://github.com/smol-bytes/backuproost) — free and open source. To change the schedule, edit the `cron` line in [`.github/workflows/backup.yml`](.github/workflows/backup.yml).

# hello-pages-spike

Public GitHub Pages site. Nothing reaches the live site without going through the
promotion gate below.

## Structure

- **`main`** — working branch. Not deployed by Pages.
- **`prod`** — the only branch GitHub Pages deploys from. Protected: no direct pushes
  or force-pushes, only merges via a pull request that has passed the `content-check`
  CI workflow (`.github/workflows/content-check.yml`).
- Draft/local work happens in the separate private repo `hello-pages-spike-drafts`,
  never in this one, until it's ready to promote.

## Promotion flow

1. Finish and review content in `hello-pages-spike-drafts` (private, local-only).
2. Copy the finished files into a new branch of this repo, off `prod`.
3. Open a PR into `prod`.
4. `content-check` runs automatically: gitleaks generic secret scan, plus a denylist
   scan against `.githooks/denylist-generic.txt` (committed, generic patterns) and the
   `SENSITIVE_DENYLIST` repo secret (personal terms — never committed, only stored as a
   masked Actions secret).
5. Branch protection blocks merging until that check passes. Merge → Pages rebuilds
   from `prod` automatically.

## Local safety net

A `pre-push` git hook (`.githooks/pre-push`) blocks pushing anything (to any branch)
whose diff matches the same denylist patterns, before it ever leaves the machine. Install
it after cloning:

```
git config core.hooksPath .githooks
```

Then create `.githooks/denylist-local.txt` locally (gitignored) with any real names, org
identifiers, or account handles that shouldn't appear in outgoing pushes — this file is
never committed, so the hook mechanism itself never leaks what it's protecting.

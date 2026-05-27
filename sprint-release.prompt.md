---
description: "Create a new sprint release branch across all repos: switch to main, create release branch, patch pipeline trigger + nuget versions, commit and push."
argument-hint: "repos directory, BC version, year, week — e.g. 'c:\\Work\\repos BC27 2026 17'"
agent: "agent"
---

Create a new sprint release across all repositories in a given directory.

The arguments are: **repos directory** (the root folder containing the repository folders), **BC version** (e.g. 27), **year** (e.g. 2026), **ISO week number** (e.g. 17).  
Derive from those:
- Branch name: `release/bc{BC}-{YEAR}{WEEK}` (week zero-padded to 2 digits, e.g. `release/bc27-202617`)
- Commit message: `Release BC{BC} {YEAR}-{WEEK}` (e.g. `Release BC27 2026-17`)

Run the following steps **for every subdirectory under the specified repos directory that contains a `.git` folder**:

### Step 1 — Switch to main and pull latest
```
git checkout main
git fetch --prune
git pull
```

### Step 2 — Create the release branch
```
git checkout -b release/bc{BC}-{YEAR}{WEEK}
```

### Step 3 — Patch `azure-pipelines.yml` trigger
In the file `azure-pipelines.yml` at the repo root, replace `- main` with the new release branch under `trigger.branches.include` — the release branch should be the **only** entry, not alongside `main`:
```yaml
trigger:
  branches:
    include:
      - release/bc{BC}-{YEAR}{WEEK}
```
Only modify the file if `- main` is still present in the trigger block.

### Step 4 — Patch `nuget.json` dependency versions
In the file `nuget.json` at the repo root, for every dependency inside a type named **`App`** or **`Test`**, set `"version"` to `"-RLS"`.  
Leave all other types (`ExternalApps`, `BreakingCheck`, etc.) unchanged.  
Skip gracefully if the file has no `App` or `Test` types.

### Step 5 — Stage all changes and commit
```
git add -A
git commit -m "Release BC{BC} {YEAR}-{WEEK}"
```

### Step 6 — Push branch to remote
```
git push -u origin release/bc{BC}-{YEAR}{WEEK}
```

After completing all repos, verify:
1. Every repo is on branch `release/bc{BC}-{YEAR}{WEEK}` (`git branch --show-current`).
2. Every repo's last commit message is `Release BC{BC} {YEAR}-{WEEK}` (`git log -1 --oneline`).
3. The remote branch exists in each repo (`git branch -r`).

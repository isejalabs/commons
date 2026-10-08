# GitHub repository settings

Repo-level GitHub settings that should be consistent across isejalabs repos, following
the same "define once" idea as the rest of this repo.

[`settings.yml`](settings.yml) is the source of truth. A scheduled GitHub Action in
this repo (`.github/workflows/enforce-repo-settings.yml`) reads it and applies it to
every non-archived repo in the `isejalabs` org via the GitHub API.

## Convention: disable merge commits

Every isejalabs repo should disable "Allow merge commits" and merge PRs via squash
(and/or rebase) instead, to keep a linear, one-commit-per-PR history on `main`.

## How enforcement works

- `.github/workflows/enforce-repo-settings.yml` runs weekly (Monday 06:00 UTC) and on
  manual `workflow_dispatch`.
- It parses the `repository:` keys out of [`settings.yml`](settings.yml) and `PATCH`es
  every non-archived repo in the org with them via `gh api`, so the workflow never
  duplicates the actual values.
- It authenticates with the `ORG_SETTINGS_TOKEN` repo secret: a fine-grained personal
  access token scoped to "All repositories" in `isejalabs`, with **Administration:
  Read and write** permission. Add it under this repo's Settings → Secrets and
  variables → Actions → New repository secret.

**Setup required once**: create that token and add the secret — without it the
workflow fails loudly (401/403) rather than silently doing nothing.

To change an enforced setting, edit `settings.yml` and either wait for the next
scheduled run or trigger the workflow manually from the Actions tab.

### Applying by hand (before the token exists, or for a one-off)

- **UI**: repo Settings → General → *Pull Requests* → uncheck "Allow merge commits".
- **API**:
  ```sh
  gh api -X PATCH repos/isejalabs/<repo> -F allow_merge_commit=false \
    -F allow_squash_merge=true -F allow_rebase_merge=true
  ```

## Alternative considered: `github/safe-settings`

[`github/safe-settings`](https://github.com/github/safe-settings) is a GitHub App that
covers this and more — branch protection, labels, team permissions — read from a
central `.github` repo. Not used here: it requires hosting a standalone Probot app,
which is a lot of infrastructure for the handful of repo-level flags this org
currently needs. Revisit if that changes.

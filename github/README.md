# GitHub repository settings

Repo-level GitHub settings that should be consistent across isejalabs repos, following
the same "define once" idea as the rest of this repo — even though these aren't
consumed via a submodule/import or Renovate's `extends` like `agents/` or `renovate/`.
They're applied by hand today, and meant to be synced by a GitHub App reading a
central config once that's set up (see below).

## Convention: disable merge commits

Every isejalabs repo should disable "Allow merge commits" and merge PRs via squash
(and/or rebase) instead, to keep a linear, one-commit-per-PR history on `main`.
`isejalabs/terraform-proxmox-talos` already has this set (merge commits are rejected;
squash works).

**Today**, this is applied by hand, per repo:

- **UI**: repo Settings → General → *Pull Requests* → uncheck "Allow merge commits".
  Leave "Allow squash merging" checked (and "Allow rebase merging" too, if wanted).
- **API**:
  ```sh
  gh api -X PATCH repos/isejalabs/<repo> -f allow_merge_commit=false \
    -f allow_squash_merge=true -f allow_rebase_merge=true
  ```

**Eventually**, once a central `isejalabs/.github` repo exists running the
[`github/safe-settings`](https://github.com/github/safe-settings) GitHub App,
[`settings.yml`](settings.yml) in this directory is meant to be copied there (as that
repo's own `.github/settings.yml`) so every repo in the org picks up this default
automatically instead of being set by hand per repo.

**Not wired up to any automation yet** — there's no `isejalabs/.github` repo or
safe-settings installation today. Treat `settings.yml` as a ready-to-use reference to
drop in once that exists, not something currently enforced.

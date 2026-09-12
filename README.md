# commons

Shared conventions and configuration reused across [isejalabs](https://github.com/isejalabs) repositories, so they're defined once instead of hand-copied (and drifting) in every repo.

## Why this exists

Several repos converge on the same conventions that have nothing to do with their own code — how AI coding agents should collaborate (commit scopes, PR discipline, issue-linking), or shared tooling config like Renovate presets. Instead of pasting the same rules into each repo's own files, this repo is the single source of truth and consumers reference it.

## Structure

Organized by concern, one top-level directory each — not one repo per concern, unless something here eventually grows enough to need its own versioning, collaborators, or branch protection:

- **`agents/`** — shared AI-agent collaboration conventions (`AGENTS.common.md`). Consumed via a git submodule plus Claude Code's `@path` import syntax.
- **`renovate/`** — shared Renovate presets. Consumed via Renovate's native remote-extends syntax — no submodule needed.

## How consumers use this

### Agent conventions (submodule + import)

```sh
git submodule add https://github.com/isejalabs/commons .commons
```

Then, in the consuming repo's `AGENTS.md` (with `CLAUDE.md` symlinked to it, per convention):

```
@.commons/agents/AGENTS.common.md
```

Claude Code inlines the imported file at read time. Only genuinely shared rules live here — a repo's own architecture/domain-specific conventions stay directly in its own `AGENTS.md`.

Pull in updates with:

```sh
git submodule update --remote .commons
```

### Renovate presets (remote extends)

```jsonc
{
  "extends": ["github>isejalabs/commons//renovate/<preset-name>"]
}
```

Resolved directly by the Renovate service at run time — no submodule or local checkout required.

## What belongs here

- **Yes**: conventions genuinely identical across multiple repos — commit-message scope rules, PR discipline, cross-repo issue-linking, shared Renovate version-scheme presets.
- **No**: anything specific to one repo's own architecture or domain (e.g. homelab's Kubernetes/Terragrunt-specific rules, a single Terraform module's variable conventions) — that stays where it describes.

## Consumers

- [isejalabs/homelab](https://github.com/isejalabs/homelab)
- [isejalabs/terraform-modules](https://github.com/isejalabs/terraform-modules)
- [isejalabs/terraform-proxmox-talos](https://github.com/isejalabs/terraform-proxmox-talos)

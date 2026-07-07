# ccp-market

betmoar's Claude Code plugin marketplace — a **catalog only**. It holds no plugin
code; it's an index. Each plugin lives in its own repo and is referenced here, so
every plugin keeps an independent dev/release cycle.

## Contents

```
.claude-plugin/marketplace.json   # the catalog (the only thing that matters)
```

Each entry in `plugins[]` points at a separate plugin repo via its `source`. At
install time Claude Code clones that repo — this catalog never vendors or
submodules the plugin code.

## Install

```
/plugin marketplace add betmoar/ccp-market
/plugin install okf@betmoar
```

Install id is `<plugin>@<marketplace>`, where the marketplace name is `betmoar`
(set by `name` in `marketplace.json`).

### Requirements / known issue (HTTPS for install)

`claude plugin install` clones a `github` plugin source over **SSH only**
(`git@github.com:`), with no HTTPS fallback — unlike `marketplace add`, which
falls back to HTTPS. On a machine with no GitHub SSH key this fails with
`Permission denied (publickey)`, **even for a public repo**. Affects every
installer, not just one machine.

Fix it once with either:

- **Route SSH clones through HTTPS** (works with no SSH key; public repos need no
  auth):

  ```
  git config --global url."https://github.com/".insteadOf "git@github.com:"
  ```

  Rollback: `git config --global --unset url."https://github.com/".insteadOf`

- **Or configure a GitHub SSH key** if you'd rather use SSH.

Verified end-to-end: with the `insteadOf` rule, `install okf@betmoar` succeeds.
Tracked upstream at [anthropics/claude-code#69587](https://github.com/anthropics/claude-code/issues/69587)
(install should gain the same SSH→HTTPS fallback `marketplace add` already has).
Claude Code 2.1.183 / macOS at time of writing.

## Plugins

| Plugin      | Source repo                | Description                                    |
| ----------- | -------------------------- | ---------------------------------------------- |
| `cc-proxy`  | `betmoar/cc-proxy-plugin`  | Z.ai and OpenRouter proxy for Claude Code — stateless local router with manual `/model` switching and per-provider auth. |
| `okf`       | `betmoar/cc-okf-plugin`    | OKF knowledge-bundle skill and lifecycle commands. |
| `cc-repete` | `betmoar/cc-repete-plugin` | Self-evolving autonomous loops — a Stop-hook loop engine driving an agent toward a verifiable goal. |
| `cc-reload` | `betmoar/cc-reload-plugin` | Session continuity across context resets — snapshots a digest before clear/compact and auto-rehydrates after. Non-looped companion to cc-repete. |
| `cc-status` | `betmoar/cc-status-plugin` | Statusline framework — discovers per-plugin segment manifests, runs enabled ones, and joins them onto one bar. |
| `cc-agents` | `betmoar/cc-agents-plugin` | GLM-offload review agents — auto-convenes a multi-lens panel of parallel GLM reviewers on spec/plan writes, synthesized into one scored report. Requires cc-proxy. |
| `cc-unknowns` | `betmoar/cc-unknowns-plugin` | Single-skill harness for surfacing unknowns before, during, and after implementation — classifies each task across known/unknown quadrants. |

## Maintaining

**Add a plugin** — ensure its repo is a valid plugin (`.claude-plugin/plugin.json`
at root, or a subpath), then add one entry to `plugins[]`, commit, and push.
Consumers run `/plugin marketplace update betmoar`.

**Release a plugin update** — by default an entry tracks its repo's default-branch
latest commit. For reproducible installs, pin the `source` with a tag or commit:

```json
"source": { "source": "github", "repo": "betmoar/cc-okf-plugin", "ref": "v0.1.0" }
```

Then a release is: tag the plugin repo → bump the pin here → push the catalog.

## Validate

```
claude plugin validate . --strict
```

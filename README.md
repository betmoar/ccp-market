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

## Plugins

| Plugin | Source repo            | Description                                    |
| ------ | ---------------------- | ---------------------------------------------- |
| `okf`  | `betmoar/cc-okf-plugin`| OKF knowledge-bundle skill and lifecycle commands. |

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

<div align="center">

<img src="assets/logo.svg" width="112" height="112" alt="img2threejs logo" />

# img2threejs — Claude Code plugin

**A packaging fork of [hoainho/img2threejs](https://github.com/hoainho/img2threejs), installable as a Claude Code plugin and synced from upstream daily.**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Upstream](https://img.shields.io/badge/upstream-hoainho%2Fimg2threejs-black.svg)](https://github.com/hoainho/img2threejs)
[![Sync](https://github.com/limeflash/img2threejs/actions/workflows/sync-upstream.yml/badge.svg)](https://github.com/limeflash/img2threejs/actions/workflows/sync-upstream.yml)

![img2threejs demo — a reference loot-chest image reconstructed as a procedural Three.js model](assets/demo.gif)

</div>

Upstream ships a skill you install by cloning it into `~/.claude/skills/`. This fork adds a
plugin manifest and a marketplace entry, so Claude Code can install, update, enable, and
disable it like any other plugin — plus a scheduled job that merges upstream every day, so
the plugin keeps tracking the original.

> **All the actual functionality is upstream's.** For what the tool does, the sculpting
> pipeline, the gates, the rubrics, and the script reference, read the
> [upstream README](https://github.com/hoainho/img2threejs#readme) and browse the
> [live demo gallery](https://hoainho.github.io/img2threejs-showcase/). File pipeline bugs
> and feature requests there, not here — this repo only carries the packaging.

## Install

```bash
claude plugin marketplace add limeflash/img2threejs
claude plugin install img2threejs@img2threejs
```

Or from inside a session:

```
/plugin marketplace add limeflash/img2threejs
/plugin install img2threejs@img2threejs
```

Check it landed:

```bash
claude plugin details img2threejs@img2threejs
```

Expect `Skills (1)  img2threejs` in the component inventory.

**Requirement:** Python 3.10+ on `PATH`. The pipeline scripts are pure standard library —
nothing to `pip install`.

## Use

Attach or point to an object image, then:

```
/img2threejs Rebuild this object as a Three.js model, keep the proportions, angles, and colours.
```

Claude also fires it on its own when you hand it an object image and ask for a Three.js
model, a sculpt spec, or a reconstruction plan.

## Update

```bash
claude plugin marketplace update img2threejs
claude plugin update img2threejs@img2threejs
```

`plugin.json` deliberately omits `version`, so Claude Code falls back to the commit SHA and
counts every synced upstream commit as a new version. Add a `version` field and updates
freeze until you bump it by hand.

## Uninstall

```bash
claude plugin uninstall img2threejs@img2threejs
claude plugin marketplace remove img2threejs
```

## What this fork changes

| Path | Purpose |
| :--- | :--- |
| `.claude-plugin/plugin.json` | Plugin manifest. No `version` field — see [Update](#update) |
| `.claude-plugin/marketplace.json` | Single-entry marketplace, `source: "./"` |
| `.github/workflows/sync-upstream.yml` | Daily merge of `upstream/main` |
| `.gitattributes` | Keeps this README from conflicting on every sync |
| `README.md` | This file |

Everything else — `SKILL.md`, `forge/`, `grimoire/`, `docs/` — is upstream, untouched. The
root `SKILL.md` is loaded as the plugin's skill as-is, so no files were moved or rewritten.

## How the sync works

```
hoainho/img2threejs ──(GitHub Action, daily 04:17 UTC)──> this fork ──(plugin update)──> your machine
```

[`sync-upstream.yml`](.github/workflows/sync-upstream.yml) merges `upstream/main` into
`main`. Every file this fork adds sits at a path upstream never touches, so the merge stays
clean. `README.md` is the one genuine overlap, and `.gitattributes` marks it `merge=ours`
so upstream README edits are dropped here instead of conflicting. The cost of that: upstream
README improvements never reach this file — read the upstream README directly.

If upstream ever claims one of the other paths, the job fails with a conflict and GitHub
emails the repo owner. That is deliberate — a loud failure beats silently discarding either
side. Resolve it by hand, then re-run the workflow.

Manual sync: the
[Actions tab](https://github.com/limeflash/img2threejs/actions/workflows/sync-upstream.yml),
or `gh workflow run sync-upstream.yml -R limeflash/img2threejs`.

> GitHub pauses scheduled workflows after 60 days without commits. If upstream goes quiet
> that long the cron stops — but there is nothing to sync at that point, and any manual
> dispatch wakes it back up.

## Contributing

Pipeline contributions belong upstream: see
[hoainho/img2threejs/CONTRIBUTING.md](https://github.com/hoainho/img2threejs/blob/main/CONTRIBUTING.md).
Issues here should be about the plugin packaging or the sync job.

## License

MIT, inherited from upstream. See [LICENSE](LICENSE).

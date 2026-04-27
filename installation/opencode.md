# OpenCode

Install + update guide. Two scopes: **global** (all your projects) or **per-project** (only this repo).

## Pick scope

When asked, choose one:

- `1` Global — `~/.config/opencode/skills/skill-master`. Available everywhere.
- `2` Per-project — `<project>/.opencode/skills/skill-master`. Only inside this repo.
- `3` Both — global symlink + project symlink.

If unsure, ask the user which one.

## Repository

Clone once. Both scopes reuse the same checkout:

```bash
git clone https://github.com/samuelgja/skill-master.git ~/.config/opencode/skill-master
```

## Global install

```bash
mkdir -p ~/.config/opencode/skills
ln -sfn ~/.config/opencode/skill-master/skills ~/.config/opencode/skills/skill-master
```

Restart OpenCode.

## Per-project install

Run from the project root:

```bash
mkdir -p .opencode/skills
ln -sfn ~/.config/opencode/skill-master/skills .opencode/skills/skill-master
```

Decide whether to commit:

- Commit `.opencode/skills/` if the team should share these skills.
- Add `.opencode/skills/` to `.gitignore` for local-only use.

Restart OpenCode (or open a new session in the project).

## Verify

Global:

```bash
ls -la ~/.config/opencode/skills/skill-master
```

Per-project:

```bash
ls -la .opencode/skills/skill-master
```

Optional in OpenCode session:

```text
use skill tool to list skills
```

You should see `create-research` and `research` available.

## Update

```bash
cd ~/.config/opencode/skill-master && git pull
```

Symlinks resolve automatically. Re-link only if scopes changed.

## Uninstall

Global: `rm ~/.config/opencode/skills/skill-master`
Per-project: `rm .opencode/skills/skill-master`
Optional cleanup of clone: `rm -rf ~/.config/opencode/skill-master`

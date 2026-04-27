# Codex

Install + update guide. Two scopes: **global** (all your projects) or **per-project** (only this repo).

## Pick scope

When asked, choose one:

- `1` Global — `~/.agents/skills/skill-master`. Available everywhere.
- `2` Per-project — `<project>/.agents/skills/skill-master`. Only inside this repo.
- `3` Both — global symlink + project symlink.

If unsure, ask the user which one.

## Repository

Clone once. Both scopes reuse the same checkout:

```bash
git clone https://github.com/samuelgja/skill-master.git ~/.codex/skill-master
```

## Global install

```bash
mkdir -p ~/.agents/skills
ln -sfn ~/.codex/skill-master/skills ~/.agents/skills/skill-master
```

Restart Codex.

### Legacy path (older Codex setups)

```bash
mkdir -p ~/.codex/skills
ln -sfn ~/.codex/skill-master/skills ~/.codex/skills/skill-master
```

## Per-project install

Run from the project root:

```bash
mkdir -p .agents/skills
ln -sfn ~/.codex/skill-master/skills .agents/skills/skill-master
```

Decide whether to commit:

- Commit `.agents/skills/` if the team should share these skills.
- Add `.agents/skills/` to `.gitignore` for local-only use.

Restart Codex (or open a new session in the project).

## Verify

Global:

```bash
ls -la ~/.agents/skills/skill-master
```

Per-project:

```bash
ls -la .agents/skills/skill-master
```

You should see a symlink pointing to `~/.codex/skill-master/skills`.

## Update

```bash
cd ~/.codex/skill-master && git pull
```

Symlinks resolve automatically. Re-link only if scopes changed.

## Uninstall

Global: `rm ~/.agents/skills/skill-master`
Per-project: `rm .agents/skills/skill-master`
Optional cleanup of clone: `rm -rf ~/.codex/skill-master`

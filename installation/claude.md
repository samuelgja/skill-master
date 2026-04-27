# Claude Code

Install + update guide. Two scopes: **global** (all your projects) or **per-project** (only this repo).

## Pick scope

When asked, choose one:

- `1` Global — installs to `~/.claude/skills`. Available everywhere.
- `2` Per-project — installs to `<project>/.claude/skills`. Only inside this repo. Survives `git clone` if you commit it (or stays local if you `.gitignore` it).
- `3` Both — global symlink + project copy.

If unsure, ask the user which one.

## Repository

Clone once. Both scopes reuse the same checkout:

```bash
git clone https://github.com/samuelgja/skill-master.git ~/skill-master
```

## Global install

```bash
mkdir -p ~/.claude/skills
for skill_dir in ~/skill-master/skills/*; do
  [ -d "$skill_dir" ] || continue
  ln -sfn "$skill_dir" ~/.claude/skills/"$(basename "$skill_dir")"
done
```

Restart Claude Code.

## Per-project install

Run from the project root:

```bash
mkdir -p .claude/skills
for skill_dir in ~/skill-master/skills/*; do
  [ -d "$skill_dir" ] || continue
  ln -sfn "$skill_dir" .claude/skills/"$(basename "$skill_dir")"
done
```

Decide whether to commit:

- Commit `.claude/skills/` if the team should share these skills.
- Add `.claude/skills/` to `.gitignore` if local-only.

Restart Claude Code (or open a new session in the project).

## Verify

Global:

```bash
ls -la ~/.claude/skills
```

Per-project:

```bash
ls -la .claude/skills
```

Each entry should be a symlink (or directory) containing `SKILL.md`.

## Notes

- Claude discovers skills from `~/.claude/skills` (personal) and `<project>/.claude/skills` (project).
- Project scope takes precedence on name conflicts.
- Each skill must live at `<scope>/skills/<skill-name>/SKILL.md`.

## Update

Pull the repo; symlinks pick up new content automatically. Re-link only if new skills were added or removed:

Global:

```bash
cd ~/skill-master && git pull
for skill_dir in ~/skill-master/skills/*; do
  [ -d "$skill_dir" ] || continue
  ln -sfn "$skill_dir" ~/.claude/skills/"$(basename "$skill_dir")"
done
```

Per-project (run from project root):

```bash
cd ~/skill-master && git pull
cd - >/dev/null
mkdir -p .claude/skills
for skill_dir in ~/skill-master/skills/*; do
  [ -d "$skill_dir" ] || continue
  ln -sfn "$skill_dir" .claude/skills/"$(basename "$skill_dir")"
done
```

## Uninstall

Global: `rm ~/.claude/skills/create-research ~/.claude/skills/research`
Per-project: `rm .claude/skills/create-research .claude/skills/research`

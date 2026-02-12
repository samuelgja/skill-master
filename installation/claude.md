# Claude Code

These steps install and update this repository's skills for Claude Code.

## Install

Repository:

```bash
git clone https://github.com/samuelgja/skill-master.git ~/skill-master
```

1. Create the Claude skills directory:

```bash
mkdir -p ~/.claude/skills
```

2. Link each skill folder from this repo:

```bash
for skill_dir in ~/skill-master/skills/*; do
  [ -d "$skill_dir" ] || continue
  ln -sfn "$skill_dir" ~/.claude/skills/"$(basename "$skill_dir")"
done
```

3. Start a new Claude Code session (or restart current session) to pick up new skills.

## Verify

```bash
ls -la ~/.claude/skills
```

Each installed skill should appear as a directory/symlink containing `SKILL.md`.

Start a new Claude Code session and ask for a skill-triggering task (example: "help me plan this feature" or "let's debug this issue"). The matching skill should trigger automatically.

## Notes

- Claude discovers skills from `~/.claude/skills` (personal) and `.claude/skills` (project).
- Each skill must be in `~/.claude/skills/<skill-name>/SKILL.md`.

## Update

```bash
cd ~/skill-master && git pull
for skill_dir in ~/skill-master/skills/*; do
  [ -d "$skill_dir" ] || continue
  ln -sfn "$skill_dir" ~/.claude/skills/"$(basename "$skill_dir")"
done
```

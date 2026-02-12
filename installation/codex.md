# Codex

These steps install and update this repository's skills for Codex.

## Install

Repository:

```bash
git clone https://github.com/samuelgja/skill-master.git ~/.codex/skill-master
```

1. Create the user skills directory:

```bash
mkdir -p ~/.agents/skills
```

2. Symlink this repo's `skills/` folder into Codex skill discovery:

```bash
ln -s ~/.codex/skill-master/skills ~/.agents/skills/skill-master
```

3. Restart Codex (quit and relaunch) so it discovers the skills.

## Verify

```bash
ls -la ~/.agents/skills/skill-master
```

You should see a symlink pointing to `~/.codex/skill-master/skills`.

## Compatibility Note (older setups)

If your Codex environment is configured for legacy user path discovery, use:

```bash
mkdir -p ~/.codex/skills
ln -s ~/.codex/skill-master/skills ~/.codex/skills/skill-master
```

## Update

```bash
cd ~/.codex/skill-master && git pull
```

## Uninstall

```bash
rm ~/.agents/skills/skill-master
```

Optional:

```bash
rm -rf ~/.codex/skill-master
```

# OpenCode

These steps install and update this repository's skills for OpenCode.

## Install

Repository:

```bash
git clone https://github.com/samuelgja/skill-master.git ~/.config/opencode/skill-master
```

1. Create the OpenCode skills directory:

```bash
mkdir -p ~/.config/opencode/skills
```

2. Symlink this repo's `skills/` folder into OpenCode skill discovery:

```bash
ln -sfn ~/.config/opencode/skill-master/skills ~/.config/opencode/skills/skill-master
```

3. Restart OpenCode so it reloads discovered skills.

## Verify

```bash
ls -la ~/.config/opencode/skills/skill-master
```

Optional in OpenCode session:

```text
use skill tool to list skills
```

You should see this repository's skills available.

## Update

```bash
cd ~/.config/opencode/skill-master && git pull
```

## Uninstall

```bash
rm ~/.config/opencode/skills/skill-master
```

Optional:

```bash
rm -rf ~/.config/opencode/skill-master
```

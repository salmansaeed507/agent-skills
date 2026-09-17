# agent-skills

Personal Cursor agent skills, synced via git for use on any machine.

## Skills

| Skill | Description |
|-------|-------------|
| [ai-coding](./ai-coding/) | Plan-first workflow for building, fixing, or shipping one feature/change at a time |
| [document-a-feature](./document-a-feature/) | Creates self-contained Markdown flow docs with an HTML nested unit diagram, file list, and where-else notes |
| [simple-fix](./simple-fix/) | Fix exactly one bug per run with minimal diff and project-script verification |

## Install on a new machine

Clone this repo, then link or copy the skills into your agent skills directory.

### Option A — Symlink (recommended)

Keeps skills in sync when you `git pull`:

```bash
git clone git@github.com:salmansaeed507/agent-skills.git ~/agent-skills

mkdir -p ~/.agents/skills
ln -sf ~/agent-skills/ai-coding ~/.agents/skills/ai-coding
ln -sf ~/agent-skills/document-a-feature ~/.agents/skills/document-a-feature
ln -sf ~/agent-skills/simple-fix ~/.agents/skills/simple-fix
```

For Cursor's personal skills path instead:

```bash
mkdir -p ~/.cursor/skills
ln -sf ~/agent-skills/ai-coding ~/.cursor/skills/ai-coding
ln -sf ~/agent-skills/document-a-feature ~/.cursor/skills/document-a-feature
ln -sf ~/agent-skills/simple-fix ~/.cursor/skills/simple-fix
```

### Option B — Copy

```bash
git clone git@github.com:salmansaeed507/agent-skills.git ~/agent-skills
cp -r ~/agent-skills/ai-coding ~/.agents/skills/
cp -r ~/agent-skills/document-a-feature ~/.agents/skills/
cp -r ~/agent-skills/simple-fix ~/.agents/skills/
```

### Option C — Skills CLI

If you use [skills.sh](https://skills.sh/):

```bash
npx skills add salmansaeed507/agent-skills@ai-coding -g -y
npx skills add salmansaeed507/agent-skills@document-a-feature -g -y
npx skills add salmansaeed507/agent-skills@simple-fix -g -y
```

## Update

```bash
cd ~/agent-skills && git pull
```

If you used symlinks, no extra step is needed. If you copied, re-run the copy commands.

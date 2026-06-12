# Installing the skill

The skill content lives in the `skills/` folder of this repository. To install it, copy that folder's **content** into a directory named `xunit-v3-migration` under a Claude Code skills location. The directory name matters — it becomes the skill (and slash-command) name.

## Project-level install (one repository)

Makes the skill available to anyone working in that repository (commit it with the repo):

```bash
mkdir -p <your-repo>/.claude/skills/xunit-v3-migration
cp -r <this-repo>/skills/. <your-repo>/.claude/skills/xunit-v3-migration/
```

## Global install (all your projects)

Makes the skill available in every Claude Code session for your user:

```bash
mkdir -p ~/.claude/skills/xunit-v3-migration
cp -r <this-repo>/skills/. ~/.claude/skills/xunit-v3-migration/
```

On Windows (PowerShell, native Claude Code outside WSL):

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills\xunit-v3-migration"
Copy-Item -Recurse -Force "<this-repo>\skills\*" "$env:USERPROFILE\.claude\skills\xunit-v3-migration\"
```

> Prefer copying over symlinking. Symlinks across the Windows/WSL boundary (`/mnt/...` ↔ `C:\...`) are unreliable, and a copy pins the version you tested.

## Verify the installation

1. Start (or restart) a Claude Code session in the target project.
2. Type `/xunit-v3-migration` — the skill should appear in the slash-command list.
3. Or just ask: *"migrate this solution to xUnit v3"* — the skill should trigger automatically.

## Updating

Re-run the same copy command after pulling new changes in this repository. If you develop changes while a project copy is installed, remember the project copy is a snapshot — sync improvements back to this repo (see [CLAUDE.md](CLAUDE.md)).

## Uninstalling

Delete the installed folder:

```bash
rm -rf <your-repo>/.claude/skills/xunit-v3-migration   # project
rm -rf ~/.claude/skills/xunit-v3-migration             # global
```
